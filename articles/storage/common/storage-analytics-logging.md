---
title: Registro em log da Análise de Armazenamento do Azure
description: Saiba como registrar em log detalhes sobre solicitações feitas no armazenamento do Azure.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 1e41eb02f4b02078dbf4d42c46cab574cf8d0701
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204059"
---
# <a name="azure-storage-analytics-logging"></a>Registro em log da Análise de Armazenamento do Azure

A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

 O log da Análise de Armazenamento não está habilitado por padrão na conta de armazenamento. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [Monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações obter propriedades do [serviço blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), propriedades do [serviço de fila](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)e [obter propriedades](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) do serviço tabela para habilitar o análise de armazenamento para cada serviço.

 As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de BLOB, mas não em seus pontos de extremidades de tabela ou de fila, somente os logs pertencentes ao serviço blob serão criados.

> [!NOTE]
>  No momento, o log da Análise de Armazenamento está disponível apenas para os serviços de Blob, Fila e Tabela. No entanto, não há suporte para a conta de armazenamento Premium.

## <a name="requests-logged-in-logging"></a>Solicitações registradas em log
### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações de dados de análise

  As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (Não Modificado)

  Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

## <a name="how-logs-are-stored"></a>Como os logs são armazenados

Todos os logs são armazenados em blobs de blocos em um contêiner denominado `$logs`, que é criado automaticamente quando a Análise de Armazenamento é habilitada para uma conta de armazenamento. O contêiner `$logs` está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído. Se você usar sua ferramenta de navegação de armazenamento para navegar diretamente até o contêiner, verá todos os blobs que contêm os dados de log.

> [!NOTE]
>  O `$logs` contêiner não é exibido quando uma operação de listagem de contêiner é executada, como a operação listar contêineres. Ele deve ser acessado diretamente. Por exemplo, você pode usar a operação listar BLOBs para acessar os BLOBs no `$logs` contêiner.

Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob. Pode levar até uma hora para que os dados de log apareçam nos BLOBs no contêiner de **$logs** , pois a frequência em que o serviço de armazenamento libera os gravadores de log. Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando **RequestId** e o número da **Operação**.

Se tiver um grande volume de dados de log com vários arquivos para cada hora, você poderá usar os metadados de blob para determinar quais dados o log contém examinando os campos de metadados do blob. Isso também é útil porque, às vezes, pode haver um atraso enquanto os dados são gravados nos arquivos de log: os metadados de blob fornecem uma indicação mais precisa do conteúdo do blob do que o nome do blob.

A maioria das ferramentas de navegação de armazenamento permite que você exiba os metadados de blobs. Além disso, você pode ler essas informações usando o PowerShell ou de forma programática. O seguinte snippet do PowerShell é um exemplo de filtragem da lista de blobs de log pelo nome, para especificar uma hora, e por metadados, para identificar apenas os logs que contêm operações de **gravação**.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre como listar BLOBs programaticamente, consulte [enumerando recursos de blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [definindo e Recuperando propriedades e metadados para recursos de blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log

 Cada log será gravado no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome do log:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table` ou `queue`.|
|`YYYY`|O ano de quatro dígitos do log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos do log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos do log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora inicial dos logs, no formato UTC de 24 horas. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto inicial dos logs. **Observação:**  Esse valor não tem suporte na versão atual do Análise de Armazenamento, e seu valor sempre será `00`.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador se inicia em `000000`. Por exemplo: `000001`|

 O nome a seguir é um exemplo de nome de log completo, que combina os exemplos anteriores:

 `blob/2011/07/31/1800/000001.log`

 O URI a seguir é um de exemplo de URI que pode ser usado para acessar o log anterior:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 18h30, em 31/07/2011, o log será gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log

 Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3:`read,write,delete`|
|`StartTime`|A hora da entrada mais antiga do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A hora da entrada mais recente do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato do log.|

 A lista a seguir exibe exemplos de metadados que usam os exemplos anteriores:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Habilitar log de armazenamento

Você pode habilitar o log de armazenamento com o portal do Azure, o PowerShell e os SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Habilitar o log de armazenamento usando o portal do Azure  

No portal do Azure, use a folha **configurações de diagnóstico (clássico)** para controlar o log de armazenamento, acessível na seção **monitoramento (clássico)** da **folha de menu**de uma conta de armazenamento.

Você pode especificar os serviços de armazenamento que deseja registrar em log e o período de retenção (em dias) para os dados registrados.  

### <a name="enable-storage-logging-using-powershell"></a>Habilitar o log de armazenamento usando o PowerShell  

 Você pode usar o PowerShell em seu computador local para configurar o Log de Armazenamento em sua conta de armazenamento usando o cmdlet do PowerShell do Azure **Get-AzureStorageServiceLoggingProperty** para recuperar as configurações atuais e o cmdlet **Set-AzureStorageServiceLoggingProperty** para alterar as configurações atuais.  

 Os cmdlets que controlam o Log de Armazenamento usam um parâmetro **LoggingOperations** que é uma cadeia de caracteres contendo uma lista de tipos de solicitação separados por vírgulas para registro em log. Os três possíveis tipos de solicitação são **leitura**, **gravação** e **exclusão**. Para desativar o log, use o valor **none** no parâmetro **LoggingOperations**.  

 O comando a seguir alterna o registro em log para solicitações de leitura, gravação e exclusão no serviço Fila em sua conta de armazenamento padrão com retenção definida como cinco dias:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O seguinte comando desativa o registro em log para o serviço de tabela em sua conta de armazenamento padrão:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte: [Como instalar e configurar o PowerShell do Azure](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Habilitar o log de armazenamento de forma programática  

 Além de usar os cmdlets portal do Azure ou Azure PowerShell para controlar o log de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se estiver usando uma linguagem .NET, você poderá usar a Biblioteca do Cliente de Armazenamento.  

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.SDK do NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 Para obter mais informações sobre como usar uma linguagem .NET para configurar o log de armazenamento, consulte [referência da biblioteca do cliente de armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre como configurar o log de armazenamento usando a API REST, consulte [Habilitando e Configurando análise de armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Baixar dados de log de log de armazenamento

 Para exibir e analisar seus dados de log, baixe os blobs que contêm os dados de log nos quais você está interessado para um computador local. Muitas ferramentas de navegação de armazenamento permitem que você baixe blobs de sua conta de armazenamento; Você também pode usar a ferramenta de cópia do Azure de linha de comando fornecida pela equipe de armazenamento do Azure [AzCopy](storage-use-azcopy-v10.md) para baixar seus dados de log.  
 
>[!NOTE]
> O `$logs` contêiner não está integrado à grade de eventos, portanto, você não receberá notificações quando os arquivos de log forem gravados. 

 Para baixar os dados de log nos quais você está interessado e para evitar o download dos mesmos dados de log mais de uma vez:  

-   Use a convenção de nomenclatura de data e hora para blobs contendo dados de log a fim de acompanhar quais blobs você já baixou para análise e não baixar novamente os mesmos dados mais de uma vez.  

-   Use os metadados nos blobs que contêm dados de log para identificar o período específico para o qual o blob contém dados de log a fim de identificar o blob exato que você precisa baixar.  

Para começar a usar o AzCopy, consulte Introdução [ao AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como você pode baixar os dados de log para o serviço fila para as horas começando às 9h, às 12h, às 9h e às 11 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como baixar arquivos específicos, consulte [baixar arquivos específicos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Após baixar seus dados de log, você pode exibir as entradas de log nos arquivos. Esses arquivos de log usam um formato de texto delimitado que muitas ferramentas de leitura de log podem analisar, incluindo o Microsoft Message Analyzer (para obter mais informações, consulte o guia [monitoramento, diagnóstico e solução de problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Ferramentas diferentes têm recursos diferentes para formatação, filtragem, classificação e pesquisa de anúncios para o conteúdo de seus arquivos de log. Para obter mais informações sobre o formato e o conteúdo do arquivo de log de log de armazenamento, consulte [análise de armazenamento formato de log](/rest/api/storageservices/storage-analytics-log-format) e [análise de armazenamento as operações registradas e as mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Próximas etapas

* [Formato de Log de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Mensagens de operações e status registradas de análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
