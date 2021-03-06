---
title: Resumo de recomendações para o Azure Advisor
description: Obtenha Resumo periódico para suas recomendações ativas
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502458"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configurar o resumo periódico para recomendações

Os **resumos de recomendação** do Advisor fornecem uma maneira fácil e proativa de se manter sobre suas recomendações ativas, em diferentes categorias. O recurso fornece a capacidade de configurar notificações periódicas para o resumo de todas as suas recomendações ativas em diferentes categorias. Você pode escolher o canal desejado para notificações como email, SMS ou outros, usando grupos de ação. Este artigo mostra como configurar uma **síntese de recomendação** para suas recomendações do Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Configurando seu resumo de recomendações 

A experiência de criação do **Resumo de recomendação** ajuda a configurar o resumo. Você pode selecionar os parâmetros abaixo para as configurações:
1. Categoria: temos categorias de recomendação como custo, alta disponibilidade, desempenho e excelência operacional. A funcionalidade ainda não está disponível para recomendações de segurança.
2. Frequência do Resumo: a frequência das notificações de resumo pode ser semanal, quinzenal e mensal.
3. Grupo de ações: você pode selecionar um grupo de ações existente ou criar um novo grupo de ação. Para saber mais sobre grupos de ações, consulte [criar e gerenciar grupos de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Idioma para o resumo
5. Nome do Resumo de recomendação: você pode usar uma cadeia de caracteres amigável para acompanhar e monitorar melhor os resumos.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Etapas para criar um resumo de recomendação no portal do Azure

Estas são as etapas para criar um **Resumo de recomendação:**
* **Etapa 1:** Na portal do Azure, acesse o **Advisor** e, na seção **monitoramento** , selecione **Resumo de recomendação** 

   ![Ponto de entrada de Resumo de recomendação](./media/digest-0.png)

* **Etapa 2:** Selecione **novo Resumo de recomendação** na barra superior, como abaixo:

   ![Criar Resumo de recomendação](./media/digest-5.png)

* **Etapa 3:** Na seção **escopo** , selecione a **assinatura** para seu resumo

   ![Fornecer entradas de Resumo de recomendação](./media/digest-1.png)

* **Etapa 4:** Na seção **condição** , selecione as configurações, como **categoria**, **frequência** e **idioma**

   ![Fornecer condições de entrada de Resumo de recomendação](./media/digest-2.png)

* **Etapa 5:** Na seção **grupo de ações** , selecione o **grupo de ações** para o resumo. Você pode aprender mais aqui – [criar e gerenciar grupos de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Fornecer grupo de ações de entrada de Resumo de recomendação](./media/digest-3.png)

* **Etapa 6:** Nesta seção final para obter **detalhes de resumo**, você pode atribuir o nome e o estado ao seu resumo de recomendação. Pressione **criar Resumo de recomendação** para concluir a configuração.
   ![Criação de Resumo de recomendação completa](./media/digest-4.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
* [API REST do Advisor](https://docs.microsoft.com/rest/api/advisor/)
