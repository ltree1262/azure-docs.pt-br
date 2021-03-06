---
title: Hybrid Runbook Worker do Linux de Automação do Azure
description: Este artigo fornece informações sobre como instalar um Hybrid Runbook Worker da Automação do Azure para que você possa executar runbooks em computadores Linux no seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 82f6d9e56e5d5745077ef512cb3392c16b95961f
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872174"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implantar o Hybrid Runbook Worker do Linux

É possível usar o recurso Hybrid Runbook Worker da Automação do Azure para executar os runbooks diretamente no computador que hospeda a função e os recursos do ambiente para gerenciar esses recursos locais. O Linux Hybrid Runbook Worker executa runbooks como um usuário especial que pode ser elevado para executar comandos que precisam de elevação. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados.

Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Linux, como remover o trabalho e como remover um grupo de Hybrid Runbook Worker.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

O recurso Hybrid Runbook Worker dá suporte para as distribuições a seguir:

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12, 4 LTS, 14, 4 LTS, 16, 4 LTS e 18, 4 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

Os Hybrid Runbook Workers do Linux não dão suporte ao conjunto completo dos tipos de runbook na Automação do Azure.

Os seguintes tipos de runbooks funcionam em um Hybrid Worker do Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Runbooks do PowerShell exigem que o PowerShell Core seja instalado no computador Linux. Consulte [Instalar PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo.

Os seguintes tipos de runbook não funcionam em um Linux Hybrid Worker:

* Fluxo de trabalho do PowerShell
* Gráfico
* Fluxo de Trabalho Gráfico do PowerShell

## <a name="deployment-requirements"></a>Requisitos de implantação

Os requisitos mínimos para um Hybrid Runbook Worker do Linux são:

* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1,0 (há suporte para TLS 1,1 e TLS 1,2)|
|Curl | cliente Web cURL | 7.15.5|
|Python-ctypes | O Python 2. x é obrigatório |
|PAM | Módulos de autenticação conectáveis|
| **Pacotes opcionais** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar runbooks do PowerShell é necessário instalar o PowerShell, consulte [Instalar o PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para saber como instalá-lo.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalar um Hybrid Runbook Worker do Linux

Para instalar e configurar um Hybrid Runbook Worker em seu computador Linux, siga um processo manual simples. Requer a habilitação da solução Hybrid Worker de Automação no espaço de trabalho do Log Analytics do Azure e, em seguida, a execução de um conjunto de comandos para registrar o computador como um trabalhador e adicioná-lo a um grupo.

Antes de prosseguir, observe o espaço de trabalho do Log Analytics ao qual sua conta de Automação está vinculada. Observe também a chave primária da conta de Automação. É possível localizar ambos do portal do Azure, selecionando a conta de Automação, selecionando o **Workspace** para a ID do workspace e, selecionando **Chaves** para a chave primária. Para obter informações sobre portas e endereços necessários para o Hybrid Runbook Worker, consulte [Configurar sua rede](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> A [conta nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões sudo correspondentes deve estar presente durante a instalação do Hybrid Worker Linux. Se você tentar instalar o trabalho e a conta não estiver presente ou não tiver as permissões apropriadas, a instalação falhará.

1. Habilite a solução Hybrid Worker de Automação no Azure usando um dos métodos a seguir:

   * Adicione a solução Hybrid Worker de Automação à sua assinatura usando o procedimento em [Adicionar soluções de log Azure monitor ao seu espaço de trabalho](../log-analytics/log-analytics-add-solutions.md).
   * Execute o cmdlet a seguir:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instale o Agente do Log Analytics para Linux, executando o comando a seguir. Substitua \<WorkspaceID\> e \<WorkspaceKey\> pelos valores apropriados do workspace.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Execute o seguinte comando, alterando os valores dos parâmetros *-w*, *-k*, *-g*, e *-e*. Para o parâmetro *-g* , substitua o valor pelo nome do grupo de Hybrid runbook Worker ao qual o novo Hybrid runbook Worker Linux deve ingressar. Se o nome não existir na sua conta de Automação, um novo grupo de executável de manual híbrido será criado com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Depois que o comando for concluído, a página Grupos do Hybrid Worker no portal do Azure mostrará o novo grupo e o número de membros. Se este for um grupo existente, o número de membros será incrementado. Você pode selecionar o grupo da lista na página Grupos do Hybrid Worker e no bloco **Hybrid Workers**. Na página Hybrid Workers, você verá cada membro do grupo listado.

> [!NOTE]
> Se você estiver usando a extensão de máquina virtual Azure Monitor para Linux para uma VM do Azure, `autoUpgradeMinorVersion` é recomendável definir como false, pois as versões de atualização automática podem causar problemas na Hybrid runbook Worker. Para saber como atualizar a extensão manualmente, consulte [CLI do Azure implantação](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desativar a validação de assinatura

Por padrão, o Linux Hybrid Runbook Workers exige validação de assinatura. Se você executar um runbook não assinado em um trabalho, verá um `Signature validation failed` erro. Para desativar a validação de assinatura, execute o comando a seguir. Substitua o segundo parâmetro pela ID do espaço de trabalho do Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker de um computador Linux local

Você pode usar o comando `ls /var/opt/microsoft/omsagent` no Hybrid runbook Worker para obter a ID do espaço de trabalho. É criada uma pasta que é nomeada com a ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Esse código não remove o agente de Log Analytics para Linux do computador. Ele apenas remove a funcionalidade e a configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo de Hybrid Runbook Worker de computadores Linux, use as mesmas etapas de um grupo do Hybrid Worker do Windows. Consulte [remover um grupo de Hybrid Worker](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus Hybrid Runbook Workers, consulte [Solucionar Problemas de Linux Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#linux)
