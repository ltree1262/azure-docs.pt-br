---
title: Ferramentas comunitárias - Mova recursos clássicos para o Azure Resource Manager
description: Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para ajudar a migrar os recursos de IaaS do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 9839f411458eeb4fd071177ec8208baa94dca3a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866148"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Ferramentas da comunidade para migrar recursos de IaaS do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs IaaS estão usando [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente aposentadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa depreciação e [como ela afeta você](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Este artigo cataloga as ferramentas que foram fornecidas pela comunidade para auxiliar com a migração dos recursos de IaaS do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager.

> [!NOTE]
> Não há suporte oficial para essas ferramentas no Suporte da Microsoft. Portanto, são software livre no GitHub e aceitamos PRs para correções ou cenários adicionais. Para relatar um problema, use o recurso de problemas do GitHub.
> 
> A migração com essas ferramentas causará tempo de inatividade de sua Máquina Virtual clássica. Se você estiver buscando uma migração da plataforma com suporte, visite 
> 
>   * [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack (Migração de recursos de IaaS com suporte da plataforma da pilha Clássica para o Azure Resource Manager)](migration-classic-resource-manager-overview.md)
>   * [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Trata-se de uma coleção de ferramentas auxiliares criadas como parte de migrações empresariais do Gerenciamento de Serviços do Azure para o Azure Resource Manager. Essa ferramenta permite replicar sua infraestrutura para outra assinatura, o que pode ser usado para testar a migração e solucionar problemas antes de executar a migração em sua assinatura de produção.

[Link para a documentação da ferramenta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz é uma opção adicional para migrar um conjunto completo de recursos de IaaS clássicos para recursos de IaaS do Azure Resource Manager. A migração pode ocorrer na mesma assinatura ou entre assinaturas e tipos de assinatura diferentes (por ex.: assinaturas do CSP).

[Link para a documentação da ferramenta](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planejamento da migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

