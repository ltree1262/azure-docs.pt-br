---
title: Cláusula ORDER BY no Azure Cosmos DB
description: Saiba mais sobre a cláusula ORDER BY do SQL para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641390"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Cláusula ORDER BY no Azure Cosmos DB

A cláusula `ORDER BY` opcional especifica a ordem de classificação dos resultados retornados pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um nome ou alias de propriedade.  
  
   Várias propriedades podem ser especificadas. Os nomes de propriedade devem ser exclusivos. A sequência das propriedades de classificação na `ORDER BY` cláusula define a organização do conjunto de resultados classificado. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
   Os nomes de propriedade referenciados `ORDER BY` na cláusula devem corresponder a uma propriedade na lista de seleção ou a uma propriedade definida na coleção especificada na `FROM` cláusula sem nenhuma ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica uma ou mais propriedades ou expressões nas quais classificar o conjunto de resultados da consulta.  
  
- `<scalar_expression>`  
  
   Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  
- `ASC | DESC`  
  
   Define que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. `ASC`classifica do valor mais baixo para o valor mais alto. `DESC`classifica do valor mais alto para o valor mais baixo. `ASC`é a ordem de classificação padrão. Valores nulos são tratados como os menores valores possíveis.  
  
## <a name="remarks"></a>Comentários  
  
   A `ORDER BY` cláusula requer que a política de indexação inclua um índice para os campos que estão sendo classificados. O tempo de execução de Azure Cosmos DB consulta dá suporte à classificação em relação a um nome de propriedade e não a Propriedades computadas. Azure Cosmos DB dá suporte `ORDER BY` a várias propriedades. Para executar uma consulta com várias propriedades ORDER BY, você deve definir um [índice composto](index-policy.md#composite-indexes) nos campos que estão sendo classificados.

> [!Note]
> Se as propriedades que estão sendo classificadas puderem ser indefinidas para alguns documentos e você quiser recuperá-las em uma consulta ORDER BY, você deverá incluir explicitamente esse caminho no índice. A política de indexação padrão não permitirá a recuperação dos documentos em que a Propriedade Sort é indefinida. [Examine as consultas de exemplo em documentos com alguns campos ausentes](#documents-with-missing-fields).

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que recupera famílias em ordem crescente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A consulta a seguir recupera `id`a família s em ordem de data de criação do item. Item `creationDate` é um número que representa o *tempo de época*ou tempo decorrido desde Jan. 1, 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Além disso, você pode ordenar por várias propriedades. Uma consulta que ordena por várias propriedades requer um [índice composto](index-policy.md#composite-indexes). Considere a consulta a seguir.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Essa consulta recupera a família `id` em ordem crescente do nome da cidade. Se vários itens tiverem o mesmo nome de cidade, a consulta será ordenada pelo `creationDate` em ordem decrescente.

## <a name="documents-with-missing-fields"></a>Documentos com campos ausentes

As consultas `ORDER BY` com que são executadas em contêineres com a política de indexação padrão não retornarão documentos em que a Propriedade Sort esteja indefinida. Se você quiser incluir documentos em que a Propriedade Sort está indefinida, você deve incluir explicitamente essa propriedade na política de indexação.

Por exemplo, aqui está um contêiner com uma política de indexação que não inclui explicitamente nenhum caminho além `"/*"`de:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se você executar uma consulta que inclui `lastName` na `Order By` cláusula, os resultados só incluirão documentos que tenham uma `lastName` propriedade definida. Não definimos um caminho explicitamente incluído para `lastName` que qualquer documento sem um `lastName` não seja exibido nos resultados da consulta.

Aqui está uma consulta que classifica `lastName` em dois documentos, um dos quais não tem um `lastName` definido:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados incluem apenas o documento que tem um definido `lastName`:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se atualizarmos a política de indexação do contêiner para incluir explicitamente um caminho `lastName`para, incluiremos documentos com uma propriedade de classificação indefinida nos resultados da consulta. Você deve definir explicitamente o caminho para levar a esse valor escalar (e não além dele). Você deve usar o `?` caractere em sua definição de caminho na política de indexação para garantir que você indexe explicitamente `lastName` a propriedade e nenhum caminho aninhado adicional além dela. Se a `Order By` consulta usar um [índice composto](index-policy.md#composite-indexes), os resultados sempre incluirão documentos com uma propriedade de classificação indefinida nos resultados da consulta.

Aqui está um exemplo de política de indexação que permite que você tenha documentos com um `lastName` indefinido que apareça nos resultados da consulta:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se você executar a mesma consulta novamente, os documentos ausentes `lastName` aparecerão primeiro nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados são:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Se você modificar a ordem de classificação `DESC`para, os documentos ausentes `lastName` aparecerão por último nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução](sql-query-getting-started.md)
- [Políticas de indexação no Azure Cosmos DB](index-policy.md)
- [Cláusula de limite de deslocamento](sql-query-offset-limit.md)
