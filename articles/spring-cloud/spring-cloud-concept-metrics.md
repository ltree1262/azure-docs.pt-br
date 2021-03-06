---
title: Noções básicas sobre métricas do Azure Spring Cloud
description: Saiba como examinar as métricas no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 3f88857ea66f65ff90705bc1d1e3fb745cc33709
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982744"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Entender as métricas para o Azure Spring Cloud

O Azure Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. 

No Azure Spring Cloud, há dois pontos de exibição para métricas.
* Gráficos em cada página de visão geral do aplicativo
* Página métricas comuns

 ![Gráficos de métricas](media/metrics/metrics-1.png)

Os gráficos na **visão geral** do aplicativo fornecem verificações rápidas de status para cada aplicativo. A página **métricas** comuns contém todas as métricas disponíveis para referência. Você pode criar seus próprios gráficos na página métricas comuns e fixá-los no painel.

## <a name="application-overview-page"></a>Página Visão geral do aplicativo
Selecione um aplicativo no **Gerenciamento de aplicativos** para localizar gráficos na página Visão geral.  

 ![Gerenciamento de métricas do aplicativo](media/metrics/metrics-2.png)

A página **Visão Geral do Aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você realize uma rápida verificação de status do seu aplicativo.  

 ![Visão geral das métricas do aplicativo](media/metrics/metrics-3.png)

O Azure Spring Cloud fornece esses cinco gráficos com métricas que são atualizadas a cada minuto:

* **Erros do servidor http**: contagem de erros para solicitações HTTP para seu aplicativo
* **Dados em**: bytes recebidos por seu aplicativo
* **Saída de dados**: bytes enviados pelo seu aplicativo
* **Solicitações**: solicitações recebidas por seu aplicativo
* **Tempo médio de resposta**: tempo médio de resposta do seu aplicativo

Para o gráfico, você pode selecionar um intervalo de tempo de uma hora para sete dias.

## <a name="common-metrics-page"></a>Página métricas comuns

As **métricas** no painel de navegação à esquerda são vinculadas à página métricas comuns.

Primeiro, selecione as métricas a serem exibidas:

![Selecionar exibição de métrica](media/metrics/metrics-4.png)

A opção detalhes de todas as métricas pode ser encontrada na [seção](#user-metrics-options) abaixo.

Em seguida, selecione tipo de agregação para cada métrica:

![Agregação de métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar pontos de métricas no gráfico por tempo. Há um ponto de métrica bruto a cada minuto, e o tipo de pré-autenticação em minutos é predefinido pelo tipo de métricas.
* Sum: soma todos os valores como saída de destino.
* Média: Use o valor médio no período como saída de destino.
* Max/min: Use o valor max/min no período como saída de destino.

O intervalo de tempo a ser mostrado também pode ser modificado. O intervalo de tempo pode ser escolhido nos últimos 30 minutos até os últimos 30 dias ou um intervalo de tempo personalizado.

![Modificação de métrica](media/metrics/metrics-6.png)

A exibição padrão inclui todas as métricas de application's do serviço de nuvem do Spring do Azure juntas. As métricas de um aplicativo ou instância podem ser filtradas na exibição.  Clique em **Adicionar filtro**, defina a propriedade como **aplicativo**e selecione o aplicativo de destino que você deseja monitorar na caixa de texto **valores** . 

Você pode usar dois tipos de filtros (Propriedades):
* Aplicativo: filtrar por nome do aplicativo
* Instância: filtrar por instância de aplicativo

![Filtros de métrica](media/metrics/metrics-7.png)

Você também pode usar a opção **aplicar divisão** , que irá desenhar várias linhas para um aplicativo:

![Divisão de métrica](media/metrics/metrics-8.png)

>[!TIP]
> Você pode criar seus próprios gráficos na página de métricas e fixá-los em seu **Painel**. Comece nomeando seu gráfico.  Em seguida, selecione **Fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **Painel** do Portal.

## <a name="user-metrics-options"></a>Opções de métricas do usuário

As tabelas a seguir mostram as métricas e os detalhes disponíveis.

### <a name="error"></a>Erro
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| TomcatErrorCount<br><br>Erro global do Tomcat (preterido) | Tomcat. global. Error | Contagem | Número de erros de solicitações processadas |
>| Tomcat. global. Error | Tomcat. global. Error | Contagem | Número de erros de solicitações processadas |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| SystemCpuUsagePercentage<br><br>Porcentagem de uso da CPU do sistema (preterida) | System. CPU. Usage | Porcentagem | Uso recente da CPU para todo o sistema. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que todas as CPUs estavam ociosas durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam executando ativamente 100% do tempo durante o período recente observado.|
>| System. CPU. Usage | System. CPU. Usage | Porcentagem | Uso recente da CPU para todo o sistema. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que todas as CPUs estavam ociosas durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam executando ativamente 100% do tempo durante o período recente observado.|
>| AppCpuUsagePercentage<br><br>Porcentagem de uso da CPU do aplicativo (preterida) | Porcentagem de uso de CPU do aplicativo | Porcentagem | Uso recente da CPU para o processo de Máquina Virtual Java. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que nenhuma das CPUs estavam executando threads do processo JVM durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam ativamente executando threads da JVM 100% do tempo durante o período recente observado. Os threads da JVM incluem os threads do aplicativo, bem como os threads internos da JVM.|
>| Process. CPU. Usage | Porcentagem de uso de CPU do aplicativo | Porcentagem | Uso recente da CPU para o processo de Máquina Virtual Java. Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que nenhuma das CPUs estavam executando threads do processo JVM durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam ativamente executando threads da JVM 100% do tempo durante o período recente observado. Os threads da JVM incluem os threads do aplicativo, bem como os threads internos da JVM.|
>| AppMemoryCommitted<br><br>Memória de aplicativo atribuída (preterida)) | JVM. Memory. Committed | Bytes | Representa a quantidade de memória que tem a garantia de estar disponível para uso pela JVM. A JVM pode liberar memória para o sistema e confirmada pode ser menor que init. Commit sempre será maior ou igual a usado. |
>| JVM. Memory. Committed | JVM. Memory. Committed | Bytes | Representa a quantidade de memória que tem a garantia de estar disponível para uso pela JVM. A JVM pode liberar memória para o sistema e confirmada pode ser menor que init. Commit sempre será maior ou igual a usado. |
>| AppMemoryUsed <br><br>Memória do aplicativo usada (preterida) | JVM. Memory. Used | Bytes | Representa a quantidade de memória usada atualmente em bytes. |
>| JVM. Memory. Used | JVM. Memory. Used | Bytes | Representa a quantidade de memória usada atualmente em bytes. |
>| AppMemoryMax<br><br>Memória máxima do aplicativo (preterida) | JVM. Memory. Max | Bytes | Representa a quantidade máxima de memória que pode ser usada para gerenciamento de memória. A quantidade de memória usada e confirmada sempre será menor ou igual ao máximo se a opção máximo for definida. Uma alocação de memória pode falhar se tentar aumentar a memória usada, de modo que usada > confirmada mesmo se usado <= Max ainda seria verdadeiro (por exemplo, quando o sistema está com pouca memória virtual). |
>| JVM. Memory. Max | JVM. Memory. Max | Bytes | Representa a quantidade máxima de memória que pode ser usada para gerenciamento de memória. A quantidade de memória usada e confirmada sempre será menor ou igual ao máximo se a opção máximo for definida. Uma alocação de memória pode falhar se tentar aumentar a memória usada, de modo que usada > confirmada mesmo se usado <= Max ainda seria verdadeiro (por exemplo, quando o sistema está com pouca memória virtual). |
>| MaxOldGenMemoryPoolBytes<br><br>Tamanho máximo de dados de geração antiga disponíveis (preterido) | JVM. GC. Max. Data. Size | Bytes | O pico de uso de memória do pool de memória de geração antiga desde que a máquina virtual Java foi iniciada. |
>| JVM. GC. Max. Data. Size | JVM. GC. Max. Data. Size | Bytes | O pico de uso de memória do pool de memória de geração antiga desde que a máquina virtual Java foi iniciada. |
>| OldGenMemoryPoolBytes<br><br>Tamanho de dados de geração antiga (preterido) | JVM. GC. Live. Data. Size | Bytes | Tamanho do pool de memória de geração antiga após um GC completo. |
>| JVM. GC. Live. Data. Size | JVM. GC. Live. Data. Size | Bytes | Tamanho do pool de memória de geração antiga após um GC completo. |
>| OldGenPromotedBytes<br><br>Promover para tamanho de dados de geração antiga (preterido) | JVM. GC. Memory. promovida | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC. |
>| JVM. GC. Memory. promovida | JVM. GC. Memory. promovida | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC. |
>| YoungGenPromotedBytes<br><br>Promover para o tamanho de dados de geração jovem (preterido) | JVM. GC. Memory. allocated | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo. |
>| JVM. GC. Memory. allocated | JVM. GC. Memory. allocated | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo. |
>| GCPauseTotalCount<br><br>Contagem de pausar do GC (preterida) | JVM. GC. PAUSE (total-contagem) | Contagem | Contagem total de GC após esse JMV iniciado, incluindo GC jovem e antigo. |
>| JVM. GC. PAUSE. total. Count | JVM. GC. PAUSE (total-contagem) | Contagem | Contagem total de GC após esse JMV iniciado, incluindo GC jovem e antigo. |
>| GCPauseTotalTime<br><br>Tempo total de pausa do GC (preterido) | JVM. GC. PAUSE (tempo total) | Milissegundos | Tempo total de GC consumido após esse JMV iniciado, incluindo o GC jovem e antigo. |
>| JVM. GC. PAUSE. total. time | JVM. GC. PAUSE (tempo total) | Milissegundos | Tempo total de GC consumido após esse JMV iniciado, incluindo o GC jovem e antigo. |
>| Tomcat. threads. config. Max | Tomcat. threads. config. Max | Contagem | Contagem máxima de threads de configuração do Tomcat |
>| Tomcat. threads. Current | Tomcat. threads. Current | Contagem | Contagem de threads atuais do Tomcat |
>| Tomcat. global. Request. AVG. time | Tomcat. global. Request. AVG. time | Milissegundos | Tempo médio da solicitação do Tomcat |


### <a name="request"></a>Solicitação
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| TomcatSentBytes<br><br>Total de bytes enviados do Tomcat (preterido) | Tomcat. global. sent | Bytes | Quantidade de dados do servidor Web Tomcat enviados |
>| Tomcat. global. sent | Tomcat. global. sent | Bytes | Quantidade de dados do servidor Web Tomcat enviados |
>| TomcatReceivedBytes<br><br>Total de bytes recebidos do Tomcat (preterido) | Tomcat. global. Received | Bytes | Quantidade de dados do servidor Web Tomcat recebidos |
>| Tomcat. global. Received | Tomcat. global. Received | Bytes | Quantidade de dados do servidor Web Tomcat recebidos |
>| TomcatRequestTotalTime<br><br>Tempo total da solicitação do Tomcat (preterido) | Tomcat. global. Request (tempo total) | Milissegundos | Tempo total do servidor Web Tomcat para processar as solicitações |
>| TomcatRequestTotalCount<br><br>Contagem total da solicitação do Tomcat (preterida) | Tomcat. global. Request (total-contagem) | Contagem | Contagem total de solicitações processadas do servidor Web Tomcat |
>| Tomcat. global. Request. total. Count | Tomcat. global. Request (total-contagem) | Contagem | Contagem total de solicitações processadas do servidor Web Tomcat |
>| TomcatRequestMaxTime<br><br>Tempo máximo de solicitação do Tomcat (preterido) | Tomcat. global. Request. Max | Milissegundos | Tempo máximo do servidor Web Tomcat para processar uma solicitação |
>| Tomcat. global. Request. Max | Tomcat. global. Request. Max | Milissegundos | Tempo máximo do servidor Web Tomcat para processar uma solicitação |

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| TomcatSessionActiveMaxCount<br><br>Contagem ativa máxima da sessão do Tomcat (preterida) | Tomcat. Sessions. Active. Max | Contagem | Número máximo de sessões que estão ativas ao mesmo tempo |
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Contagem | Número máximo de sessões que estão ativas ao mesmo tempo |
>| TomcatSessionAliveMaxTime<br><br>Tempo de atividade máx de sessão do Tomcat (preterido) | Tomcat. Sessions. Alive. Max | Milissegundos | Tempo mais longo (em segundos) que uma sessão expirada esteve ativa |
>| Tomcat. Sessions. Alive. Max | Tomcat. Sessions. Alive. Max | Milissegundos | Tempo mais longo (em segundos) que uma sessão expirada esteve ativa |
>| TomcatSessionCreatedCount<br><br>Contagem criada da sessão Tomcat (preterida) | Tomcat. Sessions. Created | Contagem | Número de sessões que foram criadas |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Contagem | Número de sessões que foram criadas |
>| TomcatSessionExpiredCount<br><br>Contagem de sessões expiradas do Tomcat (preterida) | Tomcat. Sessions. Expired | Contagem | Número de sessões que expiraram |
>| Tomcat. Sessions. Expired | Tomcat. Sessions. Expired | Contagem | Número de sessões que expiraram |
>| TomcatSessionRejectedCount<br><br>Contagem rejeitada da sessão Tomcat (preterida) | Tomcat. Sessions. Rejected | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas foi atingido. |
>| Tomcat. Sessions. Rejected | Tomcat. Sessions. Rejected | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas foi atingido. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Contagem | Contagem ativa da sessão Tomcat |

## <a name="see-also"></a>Confira também
* [Introdução ao Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [Analisar logs e métricas com configurações de diagnóstico](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>Próximas etapas
* [Tutorial: monitorar recursos de nuvem Spring usando alertas e grupos de ações](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Cotas e planos de serviço para o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

