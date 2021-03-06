---
title: Parâmetros de tempo de pastas de trabalho do Azure Monitor
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658006"
---
# <a name="workbook-time-parameters"></a>Parâmetros de tempo da pasta de trabalho

Os parâmetros de hora permitem que os usuários definam o contexto de tempo de análise e são usados por quase todos os relatórios. É relativamente simples de configurar e usar-permitindo que os autores especifiquem os intervalos de tempo a serem mostrados na lista suspensa, incluindo a opção para intervalos de tempo personalizados. 

## <a name="creating-a-time-parameter"></a>Criando um parâmetro de tempo
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro:`TimeRange`
    2. Tipo de parâmetro:`Time range picker`
    3. Necessário:`checked`
    4. Intervalos de tempo disponíveis: última hora, últimas 12 horas, últimas 24 horas, últimas 48 horas, últimos 3 dias, últimos 7 dias e permitir seleção de intervalo de tempo personalizado
5. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-time/time-settings.png)

É assim que a pasta de trabalho será exibida no modo de leitura.

![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referenciando um parâmetro de tempo
### <a name="via-bindings"></a>Via associações
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. A maioria dos controles de pasta de trabalho oferece suporte a um seletor de escopo de _intervalo_ Abra a lista suspensa _intervalo de tempo_ e selecione o `{TimeRange}` no grupo parâmetros de tempo na parte inferior.
3. Isso associa o parâmetro de intervalo de tempo ao intervalo de tempo do gráfico. O escopo de tempo da consulta de exemplo agora é as últimas 24 horas.
4. Execute a consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado por meio de associações](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. No KQL, insira um filtro de escopo de tempo usando o parâmetro:`| where timestamp {TimeRange}`
3. Isso expande o tempo de avaliação de `| where timestamp > ago(1d)`consulta para, que é o valor de intervalo de tempo do parâmetro.
4. Execute a consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Em texto 
1. Adicione um controle de texto à pasta de trabalho.
2. Na redução, insira`The chosen time range is {TimeRange:label}`
3. Escolha a _edição concluída_
4. O controle de texto mostrará o texto: _o intervalo de tempo escolhido é das últimas 24 horas_

## <a name="time-parameter-options"></a>Opções de parâmetro de tempo
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Rótulo de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:label}` | Rótulo de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:value}` | Valor do intervalo de tempo | Há > (1D) |
| `{TimeRange:query}` | Consulta de intervalo de tempo | Há > (1D) |
| `{TimeRange:start}` | Hora de início do intervalo de tempo | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | Hora de término do intervalo de tempo | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | Detalhamento de intervalo de tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Usando opções de parâmetro em uma consulta
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](workbooks-visualizations.md) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
