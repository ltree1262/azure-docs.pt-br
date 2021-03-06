---
title: Usar o API de Tabela e o Java para criar um aplicativo – Azure Cosmos DB
description: Este guia de início rápido mostra como usar a API de Tabela do Azure Cosmos DB para criar um aplicativo com o Portal do Azure e o Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e3517804cb66a9f98351e4c68f4f7c4387cee8fe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083794"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Início Rápido: Criar um aplicativo Java para gerenciar os dados de API de Tabela do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Neste início rápido, você criará uma conta da API de Tabela do Azure Cosmos DB e usará o Data Explorer e um aplicativo Java clonado do GitHub para criar tabelas e entidades. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure. Você também pode usar o [Emulador do Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [JDK (Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a variável de ambiente `JAVA_HOME` para a pasta em que o JDK está instalado.
- Um [arquivo binário do Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

> [!IMPORTANT] 
> Você precisa criar uma nova conta de API de tabela para trabalhar com os SDKs de API de tabela disponíveis. Não há suporte para contas de API de tabela criadas durante a versão prévia pelos SDKs disponíveis.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do GitHub, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática.

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> ![TIP] Para obter uma explicação mais detalhada do código semelhante, confira o artigo [Exemplo de API de Tabela do Cosmos DB](table-storage-how-to-use-java.md). 

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado. 

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

   ![Exibir as informações de cadeia de conexão no painel da Cadeia de Conexão](./media/create-table-java/cosmos-db-quickstart-connection-string.png)

2. Copie a CADEIA DE CONEXÃO PRIMÁRIA usando o botão de cópia do lado direito.

3. Abra *config.properties* na pasta *C:\git-samples\storage-table-java-getting-started\src\main\resources*. 

5. Comente a linha um e remova os comentários da linha dois. As primeiras duas linhas agora devem ter esta aparência.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Cole o valor de CADEIA DE CONEXÃO PRIMÁRIA do portal no valor da StorageConnectionString na linha 2. 

    > [!IMPORTANT]
    > Se o ponto de extremidade usa documents.azure.com, isso significa que você tem uma conta de versão prévia, e você precisa criar um [nova conta de API de tabela](#create-a-database-account) para trabalhar com o SDK de API de tabela geralmente disponível.
    >

7. Salve o arquivo *config.properties*.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="run-the-app"></a>Executar o aplicativo

1. Na janela do terminal git, `cd` para a pasta storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Na janela do terminal do Git, execute os comandos a seguir para executar o aplicativo Java.

    ```git
    mvn compile exec:java 
    ```

    A janela de console exibe os dados da tabela sendo adicionados ao novo banco de dados de tabela no Azure Cosmos DB.

    Agora, é possível voltar ao Data Explorer e ver, consultar, modificar e trabalhar com esses novos dados. 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar uma tabela usando o Data Explorer e executar um aplicativo Java para adicionar dados de tabela.  Agora, você pode consultar os dados usando a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de Tabela](table-import.md)
