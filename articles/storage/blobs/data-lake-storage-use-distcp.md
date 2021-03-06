---
title: Copiar dados para o Azure Data Lake Storage Gen2 usando DistCp| Microsoft Docs
description: Use a ferramenta DistCp para copiar dados de e para o Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255529"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Use o DistCp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen2

É possível usar [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar dados entre uma conta de armazenamento V2 de uso geral e uma conta de armazenamento V2 de uso geral com namespace hierárquico habilitado. Este artigo fornece instruções sobre como usar a ferramenta DistCp.

O DistCp fornece uma variedade de parâmetros de linha de comando e é altamente recomendável que você leia este artigo para otimizar o uso dessa ferramenta. Este artigo mostra a funcionalidade básica com foco no uso para copiar dados em uma conta habilitada para namespace hierárquico.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Armazenamento do Microsoft Azure existente sem os recursos do Data Lake Storage Gen2 (namespace hierárquico) habilitados**.
* **Uma conta de Armazenamento do Microsoft Azure com o recurso Data Lake Storage Gen2 habilitado**. Para instruções sobre como criar uma, consulte [Criar uma conta de armazenamento do Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Um sistema de arquivos** que foi criado na conta de armazenamento com namespace hierárquico habilitado.
* **Cluster de HDInsight do Azure** com acesso a uma conta de armazenamento com o Data Lake Storage Gen2 habilitado. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Certifique-se de habilitar a área de trabalho remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar o DistCp de um cluster HDInsight do Linux

Um cluster HDInsight é fornecido com o utilitário DistCp que pode ser usado para copiar dados de diferentes fontes em um cluster HDInsight. Se você configurou o cluster HDInsight para usar o Armazenamento de Blobs do Azure e o Azure Data Lake Storage em conjunto, o utilitário DistCp poderá ser usado prontamente para copiar dados entre eles. Nesta seção, veremos como usar o utilitário DistCp.

1. Crie uma sessão SSH para o cluster HDI. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verifique se é possível acessar a conta V2 de uso geral existente (sem namespace hierárquico habilitado).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no contêiner.

3. Do mesmo modo, verifique se é possível acessar a conta de armazenamento com o namespace hierárquico habilitado do cluster. Execute o seguinte comando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A saída deve fornecer uma lista de arquivos/pastas na conta do Data Lake Storage.

4. Use o DistCp para copiar dados do WASB para uma conta do Data Lake Storage.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    O comando copia os conteúdos da pasta **/example/data/gutenberg/** do Armazenamento de Blobs em **/myfolder** na conta do Data Lake Storage.

5. Igualmente, use o DistCp para copiar dados da conta do Data Lake Storage para o WASB (Azure Storage Blob).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/myfolder** na conta do Data Lake Store para a pasta **/example/data/gutenberg/** no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação ao Data Lake Storage. O número de cópias simultâneas é igual ao número de parâmetros do mapeador (**m**) na linha de comando. Esse parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é 20.

**Exemplo**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mapeadores que serão usados?

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: determinar a memória total disponível para a fila de aplicativo yarn ' padrão '** -a primeira etapa é determinar a memória disponível para a fila de aplicativos do yarn ' default '. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia Configs para ver a memória YARN disponível para a fila de aplicativos 'padrão'. Essa é a memória total disponível para o trabalho DistCp (que efetivamente é um trabalho do MapReduce).

* **Etapa 2: Calcular o número de mapeadores** ‑ O valor de **m** é igual ao quociente de memória total de YARN dividida pelo tamanho do contêiner YARN. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia Configurações. O tamanho do contêiner YARN é exibido nesta janela. A equação para chegar até o número de mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos supor que você tenha um cluster 4x D14v2s e esteja tentando transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contêm volumes diversos de dados e os tamanhos dos arquivos em cada pasta também são diferentes.

* **Total de memória YARN**: No portal do Ambari você determina que a memória YARN é 96 GB para um nó D14. Portanto, o total de memória YARN para um cluster de quatro nós é: 

        YARN memory = 4 * 96GB = 384GB

* **Número de Mapeadores**: no portal do Ambari, você determina que o tamanho do contêiner YARN é 3.072 MB para um nó de cluster D14. Portanto, o número de mapeadores é:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiverem usando memória, então você poderá usar somente uma parte da memória YARN do cluster para o DistCp.

### <a name="copying-large-datasets"></a>Copiando grandes conjuntos de dados

Quando o tamanho do conjunto de dados a ser movido for grande (por exemplo, maior que 1 TB) ou se você tiver muitas pastas diferentes, considere usar vários trabalhos DistCp. Provavelmente, não haverá nenhum ganho de desempenho, porém isso espalhará os trabalhos para que, se algum deles falhar, você só precise reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar mapeadores de tamanho semelhantes para otimizar o desempenho. Aumentar o número de mapeadores nem sempre melhora o desempenho.

* O DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais mapeadores do que arquivos. Como DistCp só pode atribuir um mapeador para um arquivo, isso limita a quantidade de simultaneidade que pode ser usada para copiar grandes arquivos.

* Se você tiver um pequeno número de grandes arquivos, divida-os em partes de arquivo de 256 MB para proporcionar um maior potencial de simultaneidade.
