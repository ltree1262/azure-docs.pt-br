---
title: Configuração de Azure App como fonte de grade de eventos
description: Este artigo descreve como usar a configuração de Azure App como uma origem de evento de grade de eventos. Ele fornece o esquema e links para os artigos tutorial e instruções.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393422"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Configuração de Azure App como uma fonte de grade de eventos
Este artigo fornece as propriedades e o esquema para Azure App eventos de configuração. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar Azure App configuração como uma fonte de eventos.

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

Azure App configuração emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Gerado quando um valor de chave é criado ou substituído. |
| Microsoft. AppConfiguration. KeyValueDeleted | Gerado quando um valor de chave é excluído. |

### <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento de chave-valor modificado: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento de chave-valor excluído é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | cadeia de caracteres | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | cadeia de caracteres | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | cadeia de caracteres | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | cadeia de caracteres | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | cadeia de caracteres | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de configuração de aplicativo. |
| dataVersion | cadeia de caracteres | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | cadeia de caracteres | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| chave | cadeia de caracteres | A chave do valor de chave que foi modificado ou excluído. |
| label | cadeia de caracteres | O rótulo, se houver, do valor de chave que foi modificado ou excluído. |
| etag | cadeia de caracteres | Para `KeyValueModified` a eTag do novo valor de chave. Para `KeyValueDeleted` a eTag do valor de chave que foi excluído. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções

|Title | Descrição |
|---------|---------|
| [Reagir a Azure App eventos de configuração usando a grade de eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da configuração de Azure App com a grade de eventos. |
| [Início rápido: rotear Azure App eventos de configuração para um ponto de extremidade da Web personalizado com CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar CLI do Azure para enviar Azure App eventos de configuração para um webhook. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter uma introdução ao trabalho com Azure App eventos de configuração, consulte [rotear Azure app eventos de configuração-CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 