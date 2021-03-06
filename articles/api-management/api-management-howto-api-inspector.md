---
title: Depurar as APIs usando o rastreamento de solicitação no Gerenciamento de API do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para aprender a inspecionar as etapas de processamento de solicitação no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: fc5e8c7a7aa0d4693d96c3405ec0e180a6d13f8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75768513"
---
# <a name="debug-your-apis-using-request-tracing"></a>Depure suas APIs usando o rastreamento de solicitação

Este tutorial descreve como verificar o processamento da solicitação para ajudá-lo com a depuração e a solução de problemas da API. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Rastrear uma chamada

![Inspetor de API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Prerequisites

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="trace-a-call"></a>Rastrear uma chamada

![Rastreamento de API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Selecione **APIs**.
2. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
3. Mude para a guia **Teste**.
4. Selecione a operação **GetSpeakers**.
5. Não se esqueça de incluir um cabeçalho HTTP chamado **Ocp-Apim-Trace** com o valor definido como **true**.

   > [!NOTE]
   > * Se Ocp-Apim-Subscription-Key não for preenchido automaticamente, você poderá recuperá-la indo até o Portal do Desenvolvedor e expor as chaves na página do perfil.
   > * Para obter um rastreamento quando o cabeçalho HTTP Ocp-Apim-Trace for usado, a configuração **Permitir rastreamento** na chave de assinatura precisará ser habilitada. Para definir a configuração **Permitir rastreamento**, em **Gerenciamento de API** no menu à esquerda, selecione **Assinaturas**.
   >   ![Permitir rastreamento no painel Assinaturas do Gerenciamento de API](media/api-management-howto-api-inspector/allowtracing.png)

6. Clique em **Enviar** para fazer uma chamada à API. 
7. Aguarde a conclusão da chamada. 
8. Acesse a guia **Rastreamento** no **Console de API**. Você pode clicar em qualquer um dos links a seguir para ir até as informações de rastreamento detalhadas: **entrada**, **back-end**, **saída**.

    Na seção **entrada**, é exibido o Gerenciamento de API da solicitação original recebido do chamador e todas as políticas aplicadas à solicitação incluindo as políticas de limite de taxa e cabeçalho de conjunto que adicionamos na etapa 2.

    Na seção **back-end**, é exibido o Gerenciamento de API de solicitações enviado ao back-end de API e a resposta recebida.

    Na seção **saída**, são exibidas todas as políticas aplicadas à resposta antes de enviar de volta para o chamador.

    > [!TIP]
    > Cada etapa também mostra o tempo decorrido desde quando a solicitação foi recebida pelo Gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Rastrear uma chamada

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Usar revisões](api-management-get-started-revise-api.md)
