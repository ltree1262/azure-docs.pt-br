---
title: Criar modelos de ML com o designer
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os termos, os conceitos e o fluxo de trabalho que compõem o designer para Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 945a27da874071d22952207b7be5add79ce7e2ec
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872630"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>O que é o designer do Azure Machine Learning (versão prévia)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

O designer de Azure Machine Learning permite que você conecte visualmente [conjuntos](#datasets) de informações e [módulos](#module) em uma tela interativa para criar modelos de aprendizado de máquina. Para saber como começar a usar o designer, consulte [tutorial: prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

![Exemplo do Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

O designer usa seu [espaço de trabalho](concept-workspace.md) Azure Machine Learning para organizar recursos compartilhados, como:

+ [Pipelines](#pipeline)
+ [Conjuntos de dados](#datasets)
+ [Recursos de computação](#compute)
+ [Modelos registrados](concept-azure-machine-learning-architecture.md#models)
+ [Pipelines publicados](#publish)
+ [Pontos de extremidade em tempo real](#deploy)

## <a name="model-training-and-deployment"></a>Treinamento e implantação do modelo

O designer fornece uma tela Visual para criar, testar e implantar modelos de aprendizado de máquina. Com o designer, você pode:

+ Os [conjuntos](#datasets) de Datae os [módulos](#module) de arrastar e soltar na tela.
+ Conecte os módulos juntos para criar um [rascunho de pipeline](#pipeline-draft).
+ Envie uma [execução de pipeline](#pipeline-run) usando os recursos de computação em seu espaço de trabalho Azure Machine Learning.
+ Converta seus **pipelines de treinamento** em **pipelines de inferência**.
+ [Publique](#publish) seus pipelines em um **ponto de extremidade de pipeline** REST para enviar novas execuções de pipeline com parâmetros e conjuntos de os diferentes.
    + Publique um **pipeline de treinamento** para reutilizar um único pipeline para treinar vários modelos durante a alteração de parâmetros e conjuntos de valores.
    + Publicar um **pipeline de inferência de lote** para fazer previsões sobre novos dados usando um modelo treinado anteriormente.
+ [Implante](#deploy) um **pipeline de inferência em tempo real** para um ponto de extremidade em tempo real para fazer previsões sobre novos dados em tempo real.

![Diagrama de fluxo de trabalho para treinamento, inferência de lote e inferência em tempo real no designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Um [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) consiste em conjuntos de valores e módulos analíticos, que você conecta juntos. Os pipelines têm muitos usos: você pode criar um pipeline que treina um único modelo ou um que treina vários modelos. Você pode criar um pipeline que faça previsões em tempo real ou em lote, ou então crie um pipeline que limpa apenas os dados. Os pipelines permitem reutilizar seu trabalho e organizar seus projetos.

### <a name="pipeline-draft"></a>Rascunho do pipeline

À medida que você edita um pipeline no designer, seu progresso é salvo como um **rascunho de pipeline**. Você pode editar um rascunho de pipeline a qualquer momento adicionando ou removendo módulos, configurando destinos de computação, criando parâmetros e assim por diante.

Um pipeline válido tem estas características:

* Conjuntos de valores só podem se conectar a módulos.
* Os módulos só podem se conectar a conjuntos de os ou outros módulos.
* Todas as portas de entrada para módulos devem ter alguma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.

Quando você estiver pronto para executar o rascunho do pipeline, envie uma execução de pipeline.

### <a name="pipeline-run"></a>Execução do pipeline

Cada vez que você executa um pipeline, a configuração do pipeline e seus resultados são armazenados em seu espaço de trabalho como uma **execução de pipeline**. Você pode voltar a qualquer execução de pipeline para inspecioná-lo para fins de solução de problemas ou auditoria. **Clone** uma execução de pipeline para criar um novo rascunho de pipeline para edição.

As execuções de pipeline são agrupadas em [experimentos](concept-azure-machine-learning-architecture.md#experiments) para organizar o histórico de execução. Você pode definir o experimento para cada execução de pipeline. 

## <a name="datasets"></a>Conjunto de dados

Um conjunto de dados de aprendizado de máquina facilita o acesso e o trabalho com eles. Vários exemplos de conjuntos de exemplo estão incluídos no designer para experimentar. Você pode [registrar](how-to-create-register-datasets.md) mais conjuntos de informações conforme necessário.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que você pode executar em seus dados. O designer tem vários módulos que variam de funções de entrada de dados até processos de treinamento, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel Propriedades à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo. Você pode definir os recursos de computação para módulos individuais no designer. 

![Propriedades do módulo](./media/concept-designer/properties.png)

Para obter ajuda para navegar pela biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [visão geral de referência do módulo & do algoritmo](algorithm-module-reference/module-reference.md). Para obter ajuda para escolher um algoritmo, consulte a folha de consulta do [algoritmo de Azure Machine Learning](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a>Recursos de computação

Use os recursos de computação do seu espaço de trabalho para executar seu pipeline e hospedar seus modelos implantados como pontos de extremidade em tempo real ou pontos de extremidade de pipeline (para inferência de lote). Os destinos de computação com suporte são:

| Destino de computação | Treinamento | Implantação |
| ---- |:----:|:----:|
| Computação do Azure Machine Learning | ✓ | |
| Instância de computação do Azure Machine Learning | ✓ | |
| Serviço de Kubernetes do Azure | | ✓ |

Os destinos de computação são anexados ao seu [espaço de trabalho do Azure Machine Learning](concept-workspace.md). Você gerencia seus destinos de computação em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Implantar

Para executar inferência em tempo real, você deve implantar um pipeline como um **ponto de extremidade em tempo real**. O ponto de extremidade em tempo real cria uma interface entre um aplicativo externo e seu modelo de pontuação. Uma chamada para um ponto de extremidade em tempo real retorna resultados de previsão para o aplicativo em tempo real. Para fazer uma chamada para um ponto de extremidade em tempo real, você passa a chave de API que foi criada quando você implantou o ponto de extremidade. O ponto de extremidade é baseado em REST, uma opção de arquitetura popular para projetos de programação da Web.

Os pontos de extremidade em tempo real devem ser implantados em um cluster do serviço kubernetes do Azure.

Para saber como implantar seu modelo, consulte [tutorial: implantar um modelo de aprendizado de máquina com o designer](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicar

Você também pode publicar um pipeline para um **ponto de extremidade de pipeline**. Semelhante a um ponto de extremidade em tempo real, um ponto de extremidade de pipeline permite que você envie novas execuções de pipeline de aplicativos externos usando chamadas REST. No entanto, você não pode enviar ou receber dados em tempo real usando um ponto de extremidade de pipeline.

Os pipelines publicados são flexíveis, eles podem ser usados para treinar ou treinar modelos, [executar inferência de lote](how-to-run-batch-predictions-designer.md), processar novos dados e muito mais. Você pode publicar vários pipelines em um único ponto de extremidade de pipeline e especificar qual versão de pipeline executar.

Um pipeline publicado é executado nos recursos de computação que você define no rascunho do pipeline para cada módulo.

O designer cria o mesmo objeto [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) que o SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Movendo da interface visual para o designer

A interface visual (versão prévia) foi atualizada e agora é Azure Machine Learning designer (versão prévia). O designer foi reprojetado para usar um back-end baseado em pipeline que se integra totalmente com os outros recursos do Azure Machine Learning. 

Como resultado dessas atualizações, alguns conceitos e termos da interface visual foram alterados ou renomeados. Consulte a tabela abaixo para obter as alterações conceituais mais importantes. 

| Conceito no designer | Anteriormente na interface visual |
| ---- |:----:|
| Rascunho do pipeline | Experimento |
| Ponto de extremidade em tempo real | Serviço Web |

### <a name="migrating-to-the-designer"></a>Migrando para o designer

Você pode converter experimentos de interface visual existentes e serviços Web em pipelines e pontos de extremidade em tempo real no designer. Use as etapas a seguir para migrar seus ativos de interface visual:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Próximas etapas

* Conheça os conceitos básicos da análise preditiva e do aprendizado de máquina com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
* Saiba como modificar as [amostras existentes do designer](samples-designer.md) para adaptá-las às suas necessidades.

