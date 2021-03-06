---
title: Configurações de nó com suporte do Azure HDInsight
description: Conheça as configurações mínimas e recomendadas para nós de cluster HDInsight.
keywords: tamanhos de VM, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77484778"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações de nó padrão e recomendadas para o Azure HDInsight?

Este artigo discute as configurações de nó padrão e recomendadas para clusters do Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração de nó padrão e mínimo recomendado e tamanhos de máquina virtual para clusters

As tabelas a seguir listam os tamanhos de VM (máquina virtual) padrão e recomendados para clusters HDInsight.  Essas informações são necessárias para entender os tamanhos de VM a serem usados ao criar o PowerShell ou CLI do Azure scripts para implantar clusters HDInsight.

Se você precisar de mais de 32 nós de trabalho em um cluster, selecione um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM. 

Os únicos tipos de cluster que têm discos de dados são Kafka e clusters HBase com o recurso de gravações aceleradas habilitado. O HDInsight dá suporte a tamanhos de disco p30 e S30 nesses cenários.

As especificações de todos os tipos de VM mínimos recomendados usados neste documento são resumidas na tabela a seguir.

| Tamanho              | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Para obter mais detalhes sobre as especificações de cada tipo de VM, consulte os seguintes documentos:

* [Tamanhos de máquina virtual de uso geral: série Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Tamanhos de máquina virtual com otimização de memória: série Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Tamanhos de máquina virtual de uso geral: série Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões com suporte, exceto sul do Brasil e oeste do Japão

> [!Note]
> Para obter o identificador de SKU para uso no PowerShell e outros scripts, `Standard_` adicione ao início de todos os SKUs de VM nas tabelas a seguir. Por exemplo, `D12_v2` se tornaria `Standard_D12_v2`.

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Cabeçalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Trabalho: tamanho de VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por agente |
| Trabalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos mínimos de VM recomendados |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços de ML: tamanho de VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços de ML: tamanho mínimo recomendado da VM |  |  |  |  |  | D4_v2 |  |

\*= Tamanhos de VM para clusters do Spark Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Sul do Brasil e oeste do Japão somente

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeçalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalho: tamanho de VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos mínimos de VM recomendados |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços de ML: tamanhos de VM padrão |  |  |  |  |  | D4 |
| Serviços de ML: tamanhos mínimos de VM recomendados |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Supervisor* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Próximas etapas

* [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)
