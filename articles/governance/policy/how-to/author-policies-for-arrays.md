---
title: Políticas de autor para propriedades de matriz em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões de linguagem de matriz, avaliar o alias [*] e acrescentar elementos com regras de definição de Azure Policy.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280658"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matriz nos recursos do Azure

Azure Resource Manager propriedades são normalmente definidas como cadeias de caracteres e boolianos. Quando existe uma relação um-para-muitos, as propriedades complexas são definidas como matrizes. Em Azure Policy, as matrizes são usadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **em** ou **notIn**
- Parte de uma regra de política que avalia o [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) a ser avaliado:
  - Cenários como **nenhum**, **qualquer**ou **todos**
  - Cenários complexos com **contagem**
- No [efeito de acréscimo](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo aborda cada uso por Azure Policy e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

A definição de um parâmetro como uma matriz permite a flexibilidade da política quando mais de um valor é necessário.
Essa definição de política permite qualquer local único para o parâmetro **allowedLocations** e o padrão _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Como o **tipo** era _String_, apenas um valor pode ser definido ao atribuir a política. Se essa política for atribuída, os recursos no escopo só serão permitidos em uma única região do Azure. A maioria das definições de políticas precisa permitir uma lista de opções aprovadas, como permitir _eastus2_, _eastus_e _westus2_.

Para criar a definição de política para permitir várias opções, use o **tipo**de _matriz_ . A mesma política pode ser reescrita da seguinte maneira:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Quando uma definição de política é salva, a propriedade **Type** em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro usa mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definida, os valores disponíveis durante a atribuição são limitados ainda mais à lista predefinida de opções. O uso de **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passar valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política por meio do portal do Azure, um parâmetro do **tipo** _matriz_ é exibido como uma única caixa de texto. A dica diz "usar; para separar valores. (por exemplo, Londres; Nova York) ". Para passar os valores de local permitidos de _eastus2_, _eastus_e _westus2_ para o parâmetro, use a seguinte cadeia de caracteres:

`eastus2;eastus;westus2`

O formato do valor do parâmetro é diferente ao usar CLI do Azure, Azure PowerShell ou a API REST. Os valores são passados por meio de uma cadeia de caracteres JSON que também inclui o nome do parâmetro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Para usar essa cadeia de caracteres com cada SDK, use os seguintes comandos:

- CLI do Azure: comando [AZ Policy atribuition Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) com **parâmetros de parâmetro**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- API REST: na operação _Put_ [criar](/rest/api/resources/policyassignments/create) como parte do corpo da solicitação como o valor da propriedade **Properties. Parameters**

## <a name="policy-rules-and-arrays"></a>Regras de política e matrizes

### <a name="array-conditions"></a>Condições de matriz

As [condições](../concepts/definition-structure.md#conditions) de regra de política _array_
para as quais um**tipo** de matriz de parâmetro pode ser `in` usado `notIn`são limitadas a e. Use a seguinte definição de política com `equals` Condition como exemplo:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

A tentativa de criar essa definição de política por meio do portal do Azure leva a um erro como esta mensagem de erro:

- "A política ' {GUID} ' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros da política estão definidos corretamente. O resultado da avaliação da exceção interna da expressão de linguagem ' [Parameters (' allowedLocations ')] ' é do tipo ' array ', o tipo esperado é ' String '. '. "

O **tipo** de condição `equals` esperado é _String_. Como **allowedLocations** é definido como **type** _matriz_de tipo, o mecanismo de política avalia a expressão de idioma e gera o erro. Com a `in` condição `notIn` and, o mecanismo de política espera a _matriz_ de **tipo** na expressão de linguagem. Para resolver essa mensagem de erro, `equals` altere para `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliando o alias [*]

Os aliases que foram ** \[ \* ** anexados ao seu nome indicam que o **tipo** é uma _matriz_. Em vez de avaliar o valor de toda a matriz ** \[ \* ** , o torna possível avaliar cada elemento da matriz individualmente, com and lógico entre elas. Há três cenários padrão pelos quais a avaliação por item é útil em: _nenhum_, _qualquer_ou _todos os_ elementos correspondem. Para cenários complexos, use [Count](../concepts/definition-structure.md#count).

O mecanismo de política aciona o **efeito** em **seguida** somente quando a regra **If** é avaliada como true.
Esse fato é importante entender no contexto da maneira ** \[ \* ** que avalia cada elemento individual da matriz.

A regra de política de exemplo para a tabela de cenário abaixo:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

A matriz **ipRules** é a seguinte para a tabela de cenário abaixo:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Para cada exemplo de condição abaixo, `<field>` substitua `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`por.

Os resultados a seguir são o resultado da combinação da condição e a regra de política de exemplo e a matriz de valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Nenhuma correspondência |Um elemento de matriz é avaliado como falso (127.0.0.1! = 127.0.0.1) e outro como verdadeiro (127.0.0.1! = 192.168.1.1), portanto, a condição não é **igual** a _false_ e o efeito não é disparado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito de política |Nenhuma correspondência |Ambos os elementos da matriz são avaliados como verdadeiros (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, a condição não é **igual** a _true_ e o efeito é disparado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito de política |Uma ou mais correspondências |Um elemento de matriz é avaliado como falso (127.0.0.1! = 127.0.0.1) e outro como verdadeiro (127.0.0.1! = 192.168.1.1), portanto, a condição não é **igual** a _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Uma ou mais correspondências |Ambos os elementos de matriz são avaliados como verdadeiros (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, a condição de não é **igual** a _true_. O operador lógico é avaliado como falso (**não** _verdadeiro_), portanto, o efeito não é disparado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito de política |Nem todas as correspondências |Um elemento de matriz é avaliado como verdadeiro (127.0.0.1 = = 127.0.0.1) e outro como falso (127.0.0.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito de política |Nem todas as correspondências |Ambos os elementos da matriz são avaliados como falso (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Todas as correspondências |Um elemento de matriz é avaliado como verdadeiro (127.0.0.1 = = 127.0.0.1) e outro como falso (127.0.0.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Todas as correspondências |Ambos os elementos da matriz são avaliados como falso (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |

## <a name="the-append-effect-and-arrays"></a>As matrizes e o efeito de acréscimo

O [efeito de acréscimo](../concepts/effects.md#append) comporta-se de maneira diferente dependendo de se o **Details. Field** é um ** \[ \* ** alias ou não.

- Quando não é ** \[ \* ** um alias, Append substitui toda a matriz pela propriedade **Value**
- Quando um ** \[ \* ** alias, Append adiciona a propriedade **Value** à matriz existente ou cria a nova matriz

Para obter mais informações, consulte os [exemplos de acréscimo](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Próximas etapas

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
