---
title: Usando transações
description: Dicas para implementar transações no pool do SQL (data warehouse) para desenvolver soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9b9ce5110a03ec4d67b3e8af6d9b18e5ad6836af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428713"
---
# <a name="using-transactions-in-sql-pool"></a>Usando transações no pool do SQL

Dicas para implementar transações no pool do SQL (data warehouse) para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar

Como esperado, o pool do SQL dá suporte a transações como parte da carga de trabalho de data warehouse. No entanto, para garantir que o desempenho do pool do SQL seja mantido em escala, alguns recursos são limitados em comparação com SQL Server. Este artigo realça as diferenças e lista as outras.

## <a name="transaction-isolation-levels"></a>Níveis de isolamento da transação

O pool SQL implementa transações ACID. O nível de isolamento do suporte transacional é padrão para leitura não confirmada.  Você pode alterá-lo para ler o isolamento de instantâneo confirmado ativando a opção de banco de dados READ_COMMITTED_SNAPSHOT para um banco de dados de usuário quando conectado ao banco de dados mestre.  

Uma vez habilitada, todas as transações neste banco de dados são executadas em isolamento de instantâneo de leitura confirmada e a configuração leitura não confirmada no nível da sessão não será respeitada. Verifique [as opções ALTER DATABASE SET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest) para obter detalhes.

## <a name="transaction-size"></a>Tamanho da transação
Uma única transação de modificação de dados é limitada em tamanho. O limite é aplicado por distribuição. Portanto, a alocação total pode ser calculada multiplicando o limite pela contagem de distribuição. 

Para chegar a uma aproximação do número máximo de linhas na transação, divida o limite de distribuição pelo tamanho total de cada linha. Para colunas de tamanho variável, considere o uso de um tamanho médio de coluna em vez do tamanho máximo.

Na tabela abaixo, foram feitas as seguintes suposições:

* Ocorreu uma distribuição uniforme dos dados
* O tamanho médio da linha é de 250 bytes

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo da transação (GB) | Nº de linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6.000.000 |360.000.000 |
| DW300c |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7.5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW2500c |18,75 |60 |1125 |75 milhões |4.500.000.000 |
| DW3000c |22,5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW5000c |37,5 |60 |2.250 |150 milhões |9.000.000.000 |
| DW6000c |45 |60 |2.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3.375 |225 milhões |13.500.000.000 |
| DW10000c |75 |60 |4.500 |300.000.000 |18.000.000.000 |
| DW15000c |112,5 |60 |6.750 |450 milhões |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo da transação (GB) | Nº de linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6.000.000 |360.000.000 |
| DW300 |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4.5 |60 |270 |18.000.000 |1.080.000.000 |
| DW1000 |7.5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW3000 |22,5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW6000 |45 |60 |2.700 |180.000.000 |10.800.000.000 |

O limite de tamanho de transação é aplicado por transação ou operação. Ele não é aplicado em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log.

Para otimizar e minimizar a quantidade de dados gravados no log, veja o artigo [práticas recomendadas de transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

> [!WARNING]
> O tamanho máximo de transações só pode ser obtido para tabelas distribuídas HASH ou ROUND_ROBIN nas quais o espalhamento de dados é uniforme. Se a transação estiver gravando dados de maneira distorcida nas distribuições, provavelmente, o limite será alcançado antes do tamanho máximo de transações.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado da transação

O pool SQL usa a função XACT_STATE () para relatar uma transação com falha usando o valor-2. Esse valor significa que a transação falhou e está marcada para reversão somente.

> [!NOTE]
> O uso de -2 pela função XACT_STATE para denotar uma transação com falha representa um comportamento diferente para o SQL Server. O SQL Server usa o valor -1 para representar uma transação não confirmável. O SQL Server consegue tolerar alguns erros dentro de uma transação sem precisar ser marcado como não confirmável. Por exemplo, `SELECT 1/0` causaria um erro, mas não forçaria uma transação em um estado não confirmável. O SQL Server também permite leituras na transação não confirmável. No entanto, o pool do SQL não permite que você faça isso. Se ocorrer um erro dentro de uma transação do pool do SQL, ele entrará automaticamente no estado-2 e você não poderá fazer mais instruções SELECT até que a instrução tenha sido revertida. Portanto, é importante verificar o código do aplicativo para ver se ele usa XACT_STATE(), pois você poderá precisar modificar o código.

Por exemplo, no SQL Server, você verá uma transação com esta aparência:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O código anterior oferece a seguinte mensagem de erro:

Msg 111233, Nível 16, Estado 1, Linha 1 111233; a transação atual foi anulada, e as alterações pendentes foram revertidas. Causa: uma transação em um estado somente de reversão não foi revertida explicitamente antes de uma instrução DDL, DML ou SELECT.

Você não receberá a saída das funções ERROR_*.

No pool SQL, o código precisa ser ligeiramente alterado:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O comportamento esperado é observado agora. O erro na transação é gerenciado e as funções ERROR_* fornecem valores conforme o esperado.

Tudo o que mudou é que o ROLLBACK da transação deve ocorrer antes da leitura das informações de erro no bloco CATCH.

## <a name="error_line-function"></a>Função Error_line()

Também vale a pena observar que o pool do SQL não implementa nem dá suporte à função ERROR_LINE (). Se você tiver isso em seu código, será necessário removê-lo para que ele esteja em conformidade com o pool do SQL. Em vez disso, use rótulos de consulta em seu código para implementar a funcionalidade equivalente. Para obter mais detalhes, consulte o artigo [LABEL](develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso de THROW e RAISERROR

THROW é a implementação mais moderna para gerar exceções no pool do SQL, mas também há suporte para RAISERROR. No entanto, existem algumas diferenças que valem a pena prestar atenção.

* Os números das mensagens de erro definidas pelo usuário não podem estar no intervalo de 100.000 a 150.000 para THROW
* As mensagens de erro do RAISERROR são fixadas em 50.000
* Não há suporte para o uso de sys.messages

## <a name="limitations"></a>Limitações

O pool do SQL tem algumas outras restrições relacionadas a transações.

Elas são as seguintes:

* Sem transações distribuídas
* Não há transações aninhadas permitidas
* Não são permitidos pontos de salvamento
* Nenhuma transação nomeada
* Nenhuma transação marcada
* Não há suporte para DDL, como CREATE TABLE em uma transação definida pelo usuário

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a otimização das transações, confira [Práticas recomendadas das transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Os guias de práticas recomendadas adicionais também são fornecidos para o [pool do SQL](best-practices-sql-pool.md) e o [SQL sob demanda (versão prévia)](on-demand-workspace-overview.md).
