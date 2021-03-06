---
title: Usar identidades gerenciadas para acessar o Banco de Dados SQL do Azure — Azure Stream Analytics
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho do Azure Stream Analytics para a saída do DB SQL.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595835"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Usar identidades gerenciadas para acessar o Banco de Dados SQL do Azure de um trabalho do Azure Stream Analytics (Versão prévia)

O Azure Stream Analytics dá suporte à [Autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para os coletores de saída do Banco de Dados SQL do Azure. Identidades gerenciadas eliminam as limitações dos métodos de autenticação baseados em usuário, como a necessidade de autenticar novamente devido a alterações de senha ou expirações de token de usuário que ocorrem a cada 90 dias. Quando você remove a necessidade de autenticar manualmente, suas implantações do Stream Analytics podem ser totalmente automatizadas.

Uma identidade gerenciada é um aplicativo gerenciado registrado no Azure Active Directory que representa um determinado trabalho do Stream Analytics. O aplicativo gerenciado é usado para autenticar um recurso de destino. Este artigo mostra como habilitar a identidade gerenciada para uma ou mais saídas do Banco de Dados SQL do Azure de um trabalho do Stream Analytics por meio do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este recurso exige os seguintes requisitos:

- Um trabalho do Azure Stream Analytics.

- Um recurso do Banco de Dados SQL do Azure.

## <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Primeiro, você cria uma identidade gerenciada para seu trabalho do Azure Stream Analytics.

1. No [portal do Azure](https://portal.azure.com), abra seu trabalho do Azure Stream Analytics.

1. No menu de navegação à esquerda, selecione **Identidade Gerenciada** localizada em **Configurar**. Em seguida, marque a caixa ao lado de **Usar a Identidade Gerenciada atribuída ao sistema** e selecione **Salvar**.

   ![Selecione a identidade gerenciada atribuída ao sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Uma entidade de serviço para a identidade do trabalho do Stream Analytics no Azure Active Directory. O ciclo de vida da identidade que acaba de ser criada é gerenciado pelo Azure. Quando o trabalho do Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure. 

1. Ao salvar a configuração, a OID (ID do objeto) da entidade de serviço é listada como a ID da Entidade de Segurança conforme mostrado abaixo: 

   ![ID do objeto mostrada como ID da entidade](./media/sql-db-output-managed-identity/principal-id.png)

   A entidade de serviço tem o mesmo nome que o trabalho do Stream Analytics. Por exemplo, se o nome do seu trabalho for *MyASAJob*, o nome da entidade de serviço também será *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selecione um administrador do Active Directory

Depois de criar uma identidade gerenciada, selecione um administrador do Active Directory.

1. Navegue até o recurso do Banco de Dados SQL do Azure e selecione o SQL Server em que o banco de dados está. Você pode encontrar o nome do SQL Server ao lado do *Nome do servidor* na página de visão geral do recurso. 

1. Selecione **Active Directory** em **Configurações**. Em seguida, selecione **Definir administrador**. 

   ![Página de administração do Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Na página Administrador do Active Directory, procure um usuário ou grupo para ser um administrador do SQL Server e clique em **Selecionar**.  

   ![Adicione o administrador do Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

1. Selecione **Salvar** na página **Administrador do Active Directory**. O processo para alterar o administrador leva alguns minutos.  

## <a name="create-a-database-user"></a>Criar um usuário de banco de dados

Em seguida, crie um usuário de banco de dados independente no banco de dados SQL mapeado para a identidade do Azure Active Directory. O usuário de banco de dados independente não tem um logon para o banco de dados mestre, mas ele mapeia para uma identidade no diretório que está associada ao banco de dados. A identidade do Azure Active Directory pode ser uma conta de usuário individual ou um grupo. Nesse caso, crie um usuário de banco de dados independente para seu trabalho do Stream Analytics. 

1. Conecte-se ao banco de dados SQL usando o SQL Server Management Studio. O **Nome de usuário** é um usuário do Azure Active Directory com a permissão **ALTER ANY USER**. O administrador definido no SQL Server é um exemplo. Use **Azure Active Directory – Universal com autenticação multifator (MFA)** . 

   ![Conecte-se ao SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   O nome do servidor `<SQL Server name>.database.windows.net` pode ser diferente em regiões diferentes. Por exemplo, a região da China deve usar `<SQL Server name>.database.chinacloudapi.cn`.
 
   Você pode especificar um Banco de Dados SQL específico acessando **Opções > Propriedades de conexão > Conectar-se ao banco de dados**.  

   ![Propriedades de conexão do SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Ao se conectar pela primeira vez, você poderá encontrar a seguinte janela:

   ![Janela de nova regra de firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Nesse caso, navegue até o recurso do SQL Server no portal do Azure. Na seção **Segurança**, abra a página **Firewalls e rede virtual**. 
   1. Adicione uma nova regra com qualquer nome de regra.
   1. Use o endereço IP *De* na janela **Nova Regra de Firewall** para a *IP inicial*.
   1. Use o endereço IP *Para* na janela **Nova Regra de Firewall** para a *IP final*. 
   1. Selecione **Salvar** e tente se conectar ao SQL Server Management Studio novamente. 

1. Quando você estiver conectado, crie o usuário de banco de dados independente. O comando SQL a seguir cria um usuário de banco de dados independente que tem o mesmo nome que o seu trabalho do Stream Analytics. Certifique-se de incluir os colchetes em torno do *ASA_JOB_NAME*. Use a sintaxe T-SQL a seguir e execute a consulta. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Conceder permissões de trabalho ao Stream Analytics

O trabalho do Stream Analytics tem permissão da Identidade Gerenciada para se **CONECTAR** ao recurso de Banco de Dados SQL. Provavelmente, seria eficiente permitir que o trabalho do Stream Analytics executasse comandos, como **SELECT**. Você pode conceder essas permissões para o trabalho do Stream Analytics usando o SQL Server Management Studio. Para obter mais informações, consulte a referência [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15).

Como alternativa, você pode clicar com o botão direito do mouse no banco de dados SQL no SQL Server Management Studio e selecionar **Propriedades > Permissões**. No menu permissões, você pode ver o trabalho do Stream Analytics que você adicionou anteriormente e pode conceder ou negar manualmente as permissões como desejar.

## <a name="create-an-azure-sql-database-output"></a>Criar uma saída do Banco de Dados SQL do Azure

Agora que sua identidade gerenciada está configurada, você está pronto para adicionar o Banco de Dados SQL do Azure como saída para seu trabalho do Stream Analytics.

1. Volte para o trabalho do Stream Analytics e navegue até a página **Saídas** em **Topologia do trabalho**. 

1. Selecione **Adicionar > Banco de Dados SQL**. Na janela Propriedades de saída do coletor de saída do Banco de Dados SQL, selecione **Identidade Gerenciada** na lista suspensa do modo de Autenticação.

1. Preencha o restante das propriedades. Para saber mais sobre como criar uma saída do Banco de Dados SQL, consulte [Criar uma saída do Banco de Dados SQL com o Stream Analytics](stream-analytics-define-outputs.md#sql-database). Quando tiver terminado, selecione **Salvar**. 

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md)
