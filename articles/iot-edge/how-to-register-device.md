---
title: Registrar um novo dispositivo de Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para a CLI do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235719"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrar um dispositivo Azure IoT Edge

Para poder usar seus dispositivos IoT com o Azure IoT Edge, você deve registrá-los com o Hub IoT. Depois que um dispositivo é registrado, você pode recuperar uma cadeia de conexão para configurar seu dispositivo para cargas de trabalho de IoT Edge.

Você tem a opção de registrar-se usando uma das seguintes ferramentas:

* [Registre um dispositivo no portal do Azure](#register-in-the-azure-portal) se você preferir uma interface gráfica do usuário para criar, exibir e gerenciar recursos do Azure.
* [Registre um dispositivo com Visual Studio Code](#register-with-visual-studio-code) se preferir gerenciar os recursos de IOT do Azure no mesmo local em que você desenvolve suas soluções de IOT.
* [Registre um dispositivo com o CLI do Azure](#register-with-the-azure-cli) se você preferir ferramentas de linha de comando para gerenciar recursos do Azure ou se pretende automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Registrar no portal do Azure

Você pode executar todas as tarefas de registro no portal do Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal do Azure

Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo IoT Edge no portal do Azure

No Hub IoT na portal do Azure, os dispositivos IoT Edge são criados e gerenciados separadamente dos dispositivos IOT que não estão habilitados para a borda.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT.
2. No painel esquerdo, selecione **IOT Edge** no menu.
3. Selecione **Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e conectar o novo dispositivo ao seu hub.
5. Selecione **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos IoT Edge no portal do Azure

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a cadeia de conexão no portal do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Na página **IOT Edge** no portal, clique na ID do dispositivo na lista de dispositivos IOT Edge.
2. Copie o valor da **cadeia de conexão primária** ou da **cadeia de conexão secundária**.

## <a name="register-with-visual-studio-code"></a>Registrar com Visual Studio Code

Há múltiplas maneiras de fazer mais operações no VS Code. Este artigo usa o Explorer, mas você também pode usar a paleta de comandos para executar as etapas.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Visual Studio Code

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

Você pode usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, você precisa entrar em sua conta do Azure e selecionar o Hub IoT.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. Na parte inferior do Explorer, expanda a seção **Hub IOT do Azure** .

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no cabeçalho da seção **...** no **Hub IOT do Azure** . Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Escolha **Selecionar Hub IoT**.
1. Se você não estiver conectado à sua conta do Azure, siga os prompts para fazer isso.
1. Selecione sua assinatura do Azure.
1. Selecione seu Hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Criar um dispositivo IoT Edge com Visual Studio Code

1. No VS Code Explorer, expanda a seção **dispositivos do Hub IOT do Azure** .
1. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Selecione **Criar dispositivo IoT Edge**.
1. Na caixa de texto que abrir, dê a seu dispositivo um ID.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos IoT Edge com Visual Studio Code

Todos os dispositivos que se conectam ao Hub IoT são listados na seção **Hub IOT do Azure** do Visual Studio Code Explorer. IoT Edge dispositivos são distinguíveis de dispositivos que não são de borda com um ícone diferente e o fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo IOT Edge.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com Visual Studio Code

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Clique com o botão direito do mouse na ID do seu dispositivo na seção **Hub IOT do Azure** .
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registrar com o CLI do Azure

O [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e recursos completos de IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para o CLI do Azure

* Um [Hub IOT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.70 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo em seu hub IOT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: fornece um nome descritivo exclusivo para o hub IoT.
* **hub-name**: fornece o nome do hub IoT.
* **edge-enabled**: esse parâmetro declara que o dispositivo é para uso com IoT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos IoT Edge com o CLI do Azure

Use o comando [AZ IOT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) para exibir todos os dispositivos em seu hub IOT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com o CLI do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o comando [AZ IOT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) para retornar a cadeia de conexão para um único dispositivo:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma identidade de dispositivo registrada em seu hub IoT, você está pronto para instalar o IoT Edge tempo de execução em seus dispositivos. Instale o tempo de execução de acordo com o sistema operacional do dispositivo:

* [Instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md)
* [Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
