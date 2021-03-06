---
title: Carregamento em massa para adicionar ou criar membros de um grupo-Azure Active Directory | Microsoft Docs
description: Adicione membros do grupo em massa no centro de administração do Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8902c3147bbe142fc58d4e2c3fa83601c8ccbba3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203499"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Importar membros do grupo em massa no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode adicionar um grande número de membros a um grupo usando um arquivo CSV (valores separados por vírgula) para importar membros do grupo em massa.

## <a name="understand-the-csv-template"></a>Entender o modelo CSV

Baixe e preencha o modelo CSV de carregamento em massa para adicionar com êxito os membros do grupo do Azure AD em massa. Seu modelo CSV pode ser semelhante a este exemplo:

![Planilha para upload e limites de chamada que explicam a finalidade e os valores de cada linha e coluna](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas em um modelo CSV baixado são as seguintes:

- **Número de versão**: a primeira linha que contém o número de versão deve ser incluída no CSV de carregamento.
- **Títulos de coluna** &lt;: o formato dos títulos de coluna é o *nome* &gt; do item [PropertyName &lt;] *necessário ou em branco*&gt;. Por exemplo, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Algumas versões mais antigas do modelo podem ter pequenas variações. Para alterações de associação de grupo, você tem a opção de qual identificador usar: ID de objeto de membro ou nome principal de usuário.
- **Linha de exemplos**: incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Você deve remover a linha de exemplos e substituí-la por suas próprias entradas.

### <a name="additional-guidance"></a>Diretriz adicional

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas ou o carregamento não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Todas as colunas adicionais adicionadas são ignoradas e não processadas.
- Recomendamos que você baixe a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-import-group-members"></a>Para importar membros do grupo em massa

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem importar em massa membros de grupos que possuem.
1. No Azure AD, selecione **grupos** > **todos os grupos**.
1. Abra o grupo ao qual você está adicionando Membros e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **importar Membros**.
1. Na página **membros do grupo de importação em massa** , selecione **baixar** para obter o modelo de arquivo CSV com as propriedades necessárias do membro do grupo.

    ![O comando importar Membros está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que você deseja importar para o grupo (os valores necessários são **ID de objeto de membro** ou **nome UPN**). Em seguida, salve o arquivo.

   ![O arquivo CSV contém nomes e IDs para os membros a serem importados](./media/groups-bulk-import-members/csv-file.png)

1. Na página **membros do grupo de importação em massa** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo é validado, a página importação em massa exibe o **arquivo carregado com êxito**. Se houver erros, você precisará corrigi-los antes de enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

## <a name="check-import-status"></a>Verificar status da importação

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa** .

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Para obter detalhes sobre cada item de linha na operação em massa, selecione os valores nas colunas **# êxito**, **# falha**ou **total de solicitações** . Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para importar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Próximas etapas

- [Remover membros do grupo em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixar uma lista de todos os grupos](groups-bulk-download.md)
