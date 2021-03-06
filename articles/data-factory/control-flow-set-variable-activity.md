---
title: Definir Atividade Variável no Azure Data Factory
description: Saiba como usar a atividade Definir Variável para definir o valor de uma variável existente definida em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e5bd3d10e4e43daf3031aae5083ee917cfe65ede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417973"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir Atividade Variável no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a atividade Definir Variável para definir o valor de uma variável existente do tipo Cadeia de caracteres, Booliano ou Matriz definido em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade no pipeline | sim
description | Texto descrevendo o que a atividade realiza | não
type | Deve ser definido como **SetVariable** | sim
value | Literal de cadeia de caracteres ou valor de objeto de expressão ao qual a variável será atribuída | sim
variableName | Nome da variável que será definida por essa atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum envolvendo variáveis no Azure Data Factory está usando uma variável como um iterador dentro de uma atividade until ou ForEach. Em uma atividade definir variável, você não pode fazer referência à variável que `value` está sendo definida no campo. Para solucionar essa limitação, defina uma variável temporária e, em seguida, crie uma segunda atividade Set Variable. A segunda atividade Set Variable define o valor do iterador para a variável temporária. 

Abaixo está um exemplo desse padrão:

![Incrementar variável](media/control-flow-set-variable-activity/increment-variable.png "Incrementar variável")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Acrescentar Atividade Variável](control-flow-append-variable-activity.md)
