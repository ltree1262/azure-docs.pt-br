---
title: Ingestão de dados no Azure Data Lake Storage Gen2
description: Saiba como ingerir dados no Azure Data Lake Storage Gen2 no Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 7b844bcf45417fefc7dd78a26d5dae0b2ce03249
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982897"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingerir dados no Azure Data Lake Storage Gen2 

Neste artigo, você aprenderá a ingerir dados de um local para outro em uma conta de armazenamento do Azure Data Lake Gen2 usando o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o Azure Data Lake Gen 2 como uma *fonte* de armazenamento de dados. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento do Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter as etapas para criar uma.

## <a name="create-linked-services"></a>Criar serviços vinculados

No Azure Synapse Analytics, um serviço vinculado é onde você define as informações de conexão com outros serviços. Nesta seção, você adicionará o Azure Synapse Analytics e o Azure Data Lake Gen 2 como serviços vinculados.

1. Abra a UX do Azure Synapse Analytics e vá para a guia **Gerenciar**.
1. Em **Conexões externas**, selecione **Serviços vinculados**.
1. Para adicionar um serviço vinculado, clique em **Novo**.
1. Selecione a peça do Azure Data Lake Storage Gen2 na lista e clique em **Continuar**.
1. Insira as credenciais de autenticação. A chave de conta, a entidade de serviço e a identidade gerenciada são tipos de autenticação atualmente suportados. Clique em testar conexão para verificar se suas credenciais estão corretas. 
1. Clique em **Criar** quando terminar.

## <a name="create-pipeline"></a>Criar um pipeline

Um pipeline contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta seção, você criará um pipeline que contém uma atividade de cópia que ingere dados do Azure Data Lake Gen 2 para um pool de SQL.

1. Vá para a guia **Orquestrar**. Clique no ícone de adição ao lado do cabeçalho de pipelines e selecione **Pipeline**.
1. Em **Mover e Transformar** no painel atividades, arraste **Copiar dados** no painel da tela do pipeline.
1. Clique na atividade de cópia e vá para a guia **Origem**. Clique em **Novo** para criar um conjunto de dados de origem.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e clique em continuar.
1. Selecione DelimitedText como seu formato e clique em continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho do arquivo dos dados de origem e especifique se a primeira linha tem um cabeçalho. Você pode importar o esquema do repositório de arquivos ou de um arquivo de exemplo. Clique em OK quando terminar.
1. Vá para a guia **Coletor**. Clique em **Novo** para criar um novo conjunto de dados do coletor.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e clique em continuar.
1. Selecione DelimitedText como seu formato e clique em continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho da pasta na qual você deseja gravar os dados. Clique em OK quando terminar.

## <a name="debug-and-publish-pipeline"></a>Depurar e publicar o pipeline

Depois de concluir a configuração do pipeline, você poderá efetuar uma execução de depuração antes de publicar seus artefatos para verificar se tudo está correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela. 
1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar Tudo**. Esta ação publica as entidades (conjuntos de dados e pipelines) criadas por você anteriormente no Synapse Analytics.
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique no botão de sino no canto superior direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Acionar e monitorar o pipeline

Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior. 

1. Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  
1. Vá para a guia **Monitorar** localizada na barra lateral esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar os links na coluna **Ações** para exibir detalhes da atividade e executar o pipeline novamente.
1. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para voltar ao modo de exibição Execuções de Pipeline, selecione **Execuções de Pipeline** na parte superior. Para atualizar a exibição, selecione **Atualizar**.
1. Verifique se os dados estão gravados corretamente no pool de SQL.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados do Synapse Analytics, consulte o artigo [Ingerir dados em um pool de SQL](data-integration-sql-pool.md).
