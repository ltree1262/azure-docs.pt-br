---
title: Manipular mensagens grandes usando agrupamento
description: Saiba como lidar com tamanhos de mensagens grandes usando o agrupamento em tarefas automatizadas e fluxos de trabalho que você cria com aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 54828dded5196c86946d99a9cd8cec7a42533661
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117556"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Tratar mensagens grandes com agrupamentos nos Aplicativos Lógicos do Azure

Ao processar mensagens, os Aplicativos Lógicos limitam o conteúdo da mensagem para um tamanho máximo. Esse limite ajuda a reduzir a sobrecarga criada por armazenar e processar mensagens grandes. Para processar mensagens maiores que esse limite, os Aplicativos Lógicos podem *dividir* uma mensagem grande em mensagens menores. Dessa forma, você ainda pode transferir arquivos grandes usando Aplicativos Lógicos em condições específicas. Ao se comunicar com outros serviços por meio de conectores ou HTTP, os Aplicativos Lógicos podem consumir mensagens grandes, mas *apenas* em partes. Esta condição significa que os conectores também precisam ser compatíveis com a divisão em partes ou a troca de mensagens HTTP subjacente entre os Aplicativos Lógicos e esses serviços devem usar a divisão em partes.

Este artigo mostra como você pode configurar o compartilhamento para ações que lidam com mensagens maiores que o limite. Gatilhos do aplicativo lógico não dão suporte a agrupamento por causa do aumento sobrecarga de troca de várias mensagens. 

## <a name="what-makes-messages-large"></a>O que torna as mensagens "grandes"?

As mensagens são "grandes" com base no serviço processando essas mensagens. O limite de tamanho exato em mensagens grandes difere entre Aplicativos Lógicos e conectores. Tanto os Aplicativos Lógicos quanto os conectores não podem consumir diretamente mensagens grandes, que devem ser divididas em partes. Para o limite de tamanho de mensagem de Aplicativos Lógicos, consulte [Limites e configuração de Aplicativos Lógicos](../logic-apps/logic-apps-limits-and-config.md).
Para o limite de tamanho de mensagem de cada conector, consulte os [detalhes técnicos específicos do conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Processamento de mensagens divididas em partes para Aplicativos Lógicos

Os Aplicativos Lógicos não podem usar as saídas diretamente de mensagens divididas em partes que são maiores que o limite de tamanho da mensagem. Somente as ações que são compatíveis com a divisão em partes podem acessar o conteúdo da mensagem nessas saídas. Portanto, uma ação que processe mensagens grandes deve atender a *qualquer um* desses critérios:

* Ser nativamente compatível com a divisão em partes quando essa ação pertence a um conector. 
* Ter a compatibilidade com a divisão em partes habilitada na configuração de runtime dessa ação. 

Caso contrário, você recebe um erro de runtime quando tenta acessar a saída de conteúdo grande. Para habilitar a divisão em partes, consulte [Configurar o suporte à divisão em partes](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Processamento de mensagens divididas em partes para conectores

Os serviços que se comunicam com os Aplicativos Lógicos podem ter seus próprios limites de tamanho. Esses limites geralmente são menores que o limite de Aplicativos Lógicos. Por exemplo, supondo que um conector é compatível com a divisão em partes, um conector pode considerar que uma mensagem de 30 MB é grande, enquanto os Aplicativos Lógicos não. Para estar em conformidade com o limite desse conector, os Aplicativos Lógicos dividem todas as mensagens maiores que 30 MB em partes menores.

Para os conectores compatíveis com a divisão em partes, o protocolo de divisão em partes subjacente é invisível para os usuários finais. No entanto, nem todos os conectores são compatíveis com a divisão em partes, portanto, esses conectores geram erros de runtime ao receberem mensagens que excedem seus limites de tamanho.

> [!NOTE]
> Para ações que usam agrupamento, você não pode passar o corpo do gatilho ou usar expressões como `@triggerBody()?['Content']` nessas ações. Em vez disso, para conteúdo de texto ou arquivo JSON, você pode tentar usar a [ação **compor** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) ou [criar uma variável](../logic-apps/logic-apps-create-variables-store-values.md) para lidar com esse conteúdo. Se o corpo do gatilho contiver outros tipos de conteúdo, como arquivos de mídia, você precisará executar outras etapas para lidar com esse conteúdo.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurar a divisão em partes por HTTP

Em cenários HTTP genéricos, você pode dividir grandes downloads e uploads de conteúdo via HTTP, para que seu Aplicativo Lógico e um ponto de extremidade possam trocar mensagens grandes. No entanto, você deve dividir as mensagens em partes da forma que os Aplicativos Lógicos esperam. 

Se um ponto de extremidade tiver habilitado a divisão em partes para downloads ou uploads, as ações de HTTP no Aplicativo Lógico automaticamente dividirão as mensagens grandes em partes. Caso contrário, você deve configurar o suporte à divisão em partes no ponto de extremidade. Se você não tiver nem controlar o ponto de extremidade ou o conector, poderá não ter a opção de configurar a divisão em partes.

Além disso, se uma ação HTTP já não habilitar a divisão em partes, você também deverá configurar a divisão em partes na propriedade `runTimeConfiguration` da ação. Você pode definir essa propriedade na ação, diretamente no editor de exibição de código conforme descrito posteriormente ou no Designer de Aplicativos Lógicos conforme descrito aqui:

1. No canto superior direito da ação HTTP, escolha o botão de reticências (**... **) e, em seguida, escolha **Configurações**.

   ![Na ação, abra o menu de configurações](./media/logic-apps-handle-large-messages/http-settings.png)

2. Em **Transferência de Conteúdo**, defina **Permitir divisão em partes** como **Ativo**.

   ![Ativar a divisão em partes](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para continuar a configuração da divisão em partes para downloads ou uploads, continue com as seções a seguir.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Baixar conteúdo em partes

Muitos pontos de extremidade automaticamente enviam mensagens grandes em partes quando baixadas por meio de uma solicitação HTTP GET. Para baixar mensagens em partes de um ponto de extremidade via HTTP, o ponto de extremidade deve ser compatível com solicitações de conteúdo parcial ou *downloads divididos em partes*. Quando seu aplicativo lógico envia uma solicitação HTTP GET para um ponto de extremidade para baixar o conteúdo e o ponto de extremidade responde com um código de status "206", a resposta contém o conteúdo em partes. Os Aplicativos Lógicos não podem controlar se um ponto de extremidade é compatível com solicitações parciais. No entanto, quando seu aplicativo lógico obtém a primeira resposta "206", seu aplicativo lógico envia automaticamente várias solicitações para baixar todo o conteúdo.

Para verificar se um ponto de extremidade pode ser compatível com o conteúdo parcial, envie uma solicitação HEAD. Essa solicitação ajuda a determinar se a resposta contém o cabeçalho `Accept-Ranges`. Dessa forma, se o ponto de extremidade for compatível com downloads em partes, mas não enviar o conteúdo em partes, você poderá *sugerir* essa opção definindo o cabeçalho `Range` na solicitação HTTP GET. 

Estas etapas descrevem o processo detalhado que os Aplicativos Lógicos usam para baixar o conteúdo em partes de um ponto de extremidade para seu aplicativo lógico:

1. Seu aplicativo lógico envia uma solicitação HTTP GET para o ponto de extremidade.

   O cabeçalho da solicitação pode incluir opcionalmente um campo `Range` que descreve um intervalo de bytes para solicitar partes de conteúdo.

2. O ponto de extremidade responde com o código de status "206" e um corpo de mensagem HTTP.

    Detalhes sobre o conteúdo nessa parte aparecem no cabeçalho `Content-Range` da resposta, incluindo informações que ajudam os Aplicativos Lógicos a determinar o início e o final da parte, além do tamanho total de todo o conteúdo antes da divisão em partes.

3. Seu aplicativo lógico envia automaticamente solicitações HTTP GET de acompanhamento.

    Seu aplicativo lógico envia solicitações GET de acompanhamento até que todo o conteúdo seja recuperado.

Por exemplo, a definição desta ação mostra uma solicitação HTTP GET que define o cabeçalho `Range`. O cabeçalho *sugere* que o ponto de extremidade deve responder com o conteúdo em partes:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

A solicitação GET define o cabeçalho “Range” como “bytes=0-1023”, que é o intervalo de bytes. Se o ponto de extremidade for compatível com solicitações de conteúdo parcial, ele responderá com uma parte do conteúdo do intervalo solicitado. Com base no ponto de extremidade, o formato exato do campo de cabeçalho “Range” pode ser diferente.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carregar conteúdo em partes

Para carregar o conteúdo em partes de uma ação de HTTP, a ação deve a compatibilidade com a divisão em partes habilitada por meio da propriedade `runtimeConfiguration` da ação. Essa configuração permite que a ação inicie o protocolo de divisão em partes. Seu aplicativo lógico, em seguida, pode enviar uma mensagem POST ou PUT inicial para o ponto de extremidade de destino. Depois que o ponto de extremidade responde com um tamanho de parte sugerido, seu aplicativo lógico acompanha enviando solicitações HTTP PATCH que contêm as partes do conteúdo.

Estas etapas descrevem o processo detalhado que os Aplicativos Lógicos usam para carregar o conteúdo em partes do aplicativo lógico para um ponto de extremidade:

1. Seu aplicativo lógico envia uma solicitação HTTP POST ou PUT inicial com o corpo da mensagem vazio. O cabeçalho de solicitação, inclui essas informações sobre o conteúdo que seu aplicativo lógico quer carregar em partes:

   | Campo de cabeçalho de solicitação de Aplicativos Lógicos | Valor | Tipo | Description |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | em partes | String | Indica que o conteúdo é carregado em partes |
   | **x-ms-content-length** | <*comprimento do conteúdo*> | Integer | O tamanho do conteúdo inteiro em bytes antes da divisão em partes |
   ||||

2. O ponto de extremidade responde com o código de status de êxito “200” e essas informações opcionais:

   | Campo de cabeçalho de resposta do ponto de extremidade | Tipo | Necessária | Descrição |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Não | O tamanho da parte sugerido em bytes |
   | **Localidade** | String | Sim | O local da URL para a qual enviar as mensagens HTTP PATCH |
   ||||

3. Seu aplicativo lógico cria e envia mensagens HTTP PATCH de acompanhamento, cada uma com essas informações:

   * Uma parte de conteúdo com base em **x-ms-chunk-size** ou algum tamanho calculado internamente até todo o conteúdo totalizando **x-ms-content-length** ser carregado sequencialmente

   * Esses detalhes de cabeçalho sobre a parte do conteúdo enviados em cada mensagem PATCH:

     | Campo de cabeçalho de solicitação de Aplicativos Lógicos | Valor | Tipo | Description |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*amplitude*> | String | O intervalo de bytes da parte do conteúdo atual, incluindo o valor inicial, o valor final e o tamanho total do conteúdo, por exemplo, "bytes=0-1023/10100" |
     | **Tipo de conteúdo** | <*tipo de conteúdo*> | String | O tipo de conteúdo em partes |
     | **Comprimento do conteúdo** | <*comprimento do conteúdo*> | String | O comprimento do tamanho em bytes da parte atual |
     |||||

4. Após cada solicitação de PATCH, o ponto de extremidade confirma o recebimento de cada parte respondendo com o código de status "200" e os seguintes cabeçalhos de resposta:

   | Campo de cabeçalho de resposta do ponto de extremidade | Tipo | Necessária | Descrição |
   |--------------------------------|------|----------|-------------|
   | **Amplitude** | String | Sim | O intervalo de bytes para o conteúdo recebido pelo ponto de extremidade, por exemplo: "bytes = 0-1023" |   
   | **x-ms-chunk-size** | Integer | Não | O tamanho da parte sugerido em bytes |
   ||||

Por exemplo, esta definição de ação mostra uma solicitação HTTP POST para carregar o conteúdo em partes para um ponto de extremidade. Na propriedade `runTimeConfiguration` da ação, a propriedade `contentTransfer` define `transferMode` como `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
