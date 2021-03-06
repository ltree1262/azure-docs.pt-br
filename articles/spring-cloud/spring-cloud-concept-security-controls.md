---
title: Conceito-controles de segurança para o serviço de nuvem do Azure Spring
description: Use controles de segurança internos no serviço de nuvem Spring do Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594977"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controles de segurança para o serviço de nuvem do Azure Spring
Os controles de segurança são internos no serviço de nuvem Spring do Azure.

Um controle de segurança é uma qualidade ou um recurso de um serviço do Azure que contribui para a capacidade do serviço de prevenir, detectar e responder a vulnerabilidades de segurança.  Para cada controle, usamos *Sim* ou *não* para indicar se ele está em vigor no momento para o serviço.  Usamos *N/A* para um controle que não é aplicável ao serviço. 

**Controles de segurança de proteção de dados**

| Controle de segurança | Sim/Não | Anotações | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O usuário carregou a origem e os artefatos, as configurações do servidor de configuração, as configurações do aplicativo e os dados no armazenamento persistente são armazenados no armazenamento do Azure, o que criptografa automaticamente o conteúdo em repouso.<br><br>Cache do servidor de configuração, binários de tempo de execução criados a partir da origem carregada e os logs de aplicativo durante o tempo de vida do aplicativo são salvos no disco gerenciado do Azure, que criptografa automaticamente o conteúdo em repouso.<br><br>As imagens de contêiner criadas a partir da fonte carregada pelo usuário são salvas no registro de contêiner do Azure, que criptografa automaticamente o conteúdo da imagem em repouso. | [Criptografia do Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Criptografia do lado do servidor de Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Armazenamento de imagens de contêiner no Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Criptografia em transitório | Sim | Os pontos de extremidade públicos do aplicativo do usuário usam HTTPS para o tráfego de entrada por padrão. |  |
| Chamadas criptografadas à API | Sim | As chamadas de gerenciamento para configurar o serviço de nuvem Spring do Azure ocorrem por meio de chamadas Azure Resource Manager por HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

