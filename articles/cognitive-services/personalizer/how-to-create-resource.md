---
title: Crie um recurso do Personalizador
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80336053"
---
# <a name="create-a-personalizer-resource"></a>Criar um recurso personalizado

Um recurso personalizado é a mesma coisa que um loop de aprendizagem personalizado. Um único recurso, ou loop de aprendizagem, é criado para cada domínio de assunto ou área de conteúdo que você tem. Não use várias áreas de conteúdo no mesmo loop porque isso irá confundir o loop de aprendizado e fornecer previsões ruins.

Se você quiser que o personalizador selecione o melhor conteúdo para mais de uma área de conteúdo de uma página da Web, use um loop de aprendizagem diferente para cada.


## <a name="create-a-resource-in-the-azure-portal"></a>Crie um recurso no portal do Azure

Crie um recurso do Personalizador para cada loop de comentários.

1. Entre no [Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva você à página **Criar** do serviço do Personalizador.
1. Insira o nome do seu serviço, selecione uma assinatura, um local, um tipo de preço e um grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Use portal do Azure para criar um recurso personalizado, também chamado de loop de aprendizado.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecione **criar** para criar o recurso.

1. Depois que o recurso tiver sido implantado, selecione o botão **ir para o recurso** para ir para o recurso personalizador.

1. Selecione a página **início rápido** para seu recurso e copie os valores para o ponto de extremidade e a chave. Você precisa do ponto de extremidade do recurso e da chave para usar as APIs de classificação e recompensa.

1. Selecione a página de **configuração** do novo recurso para [Configurar o loop de aprendizagem](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Criar um recurso com o CLI do Azure

1. Entre no CLI do Azure com o seguinte comando:

    ```azurecli-interactive
    az login
    ```

1. Crie um grupo de recursos, um agrupamento lógico para gerenciar todos os recursos do Azure que você pretende usar com o recurso personalizador.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Crie um novo recurso personalizado, o _loop de aprendizagem_, com o comando a seguir para um grupo de recursos existente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Isso retorna um objeto JSON, que inclui o **ponto de extremidade do recurso**.

1. Use o comando CLI do Azure a seguir para obter a **chave de recurso**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Você precisa do ponto de extremidade do recurso e da chave para usar as APIs de classificação e recompensa.

## <a name="next-steps"></a>Próximas etapas

* [Configurar](how-to-settings.md) Loop de aprendizagem do personalizador