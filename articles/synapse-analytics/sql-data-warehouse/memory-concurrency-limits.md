---
title: Limites de memória e simultaneidade
description: Exiba a memória e os limites de simultaneidade alocados para os vários níveis de desempenho e classes de recursos no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 56ab49949b4ea2a92bc591042b2d43a7f7b2dc63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632677"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Limites de memória e simultaneidade do Azure Synapse Analytics

Exiba a memória e os limites de simultaneidade alocados para os vários níveis de desempenho e classes de recursos no Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Configurações de capacidade do banco de dados de data warehouse

As tabelas a seguir mostram a capacidade máxima para o data warehouse em diferentes níveis de desempenho. Para alterar o nível de desempenho, consulte [escala de computação - portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Níveis de serviço

Os níveis de serviço variam de DW100c a DW30000c.

| Nível de desempenho | Nós de computação | Distribuições por nó de computação | Memória máxima por data warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

O nível de serviço máximo é DW30000c, que tem 60 nós de computação e uma distribuição por nó de computação. Por exemplo, um data warehouse de 600 TB a DW30000c processa aproximadamente 10 TB por nó de Computação.

## <a name="concurrency-maximums-for-workload-groups"></a>Máximos de simultaneidade para grupos de carga de trabalho

Com a introdução dos [grupos de cargas de trabalho](sql-data-warehouse-workload-isolation.md), o conceito de Slots de simultaneidade não se aplica mais.  Os recursos por solicitação são alocados em uma base percentual e especificados na definição do grupo de carga de trabalho.  No entanto, mesmo com a remoção de Slots de simultaneidade, há uma quantidade mínima de recursos necessários por consultas com base no nível de serviço.  A tabela abaixo definiu a quantidade mínima de recursos necessários por consulta nos níveis de serviço e a simultaneidade associada que pode ser obtida.

|Nível de serviço|Máximo de consultas simultâneas|Mínimo de% com suporte para REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8%|
|DW400c|16|6,25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Máximos de simultaneidade para classes de recurso

Para garantir que cada consulta tenha recursos suficientes para ser executada com eficiência, a análise de SQL no Azure Synapse controla a utilização de recursos atribuindo slots de simultaneidade a cada consulta. O sistema coloca as consultas em uma fila com base em importância e slots de simultaneidade. As consultas esperam na fila até que slots de simultaneidade suficientes estejam disponíveis. Os slots de [importância](sql-data-warehouse-workload-importance.md) e de simultaneidade determinam a priorização da CPU. Para saber mais, confira [Analisar sua carga de trabalho](analyze-your-workload.md)

**Classes de recursos estáticos**

A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso estático](resource-classes-for-workload-management.md).  

| Nível de serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo staticrc10 | Slots usados pelo staticrc20 | Slots usados pelo staticrc30 | Slots usados pelo staticrc40 | Slots usados pelo staticrc50 | Slots usados pelo staticrc60 | Slots usados pelo staticrc70 | Slots usados pelo staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classes de recursos dinâmicos**

A tabela a seguir mostra o número máximo de consultas simultâneas e slots de simultaneidade para cada [classe de recurso dinâmico](resource-classes-for-workload-management.md). As classes de recursos dinâmicos usam uma alocação de porcentagem de memória de 3-10-22-70 para classes de recursos de XLarge pequeno e médio porte em todos os níveis de serviço.

| Nível de serviço | Máximo de consultas simultâneas | Slots de simultaneidade disponíveis | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Quando não há slots de simultaneidade suficientes livres para iniciar a execução da consulta, as consultas são enfileiradas e executadas com base na importância.  Se houver importância equivalente, as consultas serão executadas em uma base primeiro a entrar, primeiro a sair.  À medida que uma consulta é concluída e o número de consultas e slots ficam abaixo do limite, o SQL Data Warehouse libera as consultas em fila.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar as classes de recursos para otimizar ainda mais a carga de trabalho, examine os seguintes artigos:

* [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md)
* [Análise de sua carga de trabalho](analyze-your-workload.md)
