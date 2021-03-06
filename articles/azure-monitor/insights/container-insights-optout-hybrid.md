---
title: Como parar de monitorar o cluster kubernetes híbrido | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster kubernetes híbrido com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196212"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Como parar de monitorar o cluster híbrido

Depois de habilitar o monitoramento do cluster kubernetes em execução no Azure Stack ou no local, você poderá interromper o monitoramento do cluster com Azure Monitor para contêineres se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso.  

## <a name="how-to-stop-monitoring-using-helm"></a>Como interromper o monitoramento usando o Helm

1. Para identificar primeiro o Azure Monitor para contêineres Helm versão do gráfico instalado no cluster, execute o seguinte comando Helm.

    ```
    helm list
    ```

    A saída será semelhante à seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-containers-Release-1* representa a versão do gráfico de helm para Azure monitor para contêineres.

2. Para excluir a versão do gráfico, execute o comando Helm a seguir.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isso removerá a versão do cluster. Você pode verificar executando o `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração da configuração pode levar alguns minutos para ser concluída. Como o Helm rastreia suas versões mesmo depois de excluí-las, você pode auditar o histórico de um cluster e até mesmo cancelar a `helm rollback`exclusão de uma versão com.

## <a name="next-steps"></a>Próximas etapas

Se o espaço de trabalho Log Analytics foi criado apenas para dar suporte ao monitoramento do cluster e ele não for mais necessário, você precisará excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
