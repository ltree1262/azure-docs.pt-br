---
title: Configurar um dispositivo de migrações para Azure para VMware
description: Saiba como configurar um dispositivo de migrações para Azure para avaliar e migrar VMs VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538249"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurar um dispositivo para VMs VMware

Siga este artigo para configurar o dispositivo migrações para Azure para avaliação com a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) e para a migração sem agente usando a ferramenta [migrações para Azure: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) .

O [dispositivo de migrações para Azure](migrate-appliance.md) é um dispositivo leve usado pelas migrações para Azure: avaliação de servidor e migração de servidor para descobrir VMs VMware locais, enviar dados de metadados/desempenho de VM para o Azure e para replicação de VMs VMware durante a migração sem agente.

Você pode implantar o dispositivo usando dois métodos:

- Configure em uma VM VMware usando um modelo OVA baixado. Esse é o método descrito neste artigo.
- Configure em uma VM VMware ou máquina física com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deve ser usado se você não puder configurar uma VM usando um modelo ova ou se você estiver no Azure governamental.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.


## <a name="appliance-deployment-ova"></a>Implantação de dispositivo (OVA)

Para configurar o dispositivo usando um modelo OVA, você:
- Baixe um arquivo de modelo OVA e importe-o para o vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à Avaliação de Servidor das Migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de Migrações para Azure.

## <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2. Em **descobrir computadores** > **são seus computadores virtualizados?**, clique em **Sim, com o hipervisor do VMware vSphere**.
3. Clique em **Baixar** para baixar o arquivo de modelo .OVA.

  ![Seleções para baixar um arquivo OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o comando a seguir para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas [configurações](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. No console do cliente do vSphere, clique em **arquivo** > **implantar o modelo OVF**.
![Comando de menu para implantar um modelo de OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
6. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
8. Em **mapeamento de rede**, especifique a rede à qual a VM será conectada. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
9. Revise e confirme as configurações e clique em **Concluir**.


## <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar a URLs do Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .


## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez. Se você implantar o dispositivo usando um script em vez de um modelo OVA, as duas primeiras etapas do procedimento não serão aplicáveis.

1. No console do cliente do vSphere, clique com botão direito do mouse na VM > **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais caso o proxy exija autenticação.
        - Há suporte apenas para o proxy HTTP.
    - **Sincronização do horário**: o horário é verificado. O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: migrações para Azure verifica se as atualizações mais recentes do dispositivo estão instaladas.
    - **Instalar o VDDK**: migrações para Azure verifica se o VDDK (Kit de desenvolvimento de disco virtual) do VMware vSphere está instalado.
        - As migrações para Azure usam o VDDK para replicar máquinas durante a migração para o Azure.
        - Baixe o VDDK 6.7 da VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Clique em **Fazer Logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e sua senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
2. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado. Em seguida, selecione o projeto.
3. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **Registrar**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Iniciar a descoberta contínua fornecendo vCenter Server e credencial de VM

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em **Especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Você pode manter a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escutará.
2. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta somente leitura que o dispositivo usará para descobrir VMs no vCenter Server. Você pode definir o escopo da descoberta limitando o acesso à conta do vCenter. [Saiba mais](set-discovery-scope.md).
3. Clique em **Validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais de VM
Para a descoberta de aplicativos, funções e recursos e visualização de dependências das VMs, você pode fornecer uma credencial de VM que tenha acesso às VMs VMware. Você pode adicionar uma credencial para VMs do Windows e uma credencial para VMs do Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) sobre os privilégios de acesso necessários.

> [!NOTE]
> Essa entrada é opcional e é necessária para habilitar a descoberta de aplicativos e a visualização de dependência sem agente.

1. Em **Descobrir aplicativos e dependências em VMs**, clique em **Adicionar credenciais**.
2. Selecione o **Sistema operacional**.
3. Informar um nome amigável para a credencial.
4. Em **Nome de usuário** e **Senha**, especifique uma conta que tenha pelo menos acesso de convidado nas VMs.
5. Clique em **Adicionar**.

Depois de especificar o vCenter Server e as credenciais da VM (opcional), clique em **Salvar e iniciar a descoberta** para iniciar a descoberta do ambiente local.

São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal. A descoberta de aplicativos, funções e recursos instalados leva algum tempo, a duração depende do número de VMs que estão sendo descobertas. Para as VMs 500, leva aproximadamente 1 hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Examine os tutoriais para [avaliação do VMware](tutorial-assess-vmware.md) e [migração sem agente](tutorial-migrate-vmware.md).
