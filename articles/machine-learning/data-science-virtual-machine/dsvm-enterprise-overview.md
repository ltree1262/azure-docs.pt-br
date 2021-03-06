---
title: Ambiente de IA e análise de equipe
titleSuffix: Azure Data Science Virtual Machine
description: Padrões para implantar a VM de Ciência de Dados em um ambiente de equipe corporativo.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 03fdbf6979db3249d1322a3025b48de81b953ae1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856220"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente de IA e análise de equipe com base em Máquina Virtual de Ciência de Dados 
A [DSVM](overview.md) (Máquina Virtual de Ciência de Dados) fornece um ambiente avançado na plataforma Azure com o software predefinido de IA (inteligência artificial) e análise de dados.

Tradicionalmente, a DSVM era usada como uma área de trabalho de análise individual. Os cientistas de dados individuais obtêm produtividade com esse ambiente de análise pré-criado. À medida que as equipes de análise de grande porte planejam ambientes para os respectivos cientistas de dados e desenvolvedores de IA, um dos temas recorrentes é uma infraestrutura compartilhada de análise para experimentação e desenvolvimento. Essa infraestrutura é gerenciada de acordo com as políticas de TI da empresa, que também facilita a colaboração e a consistência entre as equipes de ciência de dados e análise.

Uma infraestrutura compartilhada também permite que a TI utilize melhor o ambiente de análise. Algumas organizações chamam a infraestrutura de ciência/análise de dados com base em equipe de *área restrita de análise*. Ela permite que os cientistas de dados acessem vários ativos de dados para compreender os dados rapidamente. Esse ambiente de área restrita também ajuda os cientistas de dados a executar experiências rapidamente, validar hipóteses e criar modelos de previsão sem afetar o ambiente de produção.

Como a DSVM opera no nível de infraestrutura do Azure, os administradores de TI podem configurar prontamente a DSVM para operar em conformidade com as políticas de TI da empresa. A DSVM oferece total flexibilidade na implementação de várias arquiteturas de compartilhamento, oferecendo também acesso a ativos de dados corporativos de uma forma controlada.

Esta seção discute alguns padrões e diretrizes que você pode usar para implantar a DSVM como uma infraestrutura de ciência de dados baseada em equipe. Já que os blocos de construção desses padrões são provenientes da IaaS (infraestrutura como serviço) do Azure, eles se aplicam a qualquer VM do Azure. O foco desta série de artigos é sobre como aplicar essas funcionalidades de infraestrutura padrão do Azure na DSVM.

Os principais blocos de construção de um ambiente corporativo de análise de equipe incluem:

* [Um pool de DSVMs dimensionado automaticamente](dsvm-pools.md)
* [Identidade e acesso comum ao workspace de qualquer uma das DSVMs no pool](dsvm-common-identity.md)
* [Acesso seguro a fontes de dados](dsvm-secure-access-keys.md)


Esta série fornece diretrizes e indicações para cada um dos tópicos anteriores. Isso não abrange todas as considerações e os requisitos de implantação da DSVM em configurações de grande porte. Aqui estão alguns outros recursos do Azure que você pode usar durante a implementação de instâncias DSVM em sua empresa:

* [Segurança de rede](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitoramento](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gerenciamento](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Log e auditoria](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Configuração da política e execução](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Criptografia](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [Descoberta de dados e governança](https://docs.microsoft.com/azure/data-catalog/)

Por fim, o [Centro de Arquitetura do Azure](https://docs.microsoft.com/azure/architecture/) fornece modelos e arquitetura detalhada de ponta a ponta para criar e gerenciar sua infraestrutura de análise baseado em nuvem.