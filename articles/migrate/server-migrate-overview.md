---
title: Selecione uma opção de migração VMware com migração de servidor migrações para Azure | Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMs VMware para o Azure com migração de servidor de migrações para Azure
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76028721"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração do VMware

É possível migrar VMs VMware para o Azure usando a ferramenta de Migração de Servidor de Migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.




## <a name="compare-migration-methods"></a>Comparar métodos de migração

Use essas comparações selecionadas para ajudá-lo a decidir qual método usar. Você também pode examinar os requisitos de suporte completo para migração [baseada em](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) agente e sem [agentes](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) .

**Configuração** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Permissões do Azure** | Você precisa de permissões para criar um projeto de migrações para Azure e registrar os aplicativos do Azure AD criados ao implantar o dispositivo de migrações para Azure. | Você precisa de permissões de colaborador na assinatura do Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente de um vCenter Server.<br/> Se você tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais em uma única vez afetará o desempenho. | NA
**Implantação de dispositivo** | O [dispositivo migrações para Azure](migrate-appliance.md) é implantado localmente. | O [dispositivo de replicação de migrações para Azure](migrate-replication-appliance.md) é implantado localmente.
**Compatibilidade Site Recovery** | Compatíveis. | Você não poderá replicar com a migração de servidor de migrações para Azure se tiver configurado a replicação para um computador usando Site Recovery.
**Disco de destino** | Discos gerenciados | Discos gerenciados
**Limites de disco** | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Máximo de discos: 60 | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Máximo de discos: 63
**Discos de passagem** | Sem suporte | Com suporte
**Inicialização UEFI** | Sem suporte | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de revisar as limitações, entender as etapas envolvidas na implantação de cada solução pode ajudá-lo a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agente**
--- | --- | --- | ---
**Avaliação** | Avalie servidores antes da migração.  A avaliação é opcional. Sugerimos que você avalie as máquinas antes de migrá-las, mas não é necessário. <br/><br/> Para avaliação, as migrações para Azure configuram um dispositivo leve para descobrir e avaliar as VMs. | Se você executar uma migração sem agente após a avaliação, o mesmo dispositivo de migração do Azure configurado para avaliação será usado para a migração sem agente.  |  Se você executar uma migração baseada em agente após a avaliação, a configuração do dispositivo para avaliação não será usada durante a migração sem agente. Você pode deixar o dispositivo em vigor ou removê-lo se não quiser fazer mais descoberta e avaliação.
**Preparar servidores VMware e VMs para migração** | Defina várias configurações em servidores VMware e VMs. | Obrigatório | Obrigatório
**Adicionar a ferramenta de migração de servidor** | Adicione a ferramenta de migração de servidor de migrações para Azure no projeto de migrações para Azure. | Obrigatório | Obrigatório
**Implantar o dispositivo de migrações para Azure** | Configure um dispositivo leve em uma VM VMware para avaliação e descoberta de VM. | Obrigatório | Não obrigatório.
**Instalar o serviço de mobilidade em VMs** | Instalar o serviço de mobilidade em cada VM que você deseja replicar | Não obrigatório | Obrigatório
**Implantar o dispositivo de replicação de migração de servidor de migrações para Azure** | Configurar um dispositivo em uma VM VMware para descobrir VMs e fazer a ponte entre o serviço de mobilidade em execução em VMs e migração de servidor de migrações para Azure | Não obrigatório | Obrigatório
**Replicar VMs**. Habilitar a replicação de VM. | Definir as configurações de replicação e selecionar as VMs a serem replicadas | Obrigatório | Obrigatório
**Execute um teste de migração** | Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado. | Obrigatório | Obrigatório
**Executar uma migração completa** | Migre as VMs. | Obrigatório | Obrigatório




## <a name="next-steps"></a>Próximas etapas

[Migre VMs VMware](tutorial-migrate-vmware.md) com migração sem agente.



