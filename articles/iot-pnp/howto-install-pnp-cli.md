---
title: Usar a extensão do Azure IoT para CLI do Azure para interagir com dispositivos de visualização de IoT Plug and Play | Microsoft Docs
description: Instale a extensão do Azure IoT para CLI do Azure e use-a para interagir com os dispositivos IoT Plug and Play conectados ao meu Hub IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770451"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalar e usar a extensão do Azure IoT para o CLI do Azure

[O CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como o Hub IOT. O CLI do Azure está disponível no Windows, Linux e MacOS. O CLI do Azure também é pré-instalado no [Azure cloud Shell](https://shell.azure.com). O CLI do Azure permite que você gerencie recursos do Hub IoT do Azure, instâncias do serviço de provisionamento de dispositivos e hubs vinculados sem instalar nenhuma extensão.

A extensão do Azure IoT para o CLI do Azure é uma ferramenta de linha de comando para interagir com e testar dispositivos de visualização de IoT Plug and Play. Você pode usar a extensão para:

- Conecte-se a um dispositivo.
- Exiba a telemetria que o dispositivo envia.
- Trabalhar com as propriedades do dispositivo.
- Chame os comandos do dispositivo.

Este artigo mostra como:

- Instale e configure a extensão do Azure IoT para o CLI do Azure.
- Use a extensão para interagir com e testar seus dispositivos.
- Use a extensão para gerenciar interfaces no repositório de modelos.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalar a extensão do Azure IoT para o CLI do Azure

### <a name="step-1---install-the-azure-cli"></a>Etapa 1-instalar o CLI do Azure

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar o CLI do Azure em seu ambiente. Para usar todos os comandos abaixo, sua versão de CLI do Azure deve ser a versão 2.0.73 ou superior. Use `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Etapa 2 – instalar a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-iot`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-iot` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Usar a extensão do Azure IoT para o CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para entrar em sua assinatura do Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se você estiver usando o Azure cloud Shell, você estará automaticamente conectado e não precisará executar o comando anterior.

Para usar a extensão de IoT do Azure para o CLI do Azure, você precisa de:

- Um Hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [criar um hub IOT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do Hub IoT para executar os comandos de extensão do Azure IoT. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registrado em seu hub IoT. Você pode usar o seguinte comando CLI do Azure para registrar um dispositivo, certifique-se de substituir `{YourIoTHubName}` os `{YourDeviceID}` espaços reservados e pelos seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alguns comandos precisam da cadeia de conexão para um repositório de modelos da empresa. Um repositório de modelos para sua empresa é criado quando você se integra [ao portal do Azure Certified para IOT](howto-onboard-portal.md)pela primeira vez. Um terceiro pode compartilhar sua cadeia de conexão de repositório de modelos com você para fornecer acesso às interfaces e aos modelos.

### <a name="interact-with-a-device"></a>Interagir com um dispositivo

Você pode usar a extensão para exibir e interagir com dispositivos IoT Plug and Play que estão conectados a um hub IoT. A extensão funciona com o FileUp digital que representa o dispositivo de Plug and Play de IoT.

#### <a name="list-devices-and-interfaces"></a>Listar dispositivos e interfaces

Listar todos os dispositivos em um hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Listar todas as interfaces registradas por um dispositivo de Plug and Play IoT:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriedades

Listar todas as propriedades e valores de propriedade para uma interface em um dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Defina o valor de uma propriedade de leitura/gravação:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Um arquivo de conteúdo de exemplo para definir a propriedade **Name** na interface do **sensor** de um dispositivo para **contoso** é semelhante ao seguinte:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Comandos

Listar todos os comandos para uma interface em um dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sem o `--repo-login` parâmetro, esse comando usa o repositório de modelo público.

Invocar um comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventos de entrelaçamento digital

Monitore todos os eventos de Plug and Play de informações digitais de IoT de um dispositivo e interface específicos indo para o grupo de consumidores do hub de eventos **$Default** :

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorar todos os eventos de Plug and Play de informações digitais de IoT de um dispositivo específico e uma interface que vai passar por um grupo de consumidores específico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gerenciar interfaces em um repositório de modelos

Os comandos a seguir usam o repositório de modelo de Plug and Play de IoT público. Para usar um repositório de modelo da empresa, `--login` adicione o argumento com a cadeia de conexão do repositório de modelos.

Listar interfaces no repositório de modelo de Plug and Play de IoT público:

```azurecli
az iot pnp interface list
```

Mostrar uma interface no repositório de modelo de Plug and Play de IoT público:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Crie uma interface no repositório de modelo da empresa IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode criar uma interface diretamente no repositório de modelo público.

Atualize uma interface no repositório de modelo da empresa IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode atualizar diretamente uma interface no repositório de modelo público.

Publique uma interface do repositório de modelo da empresa Plug and Play IoT no repositório de modelo público. Esta operação torna a interface imutável:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Somente os parceiros da Microsoft podem publicar interfaces no repositório de modelo público.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gerenciar modelos de capacidade de dispositivo em um repositório de modelos

Os comandos a seguir usam o repositório de modelo de Plug and Play de IoT público. Para usar um repositório de modelo da empresa, `--login` adicione o argumento com a cadeia de conexão do repositório de modelos.

Listar modelos de capacidade de dispositivo no repositório de modelo público do Plug and Play IoT:

```azurecli
az iot pnp capability-model list
```

Mostrar um modelo de capacidade de dispositivo no repositório de modelo público do Plug and Play IoT:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Criar um modelo de capacidade de dispositivo em um repositório de modelo da empresa IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode criar um modelo diretamente no repositório de modelo público.

Atualize um modelo de capacidade de dispositivo no repositório de modelo da empresa IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode atualizar um modelo diretamente no repositório de modelo público.

Publique um modelo de capacidade de dispositivo do repositório de modelo da empresa IoT Plug and Play para o repositório de modelo público. Esta operação torna o modelo imutável:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Somente os parceiros da Microsoft podem publicar modelos no repositório de modelos públicos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu como instalar e usar a extensão do Azure IoT para o CLI do Azure interagir com seus dispositivos Plug and Play. Uma próxima etapa sugerida é aprender a [gerenciar modelos](./howto-manage-models.md).
