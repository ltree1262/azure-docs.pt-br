---
title: Introdução à API do Azure Cosmos DB para MongoDB
description: Saiba como você pode usar o Azure Cosmos DB para armazenar e consultar grandes quantidades de dados usando a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754989"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O [Azure Cosmos DB](introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece [distribuição global imediata](distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](partition-data.md) mundialmente, latências de milissegundos de um dígito no 99º percentil e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa dados automaticamente](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, grafos, valores-chave e documentos. Por padrão, você pode interagir com o Cosmos DB usando a API do SQL. Além disso, o serviço Cosmos DB implementa os protocolos de transmissão para APIs NoSQL comuns incluindo Cassandra, MongoDB, Gremlin e Armazenamento de Tabelas do Azure. Isso permite que você use suas ferramentas e drivers de cliente NoSQL familiares para interagir com o banco de dados Cosmos.

## <a name="wire-protocol-compatibility"></a>Compatibilidade de protocolo de transmissão

O Azure Cosmos DB implementa os protocolos de transmissão de bancos de dados NoSQL comuns incluindo Cassandra, MongoDB, Gremlin e Armazenamento de Tabelas do Azure. Ao fornecer uma implementação nativa de protocolos de transmissão de forma direta e eficiente no Cosmos DB, ele permite que os SDKs, os drivers e as ferramentas de cliente existentes dos bancos de dados NoSQL interajam com o Cosmos DB com transparência. O Cosmos DB não usa nenhum código-fonte dos bancos de dados para fornecer APIs compatíveis com a transmissão a qualquer um dos bancos de dados NoSQL.

Por padrão, as novas contas criadas com a API do Azure Cosmos DB para MongoDB são compatíveis com a versão 3.6 do protocolo de transmissão do MongoDB. Qualquer driver de cliente do MongoDB que entenda essa versão de protocolo deve conseguir se conectar nativamente ao Cosmos DB.

![API do Azure Cosmos DB para MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Principais benefícios

Os principais benefícios do Cosmos DB como um banco de dados como serviço distribuído globalmente e totalmente gerenciado estão descritos [aqui](introduction.md). Além disso, com a implementação nativa de protocolos de transmissão de APIs NoSQL populares, o Cosmos DB oferece os seguintes benefícios:

* Migrar facilmente o aplicativo para o Cosmos DB, preservando as partes significativas da lógica do aplicativo.
* Manter o aplicativo portátil e independente de fornecedor de nuvem.
* Obter os SLAs com suporte financeiro líderes do mercado para as APIs NoSQL comuns da plataforma Cosmos DB.
* Dimensionar elasticamente a produtividade e armazenamento provisionados para seus bancos de dados Cosmos com base na sua necessidade e pagar somente pela produtividade e o armazenamento que você precisa. Isso resulta em economia de custo significativa.
* Distribuição global, pronta para uso, com a replicação de vários mestres.

## <a name="cosmos-dbs-api-for-mongodb"></a>API do Cosmos DB para MongoDB

Siga os inícios rápidos para criar uma conta do Azure Cosmos e migrar seu aplicativo existente do MongoDB para usar o Azure Cosmos DB ou criar outro aplicativo:

* [Migrar um aplicativo Web Node.js existente do MongoDB](create-mongodb-nodejs.md).
* [Criar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET](create-mongodb-dotnet.md)
* [Criar um aplicativo de console usando a API do Azure Cosmos DB para MongoDB e o SDK do Java](create-mongodb-java.md)

## <a name="next-steps"></a>Próximas etapas

Aqui estão algumas dicas para começar:

* Siga o tutorial [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da conta.
* Siga o tutorial [Usar o Studio 3T com o Azure Cosmos DB](mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados Cosmos DB e o aplicativo do MongoDB no Studio 3T.
* Siga o tutorial [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md) para importar os dados existentes para um banco de dados Cosmos DB.
* Conectar-se a uma conta do Cosmos usando o [Robo 3T](mongodb-robomongo.md).
* Saiba como [Configurar as preferências de leitura para aplicativos distribuídos globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Observação: este artigo descreve um recurso do Azure Cosmos DB que fornece a compatibilidade de protocolo de transmissão com bancos de dados MongoDB. A Microsoft não executa bancos de dados MongoDB para fornecer esse serviço. O Azure Cosmos DB não está afiliado à MongoDB, Inc.</sup>
