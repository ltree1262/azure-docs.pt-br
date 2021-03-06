---
title: Novidades da documentação do Azure Monitor
description: Atualizações significativas na documentação do Azure Monitor atualizada a cada mês.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80802597"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Quais são as novidades da documentação do Azure Monitor?
Este artigo lista os artigos sobre o Azure Monitor que são novos ou que foram atualizados de maneira significativa. Ele será atualizado na primeira semana de cada mês para incluir as atualizações de artigos do mês anterior.

## <a name="march-2020"></a>Março de 2020

### <a name="general"></a>Geral
- [Visão geral do Azure Monitor](overview.md): vídeo de visão geral do Azure Monitor adicionado.
- [Configuração da chave gerenciada pelo cliente do Azure Monitor](platform/customer-managed-keys.md): atualizações gerais.
- [Referência de dados do Azure Monitor](/azure/azure-monitor/reference): novo site.

### <a name="alerts"></a>Alertas

- [Criar, ver e gerenciar alertas do log de atividades no Azure Monitor](platform/alerts-activity-log.md): explicação adicional do modelo do Resource Manager.
- [Entender como funcionam os alertas de métrica no Azure Monitor.](platform/alerts-metric-overview.md) - Atualizado para suporte do governo.
- [Solucionar problemas em alertas e notificações do Azure Monitor](platform/alerts-troubleshoot.md): novo artigo

### <a name="application-insights"></a>Application Insights
- [Automatizar o Azure Application Insights com o PowerShell](app/powershell.md): exemplos do ARMClient adicionados.
- [Exportação contínua de telemetria do Application Insights](app/export-telemetry.md):adicionar tabela com detalhes da estrutura de exportação.
- [Habilitar Depurador de Instantâneos para aplicativos .NET no Serviço de Aplicativo do Azure](app/snapshot-debugger-appservice.md): exemplo de modelo do Resource Manager adicionado.
- [Gerenciar o uso e os custos do Azure Application Insights](app/pricing.md): informações adicionadas sobre o alerta de limite de dados.
- [Monitorar aplicativos Python com Azure Monitor (versão prévia)](app/opencensus-python.md): métricas padrão adicionadas.
- [Suporte ao source map para aplicativos JavaScript – Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support): novo artigo.


### <a name="containers"></a>Contêineres
- [Perguntas frequentes sobre o Azure Monitor](faq.md): atualizar para o Azure Monitor para contêineres.
- [Configurar o monitoramento de GPU com o Azure Monitor para contêineres](insights/container-insights-gpu-monitoring.md): novo artigo

### <a name="insights"></a>Insights
- [Solução de gerenciamento do Office 365 no Azure](insights/solution-office-365.md): atualização da data de reprovação.

### <a name="logs"></a>Logs
- [Otimizar consultas de log no Azure Monitor](log-query/query-optimization.md): condição de CPU adicionada para análise de XML e JSON.
- [Excluir e recuperar o workspace do Azure Log Analytics](platform/delete-workspace.md): adição de solução de problemas.
- [Usar os logs do Azure Monitor com os Aplicativos Lógicos do Azure e o Power Automate](platform/logicapp-flow-connector.md): atualizado para o novo conector do Azure Monitor.

### <a name="metrics"></a>Métricas
- [Reprovação de métricas de disco no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation): artigo novo.
- [Tutorial – Criar um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md): vídeo adicionado.

### <a name="platform-logs"></a>Logs de plataforma
- [Coletar e analisar o log de atividades do Azure no Azure Monitor](platform/activity-log-collect.md): reescrever para explicar melhor a coleta do log de atividades com as configurações de diagnóstico.

### <a name="virtual-machines"></a>Máquinas virtuais
- [Monitorar máquinas virtuais do Azure com o Azure Monitor](insights/monitor-vm-azure.md): novo artigo.
- [Início Rápido: Monitorar máquinas virtuais do Azure com o Azure Monitor](learn/quick-monitor-azure-vm.md): atualizado para adicionar o Azure Monitor para VMs.
- [Alertas do Azure Monitor para VMs](insights/vminsights-alerts.md): novo artigo
- [Visão geral de Habilitar o Azure Monitor para VMs](insights/vminsights-enable-overview.md): links atualizados para baixar agentes.


Atualizações gerais para disponibilidade geral do Azure Monitor para VMs
- [O que é o Azure Monitor para VMs?](insights/vminsights-overview.md)
- [Perguntas frequentes sobre o Azure Monitor para VMs (GA)](insights/vminsights-ga-release-faq.md) 
- [Habilitar o Azure Monitor para VMs usando o Azure Policy](insights/vminsights-enable-at-scale-policy.md) 
- [Como criar um gráfico de desempenho com o Azure Monitor para VMs](insights/vminsights-performance.md)
- [Como consultar logs do Azure Monitor para VMs](insights/vminsights-log-search.md)
- [Ver dependências de aplicativo com o Azure Monitor para VMs](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Visualizações
- [Visualização de dados do Azure Monitor](visualizations.md): atualizado para observar a reprovação planejada do Designer de Exibição.





## <a name="february-2020"></a>Fevereiro de 2020

### <a name="agents"></a>Agentes
Várias atualizações como parte da reformulação do conteúdo da extensão de diagnóstico.

- [Visão geral dos agentes de monitoramento do Azure](platform/agents-overview.md): tabelas reestruturadas para melhor esclarecimento dos recursos exclusivos de cada agente.
- [Visão geral da extensão de Diagnóstico do Azure](platform/diagnostics-extension-overview.md): reformulação completa.
- [Usar o armazenamento de blobs para o IIS e o armazenamento de tabelas para eventos no Azure Monitor](platform/diagnostics-extension-logs.md): reformulação geral para fins de atualização e clareza.
- [Instalar e configurar a extensão do WAD (Diagnóstico do Azure para Windows)](platform/diagnostics-extension-windows-install.md): novo artigo. 
- [Esquema de extensão de diagnóstico do Windows](platform/diagnostics-extension-schema-windows.md): reorganizado.
- [Enviar dados da extensão de diagnóstico do Azure para Windows para os Hubs de Eventos do Azure](platform/diagnostics-extension-stream-event-hubs.md): reformulação e atualização completas.
- [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](/azure/cloud-services/diagnostics-extension-to-storage): reformulação e atualização completas.
- [Extensão da máquina virtual do Log Analytics para Windows](../virtual-machines/extensions/oms-windows.md): melhor esclarecimento da relação com o agente do Log Analytics.
- [Extensão da máquina virtual do Azure Monitor para Linux](../virtual-machines/extensions/oms-linux.md): melhor esclarecimento da relação com o agente do Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Cadeias de conexão no Azure Application Insights](app/sdk-connection-string.md): novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contêineres
- [Integrar o Azure Active Directory com o Serviço de Kubernetes do Azure](../aks/azure-ad-integration.md): adição de observação para criar um aplicativo cliente a fim de dar suporte ao cluster habilitado para RBAC em apoio ao Azure Monitor para contêineres.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
- [Perguntas frequentes sobre o Azure Monitor para VMs (disponibilidade geral)](insights/vminsights-ga-release-faq.md): alteração da forma como os dados de desempenho são armazenados.

#### <a name="office-365"></a>Office 365
- [Solução de gerenciamento do Office 365 no Azure](insights/solution-office-365.md): atualização da data de reprovação.


### <a name="logs"></a>Logs
- [Otimizar consultas de log no Azure Monitor](log-query/query-optimization.md): novo artigo.
- [Gerenciar o uso e os custos de logs do Azure Monitor](platform/manage-cost-storage.md): melhoria das consultas de exemplo para ajudar a entender o uso.

### <a name="metrics"></a>Métricas
- [Métricas de plataforma do Azure Monitor exportáveis por meio das Configurações de Diagnóstico](platform/metrics-supported-export-diagnostic-settings.md): adição de uma seção sobre a alteração de comportamento para valores nulos e zero.


### <a name="visualizations"></a>Visualizações
Vários novos artigos sobre o guia de conversão do designer de exibição para as pastas de trabalho.

- [Guia de transição do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-overview.md): novo artigo.
- [Opções de conversão do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-options.md): novo artigo.
- [Conversões de bloco do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-tiles.md): novo artigo.
- [Resumo e acesso do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-access.md): novo artigo.
- [Tarefas comuns do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-tasks.md): novo artigo.
- [Exemplos de conversão do designer de exibição para as pastas de trabalho do Azure Monitor](platform/view-designer-conversion-examples.md): novo artigo.




## <a name="january-2020"></a>Janeiro de 2020

### <a name="general"></a>Geral
- [O que é monitorado pelo Azure Monitor?](monitor-reference.md) – novo artigo.

### <a name="agents"></a>Agentes
- [Coletar dados de log do agente do Azure Log Analytics](platform/log-analytics-agent.md): atualização da tabela de requisitos do firewall de rede.


### <a name="alerts"></a>Alertas
- [Criar e gerenciar grupos de ações no portal do Azure](platform/action-groups.md): remoção de configuração não mais necessária do Functions v2.
- [Criar um alerta de métrica com um modelo do Resource Manager](platform/alerts-metric-create-templates.md): adição de exemplo para o parâmetro *ignoreDataBefore*.  Adição de restrições sobre regras de vários critérios.
- [Como usar a API REST de Alerta do Log Analytics](platform/api-alerts.md): correção de exemplo JSON.


### <a name="application-insights"></a>Application Insights
- [Endereços IP usados pelo Application Insights e pelo Log Analytics](app/ip-addresses.md): atualização da seção Teste de disponibilidade com Como adicionar uma regra de porta de entrada para permitir o tráfego usando grupos de segurança de rede do Azure.
- [Solução de problemas com o Azure Application Insights Profiler](app/profiler-troubleshooting.md): atualização da solução de problemas geral.
- [Amostragem de telemetria no Azure Application Insights](app/sampling.md): atualização e reestruturação para aprimorar a legibilidade de acordo com os comentários dos clientes.


### <a name="data-security"></a>Segurança de dados
- [Configuração da chave gerenciada pelo cliente do Azure Monitor](platform/customer-managed-keys.md): novo artigo.

### <a name="insights-and-solutions"></a>Insights e soluções

#### <a name="azure-monitor-for-containers"></a>Azure Monitor para Contêineres
- [Configurar o Azure Monitor para coleta de dados do agente de contêineres](insights/container-insights-agent-config.md): adição de detalhes sobre atualização do agente no Red Hat OpenShift no Azure e de informações adicionais para distinguir os métodos de atualização do agente.
- [Criar alertas de desempenho do Azure Monitor para contêineres](insights/container-insights-alerts.md): revisão de informações e atualização de etapas para criar um alerta sobre os dados de desempenho armazenados no workspace usando alertas de contexto do workspace.
- [Monitoramento do Kubernetes com o Azure Monitor para contêineres](insights/container-insights-analyze.md): atualização do artigo de visão geral e do artigo de análise sobre o suporte de clusters do Windows Kubernetes.
- [Configurar clusters do Red Hat OpenShift no Azure com o Azure Monitor para contêineres](insights/container-insights-azure-redhat-setup.md): adição de detalhes sobre atualização do agente no Red Hat OpenShift do Azure e de informações adicionais para distinguir os métodos de atualização do agente.
- [Configurar clusters Kubernetes Híbridos com o Azure Monitor para contêineres](insights/container-insights-hybrid-setup.md): atualização para refletir o suporte adicionado para a porta segura 10250 com o cAdvisor do Kubelet.
- [Como gerenciar o agente do Azure Monitor para contêineres](insights/container-insights-manage-agent.md): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.
- [Configurar o Azure Monitor para contêineres: integração ao Prometheus](insights/container-insights-prometheus-integration.md): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.
- [Como atualizar o Azure Monitor para contêineres para métricas](insights/container-insights-update-metrics.md): atualização de detalhes relacionados ao comportamento e à configuração do recorte de métrica com o Red Hat OpenShift no Azure comparado a outros tipos de clusters Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
- [Perguntas frequentes sobre o Azure Monitor para VMs (GA)](insights/vminsights-ga-release-faq.md): informações sobre como atualizar o workspace e os agentes para a nova versão.

#### <a name="office-365"></a>Office 365
- [Solução de gerenciamento do Office 365 no Azure](insights/solution-office-365.md): adição de detalhes e perguntas frequentes sobre a migração para a solução do Office 365 no Azure Sentinel. Remoção da seção sobre integração.



### <a name="logs"></a>Logs
- [Gerenciar workspaces do Log Analytics no Azure Monitor](platform/manage-access.md): atualizações de Não ações.
- [Gerenciar o uso e os custos dos logs do Azure Monitor](platform/manage-cost-storage.md): adição de esclarecimento sobre o cálculo do volume de dados na seção Modelo de preços.
- [Usar modelos do Azure Resource Manager para criar e configurar um workspace do Log Analytics](platform/template-workspace-configuration.md): atualização de modelo com novos tipos de preço.


### <a name="platform-logs"></a>Logs de plataforma
- [Coletar o log de atividades do Azure com as configurações de diagnóstico – Azure Monitor](platform/diagnostic-settings-legacy.md): informações adicionais sobre as propriedades alteradas.
- [Exportar o log de atividades do Azure](platform/activity-log-export.md): atualização para as alterações na interface do usuário. 





## <a name="december-2019"></a>Dezembro de 2019

### <a name="agents"></a>Agentes
- [Conectar computadores Linux ao Azure Monitor](platform/agent-linux.md): novo artigo.

### <a name="alerts"></a>Alertas
- [Criar um alerta de métrica com um modelo do Resource Manager](platform/alerts-metric-create-templates.md): adição de exemplo de métrica personalizada.
- [Como criar alertas com limites dinâmicos no Azure Monitor](platform/alerts-dynamic-thresholds.md): adição de seção sobre como interpretar gráficos de limites dinâmicos.
- [Visão geral do monitoramento de alertas e notificações no Azure](platform/alerts-overview.md): atualização de consulta do Resource Graph.
- [Recursos compatíveis com alertas de métrica no Azure Monitor](platform/alerts-metric-near-real-time.md): atualização de métricas e dimensões compatíveis.
- [Alternar a API de alertas herdada do Log Analytics para a nova API de Alertas do Azure](platform/alerts-log-api-switch.md): adição de observação sobre o nome de alerta modificado.
- [Entender como funcionam os alertas de métrica no Azure Monitor.](platform/alerts-metric-overview.md) : adição de tipos de recursos compatíveis com o monitoramento em escala.

### <a name="application-insights"></a>Application Insights
- [Application Insights para aplicativos do Worker Service (aplicativos não HTTP)](app/worker-service.md): adição do nível de registros em log padrão ao código C#. Atualização da versão de referência do pacote.
- [Referência de ApplicationInsights.config – Azure](app/configuration-with-applicationinsights-config.md): atualização de código de exemplo.
- [Automatizar o Azure Application Insights com o PowerShell](app/powershell.md): atualização do modelo do Resource Manager.
- [Pacotes NuGet do Application Insights do Azure Monitor](app/nuget.md): atualização de versões de pacote.
- [Criar um recurso do Azure Application Insights](app/create-new-resource.md): adição de observação ao nome global exclusivo.
- [Diagnóstico com o Live Metrics Stream – Azure Application Insights](app/live-stream.md): atualização do requisito de versão do SDK do ASP.NET Core.
- [Contadores de eventos no Application Insights](app/eventcounters.md): atualização de categoria e tabela para customMetrics.
- [Explorar os logs de rastreamento do Java no Azure Application Insights](app/java-trace-logs.md): adição de configuração do limite de log do agente Java.
- [Endereços IP usados pelo Application Insights e pelo Log Analytics](app/ip-addresses.md): atualização de endereços IP do Live Metrics Stream.
- [Monitorar o desempenho dos Serviços de Aplicativos do Azure](app/azure-web-apps.md): adição de suporte para o ASP.NET Core 3.0. 
- [Monitorar aplicativos Python com o Azure Monitor (versão prévia)](app/opencensus-python.md): adição de esclarecimento sobre o mapeamento de esquema do OpenCensus Python para o esquema do Azure Monitor
- [Notas sobre a versão do Azure Application Insights](app/release-notes.md): adição de observações para versões mais antigas.
- [Canais de telemetria no Azure Application Insights](app/telemetry-channels.md): atualização da duração de dados descartados durante o período estendido de conexão perdida.
- [Amostragem de telemetria no Azure Application Insights](app/sampling.md): correção de snippet de código de TelemetryInitializer personalizado.
- [Solução de problemas do Application Insights em um projeto Web Java](app/java-troubleshoot.md): remoção da instrução sobre o não suporte à coleta de dependências no JDK 9.

### <a name="insights-and-solutions"></a>Insights e soluções
- [Perguntas frequentes sobre o Azure Monitor para contêineres](insights/container-insights-faq.md): adição de pergunta sobre os campos Imagem e Nome.
- [Solução Análise de SQL do Azure no Azure Monitor](insights/azure-sql.md): atualização do suporte de Instância Gerenciada em Esperas do banco de dados.
- [Configurar o Azure Monitor para coleta de dados do agente de contêineres](insights/container-insights-agent-config.md): adição de configuração para enrich_container_logs.
- [Configurar clusters Kubernetes Híbridos com o Azure Monitor para contêineres](insights/container-insights-hybrid-setup.md): adição da seção de solução de problemas.
- [Monitorar o status de replicação do Active Directory com o Azure Monitor](insights/ad-replication-status.md): atualização de pré-requisito do .NET Framework.
- [Solução Monitor de Desempenho de Rede no Azure](insights/network-performance-monitor.md): adição de regiões com suporte.
- [Otimizar seu ambiente do Active Directory com o Azure Monitor](insights/ad-assessment.md): atualização de pré-requisito do .NET Framework.
- [Otimizar seu ambiente do SQL Server com o Azure Monitor](insights/sql-assessment.md): atualização de pré-requisito do .NET Framework.
- [Otimizar seu ambiente do System Center Operations Manager com o Azure Log Analytics](insights/scom-assessment.md): atualização de pré-requisito do .NET Framework.
- [Conexões compatíveis com o Conector de Gerenciamento de Serviços de TI no Azure Log Analytics](platform/itsmc-connections.md): adição de Nova York aos pré-requisitos de ID do cliente e segredo do cliente.

### <a name="logs"></a>Logs
- [Excluir e recuperar o workspace do Azure Log Analytics](platform/delete-workspace.md): adição de método do PowerShell.
- [Como criar sua implantação de logs do Azure Monitor](platform/design-logs-deployment.md): aumento da taxa de ingestão para um workspace.

### <a name="metrics"></a>Métricas
- [Métricas de plataforma do Azure Monitor exportáveis por meio de Configurações de Diagnóstico](platform/metrics-supported-export-diagnostic-settings.md): novo artigo.

### <a name="platform-logs"></a>Logs de plataforma
Atualização de vários artigos como parte da reestruturação de conteúdo dos logs de plataforma de acordo com o novo recurso de configuração do log de atividades usando as configurações de diagnóstico.

- [Arquivar logs de recursos do Azure na conta de armazenamento](platform/resource-logs-collect-storage.md)
- [Esquema de eventos do log de atividades do Azure](platform/activity-log-schema.md)
- [Limites do serviço Azure Monitor](service-limits.md)
- [Coletar e analisar os logs de atividades do Azure no workspace do Log Analytics](platform/activity-log-collect.md)
- [Coletar o log de atividades do Azure com as configurações de diagnóstico (versão prévia) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Coletar os logs de atividades do Azure em um workspace do Log Analytics nos locatários do Azure](platform/activity-log-collect-tenants.md)
- [Coletar os logs de recursos do Azure no workspace do Log Analytics](platform/resource-logs-collect-workspace.md)
- [Criar uma configuração de diagnóstico no Azure usando o modelo do Resource Manager](platform/diagnostic-settings-template.md)
- [Criar uma configuração de diagnóstico para coletar logs e métricas no Azure](platform/diagnostic-settings.md)
- [Exportar o log de atividades do Azure](platform/activity-log-export.md)
- [Visão geral dos logs de plataforma do Azure](platform/platform-logs-overview.md)
- [Transmitir dados de monitoramento do Azure para um hub de eventos](platform/stream-monitoring-data-event-hubs.md)
- [Transmitir logs de plataforma do Azure para um hub de eventos](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Inícios Rápidos e tutoriais

- [Criar um gráfico de métricas no Azure Monitor](learn/tutorial-metrics-explorer.md): novo artigo.
- [Coletar logs de recursos de um recurso do Azure e analisá-los com o Azure Monitor](learn/tutorial-resource-logs.md): novo artigo.
- [Monitorar um recurso do Azure com o Azure Monitor](learn/quick-monitor-azure-resource.md): novo artigo.
   
## <a name="next-steps"></a>Próximas etapas

- Caso deseje enviar sua contribuição para a documentação do Azure Monitor, confira o [Guia do colaborador do Docs](https://docs.microsoft.com/contribute/).