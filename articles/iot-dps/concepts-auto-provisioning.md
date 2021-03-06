---
title: Serviço de Provisionamento de Dispositivos no Hub IoT – conceitos de provisionamento automático
description: Este artigo fornece uma visão geral conceitual das fases do provisionamento automático do dispositivo, usando o DPS (serviço de provisionamento de dispositivos IoT), o Hub IoT e os SDKs do cliente.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975322"
---
# <a name="auto-provisioning-concepts"></a>Conceitos de provisionamento automático

Conforme descrito na [Visão geral](about-iot-dps.md), o Serviço de Provisionamento de Dispositivo é um serviço auxiliar que permite o provisionamento just-in-time de dispositivos para um hub IoT, sem a necessidade de intervenção humana. Após a configuração bem-sucedida, os dispositivos conectam-se diretamente ao seu Hub IoT designado. Esse processo é conhecido como o provisionamento automático e fornece uma experiência de registro pronto e configuração inicial para dispositivos.

## <a name="overview"></a>Visão geral

O provisionamento automático do Azure IoT pode ser dividido em três fases:

1. **Configuração do serviço** – uma única configuração das instâncias do Hub IoT do Azure e o Serviço de Provisionamento de Dispositivos no Hub IoT, estabelecendo-os e criando vínculos entre eles.

   > [!NOTE]
   > Independentemente do tamanho da sua solução de IoT, mesmo se você planejar oferecer suporte a milhões de dispositivos, essa será uma **configuração única**.

2. **Registro de dispositivo** – o processo de tornar a instância do Serviço de Provisionamento de Dispositivos ciente dos dispositivos que tentarão se registrar no futuro. O [registro](concepts-service.md#enrollment) é realizado com a configuração de informações de identidade do dispositivo no serviço de provisionamento, como um "registro individual" para um único dispositivo ou um "registro de grupo" para vários dispositivos. A identidade baseia-se no [mecanismo de atestado](concepts-security.md#attestation-mechanism) que o dispositivo foi projetado para usar, que permite que o serviço de provisionamento confirme a autenticidade do dispositivo durante o registro:

   - **TPM**: configurado como um "registro individual", a identidade do dispositivo baseia-se na ID de registro de TPM e a chave de endosso público. Considerando que TPM é uma [especificação](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), o serviço só espera confirmar a especificação, independentemente da implementação do TPM (hardware ou software). Consulte [Provisionamento de dispositivo: atestado de identidade com TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) para obter detalhes sobre atestado baseado em TPM. 

   - **X509**: configurado como um "registro individual" ou "registro do grupo", a identidade do dispositivo está baseada em um certificado digital X.509, que é carregado para o registro como um arquivo .pem ou .cer.

   > [!IMPORTANT]  
   > Embora não seja um pré-requisito para usar os Serviços de Provisionamento de Dispositivos, é altamente recomendável que seu dispositivo use um Módulo de Segurança de Hardware (HSM) para armazenar informações confidenciais de identidade do dispositivo, como chaves e certificados X.509.

3. **Registro de dispositivos e configuração** – iniciadas após a inicialização pelo software do registro, que é criado usando um SDK de cliente do Serviço de Provisionamento de Dispositivos apropriado para o mecanismo de dispositivo e atestado. O software estabelece uma conexão para o serviço de provisionamento para a autenticação do dispositivo e o registro subsequente no Hub IoT. Após o registro bem-sucedido, o dispositivo é fornecido com as informações de conexão e ID de dispositivo exclusiva do Hub IoT, permitindo que ele receba sua configuração inicial e comece o processo de telemetria. Em ambientes de produção, essa fase pode ocorrer semanas ou meses depois das duas etapas anteriores.

## <a name="roles-and-operations"></a>Funções e operações

As fases discutidas na seção anterior podem abranger semanas ou meses, devido a realidades de produção, como tempo de fabricação, envio, processo alfandegário etc. Além disso, eles podem abranger atividades em várias funções dadas as várias entidades envolvidas. Esta seção faz uma análise mais profunda sobre as várias funções e operações relacionadas a cada fase e ilustra o fluxo em um diagrama de sequência. 

O provisionamento automático também coloca requisitos no fabricante do dispositivo específicos para habilitar o mecanismo de atestado. As operações de manufatura também podem ocorrer independentemente do cronograma das fases de provisionamento automático, especialmente em casos em que os novos dispositivos são obtidos depois que o provisionamento automático já tiver sido estabelecido.

Uma série de tutoriais são fornecidos no sumário à esquerda para ajudar a explicar o provisionamento automático através da experiência prática. Para facilitar/simplificar o processo de aprendizagem, o software é usado para simular um dispositivo físico para a inscrição e o registro. Alguns tutoriais exigem que você realize operações para várias funções, inclusive operações para funções inexistentes, devido à natureza simulada dos tutoriais.

| Função | Operação | Descrição |
|------| --------- | ------------|
| Fabricante | Codificação de identidade e URL de registro | Com base no mecanismo de atestado usado, o fabricante é responsável por codificar as informações de identidade do dispositivo e a URL de registro do Serviço de Provisionamento de Dispositivos.<br><br>**Início rápido**: como o dispositivo é simulado, não há nenhuma função de fabricante. Consulte a função de Desenvolvedor para obter detalhes sobre como você pode obter essas informações, que são usadas para codificar um aplicativo de registro de exemplo. |
| | Forneça a identidade do dispositivo | Como a origem das informações de identidade do dispositivo, o fabricante é responsável por comunicá-las para o operador (ou um agente designado) ou registrá-lo diretamente para o Serviço de Provisionamento de Dispositivos por meio de APIs.<br><br>**Início rápido**: como o dispositivo é simulado, não há nenhuma função de fabricante. Consulte a função de operador para obter detalhes sobre como você pode obter a identidade do dispositivo, que é usada para inscrever um dispositivo simulado em sua instância de Serviço de Provisionamento de Dispositivos. |
| Operador | Configurar o provisionamento automático | Esta operação corresponde à primeira fase do provisionamento automático.<br><br>**Início rápido**: execute a função de operador, configurando o Serviço de Provisionamento de Dispositivos e instâncias de Hub IoT em sua assinatura do Azure. |
|  | Inscrever a identidade do dispositivo | Esta operação corresponde à segunda fase do provisionamento automático.<br><br>**Início rápido**: execute a função de operador, registrando seu dispositivo simulado em sua instância de Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pelo método de atestado que está sendo simulado no Início Rápido (TPM ou X.509). Veja a função Desenvolvedor para obter detalhes do atestado. |
| Serviço de provisionamento de dispositivo,<br>Hub IoT | \<todas as operações\> | Para uma implementação de produção com dispositivos físicos e guias de início rápido com dispositivos simulados, essas funções são realizadas por meio dos serviços de IoT que você configura na sua assinatura do Azure. As funções ou operações funcionam exatamente da mesma maneira, assim como os serviços IoT são indiferentes para o provisionamento de dispositivos físicos versus simulados. |
| Desenvolvedor | Compilar/implantar o software de registro | Esta operação corresponde à terceira fase do provisionamento automático. O Desenvolvedor é responsável por criar e implantar o software de registro no dispositivo usando o SDK adequado.<br><br>**Início rápido**: o aplicativo de registro de exemplo que você criar simula um dispositivo real, para a plataforma/linguagem de sua escolha, que é executado em sua estação de trabalho (em vez de implantá-lo em um dispositivo físico). O aplicativo de registro executa as mesmas operações que foram um implantadas em um dispositivo físico. Você especifica o método de atestado (certificado X.509 ou TPM), a URL de registro e o "Escopo da ID" da sua instância do Serviço de Provisionamento de Dispositivos. A identidade do dispositivo é determinada pela lógica de atestado de SDK em runtime, com base no método que você especificar: <ul><li>**Atestado de TPM** – sua estação de trabalho de desenvolvimento executa um [aplicativo de simulador de TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Quando em execução, um aplicativo separado é usado para extrair a "Chave de endosso" e a "ID do Registro" do TPM para uso no registro da identidade do dispositivo. A lógica de atestado do SDK também usa o simulador durante o registro, para apresentar um token de SAS assinado para autenticação e verificação de registro.</li><li>**Atestado X509** – use uma ferramenta para [gerar um certificado](how-to-use-sdk-tools.md#x509-certificate-generator). Uma vez gerado, crie o arquivo de certificado necessário para usar no registro. A lógica de atestado do SDK também usa o certificado durante o registro, para apresentar para autenticação e verificação de registro.</li></ul> |
| Dispositivo | Inicializar e registrar | Esta operação corresponde à terceira fase do provisionamento automático realizada pelo software de registro de dispositivo criado pelo Desenvolvedor. Consulte a função Desenvolvedor para obter detalhes. Após a primeira inicialização: <ol><li>O aplicativo conecta-se com a instância do Serviço de Provisionamento de Dispositivos de acordo com a URL global e a "ID do escopo" do serviço especificado durante o desenvolvimento.</li><li>Uma vez conectado, o dispositivo é autenticado usando o método de atestado e a identidade especificada durante o registro.</li><li>Uma vez autenticado, o dispositivo é registrado com a instância do Hub IoT especificada pela instância do serviço de provisionamento.</li><li>Após o registro, uma ID de dispositivo exclusiva e o ponto de extremidade do Hub IoT são retornados para o aplicativo de registro para se comunicar com o Hub IoT.</li><li> A partir daí, o dispositivo pode extrair seu estado de [dispositivo gêmeo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) inicial para configuração e iniciar o processo de reportar dados telemétricos.</li></ol>**Início rápido**: como o dispositivo é simulado, o software de registro é executado em sua estação de trabalho de desenvolvimento.|

O diagrama a seguir resume as funções e o sequenciamento das operações durante o provisionamento automático do dispositivo:
<br><br>
[![Sequência de provisionamento automático para um dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Opcionalmente, o fabricante também pode executar a operação "Registrar a identidade do dispositivo" usando as APIs do Serviço de Provisionamento de Dispositivos (em vez de por meio do operador). Para obter uma discussão detalhada sobre esse sequenciamento e muito mais, veja o [Registro sem toque de dispositivos com vídeo IoT do Azure](https://youtu.be/cSbDRNg72cU?t=2460) (começando no marcador 41:00)

## <a name="roles-and-azure-accounts"></a>Funções e contas do Azure

Como cada função é mapeada para uma conta do Azure depende do cenário e há alguns cenários que podem ser complicados. Os padrões comuns abaixo devem ajudar a fornecer uma compreensão geral sobre como as funções geralmente são mapeadas em uma conta do Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>O fabricante do chip fornece serviços de segurança

Nesse cenário, o fabricante gerencia a segurança para clientes de nível um. Esse cenário pode ser preferido por esses clientes de nível um, uma vez que eles não têm de gerenciar a segurança detalhada. 

O fabricante introduz a segurança em módulos de segurança de hardware (HSMs). Essa segurança pode incluir o fabricante obtendo chaves, certificados etc. de clientes em potencial que já têm instâncias DPS e configuração de grupos de inscrição. O fabricante também pode gerar essas informações de segurança para seus clientes.

Nesse cenário, pode haver duas contas do Azure envolvidas:

- **#1 de conta**: provavelmente compartilhado entre as funções de operador e de desenvolvedor em algum grau. Essa parte pode adquirir os chips do HSM do fabricante. Esses chips foram apontados para instâncias DPS associadas à Conta n º 1. Com registros de DPS, essa parte pode conceder a dispositivos para vários clientes de nível dois reconfigurando as configurações de registro de dispositivo no DPS. Essa parte também pode ter hubs IoT alocados para que os sistemas de back-end do usuário final se desorganizem para acessar a telemetria do dispositivo etc. Neste último caso, uma segunda conta pode não ser necessária.

- **Conta #2**: usuários finais, os clientes de nível dois podem ter seus próprios hubs IOT. Parte associada com a Conta N. 1 apenas aponta dispositivos concedidos para o hub correto nessa conta. Essa configuração requer a vinculação de hubs de IoT DPS entre contas do Azure, que podem ser feitas com modelos do Azure Resource Manager.

#### <a name="all-in-one-oem"></a>OEM todos-em-um

O fabricante pode ser um “OEM todos-em-um” em que apenas uma conta de fabricante única seria necessária. O fabricante lida com a segurança e provisionamento ponta a ponta.

O fabricante pode fornecer um aplicativo baseado em nuvem para os clientes que compram dispositivos. Este aplicativo seria a interface com o Hub IoT alocado pelo fabricante.

Máquinas de vendas ou máquinas de café automatizadas representam exemplos para este cenário.




## <a name="next-steps"></a>Próximas etapas

Pode ser útil marcar este artigo como um ponto de referência, enquanto você analisa o Início Rápido de provisionamento automático correspondente. 

Comece concluindo o Início Rápido "Configurar o provisionamento automático" que melhor se adapte a sua preferência de ferramenta de gerenciamento durante a fase de "Configuração do serviço":

- [Configurar provisionamento automático usando CLI do Azure](quick-setup-auto-provision-cli.md)
- [Configurar o provisionamento automático usando o portal do Azure](quick-setup-auto-provision.md)
- [Configurar o provisionamento automático usando um modelo do Resource Manager](quick-setup-auto-provision-rm.md)

Em seguida, continue com um Início Rápido "Provisionamento automático de um dispositivo simulado" que atenda às seu mecanismo de atestado do dispositivo e a preferência de idioma/SDK do Serviço de Provisionamento de Dispositivos. Neste Início Rápido, você examina as fases "Registro do dispositivo" e "Registro e configuração do dispositivo": 

|  | Mecanismo de atestado do dispositivo simulado | SDK/idioma do Início Rápido |  |
|--|--|--|--|
|  | TPM (Trusted Platform Module) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certificado X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




