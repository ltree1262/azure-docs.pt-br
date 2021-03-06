---
title: Usar injeção de dependência no .NET do Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar serviços em funções do .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678451"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET do Azure Functions

Azure Functions dá suporte ao padrão de design de software injeção de dependência (DI), que é uma técnica para obter [inversão de controle (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

- A injeção de dependência no Azure Functions é criada sobre os recursos de injeção de dependência do .NET Core. É recomendável ter familiaridade com a [injeção de dependência do .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Há diferenças em como você substitui dependências e como os valores de configuração são lidos com Azure Functions no plano de consumo.

- O suporte para injeção de dependência começa com Azure Functions 2. x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar a injeção de dependência, você deve instalar os seguintes pacotes NuGet:

- [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacote Microsoft. net. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Serviços de registro

Para registrar serviços, crie um método para configurar e adicionar componentes a uma `IFunctionsHostBuilder` instância do.  O host Azure Functions cria uma instância do `IFunctionsHostBuilder` e a transmite diretamente para o seu método.

Para registrar o método, adicione o `FunctionsStartup` atributo de assembly que especifica o nome de tipo usado durante a inicialização.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Advertências

Uma série de etapas de registro executada antes e depois que o tempo de execução processa a classe de inicialização. Portanto, tenha em mente os seguintes itens:

- *A classe de inicialização destina-se apenas à instalação e ao registro.* Evite usar serviços registrados na inicialização durante o processo de inicialização. Por exemplo, não tente registrar uma mensagem em um agente que está sendo registrado durante a inicialização. Esse ponto do processo de registro é muito cedo para que os serviços estejam disponíveis para uso. Depois que `Configure` o método é executado, o tempo de execução do Functions continua registrando dependências adicionais, o que pode afetar o funcionamento dos serviços.

- *O contêiner de injeção de dependência só mantém tipos explicitamente registrados*. Os únicos serviços disponíveis como tipos que podem ser injetados são os que `Configure` são configurados no método. Como resultado, tipos específicos de funções como `BindingContext` e `ExecutionContext` não estão disponíveis durante a instalação ou como tipos injetados.

## <a name="use-injected-dependencies"></a>Usar dependências injetadas

A injeção de construtor é usada para disponibilizar suas dependências em uma função. O uso de injeção de construtor requer que você não use classes estáticas.

O exemplo a seguir demonstra como `IMyService` as `HttpClient` dependências e são injetadas em uma função disparada por http. Este exemplo usa o pacote [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar um `HttpClient` na inicialização.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Azure Functions aplicativos fornecem os mesmos tempos de vida de serviço como [injeção de dependência ASP.net](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para um aplicativo do functions, os tempos de vida de serviço diferentes se comportam da seguinte maneira:

- **Transitório**: os serviços transitórios são criados após cada solicitação do serviço.
- Com **escopo**: o tempo de vida do serviço com escopo corresponde a um tempo de vida de execução da função. Os serviços com escopo são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: o tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado nas execuções de função nessa instância. Os serviços de vida útil singleton são recomendados para conexões e clientes `SqlConnection` , `HttpClient` por exemplo, ou instâncias.

Exiba ou baixe uma [amostra de tempos de vida de serviço diferentes](https://aka.ms/functions/di-sample) no github.

## <a name="logging-services"></a>Serviços de log

Se você precisar de seu próprio provedor de log, registre um tipo personalizado `ILoggerProvider` como uma instância. Application Insights é adicionado por Azure Functions automaticamente.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à coleção de serviços, pois ele registra os serviços que entram em conflito com os serviços fornecidos pelo ambiente.
> - Não Registre seu próprio `TelemetryConfiguration` ou `TelemetryClient` se você estiver usando a funcionalidade interna de Application insights. Se você precisar configurar sua própria `TelemetryClient` instância, crie uma por meio do injetado `TelemetryConfiguration` , conforme mostrado no [Monitor Azure Functions](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> e ILoggerFactory

O host injetará `ILogger<T>` e `ILoggerFactory` fará serviços em construtores.  No entanto, por padrão, esses novos filtros de log serão filtrados dos logs de função.  Será necessário modificar o `host.json` arquivo para aceitar filtros e categorias adicionais.  O exemplo a seguir demonstra como `ILogger<HttpTrigger>` adicionar um com logs que serão expostos pelo host.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

E um `host.json` arquivo que adiciona o filtro de log.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Serviços fornecidos pelo aplicativo de funções

O host de função registra vários serviços. Os seguintes serviços são seguros para serem adotados como uma dependência em seu aplicativo:

|Tipo de serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Responsável por fornecer a ID da instância do host|

Se houver outros serviços nos quais você deseja assumir uma dependência, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituindo serviços de host

Atualmente, não há suporte para a substituição de serviços fornecidos pelo host.  Se houver serviços que você deseja substituir, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Trabalhando com opções e configurações

Os valores definidos nas [configurações do aplicativo](./functions-how-to-use-azure-function-app-settings.md#settings) estão disponíveis `IConfiguration` em uma instância, o que permite que você leia os valores das configurações do aplicativo na classe de inicialização.

Você pode extrair valores da `IConfiguration` instância para um tipo personalizado. Copiar os valores das configurações do aplicativo para um tipo personalizado torna fácil testar seus serviços, tornando esses valores injetados. As configurações lidas na instância de configuração devem ser pares de chave/valor simples.

Considere a seguinte classe que inclui uma propriedade chamada consistente com uma configuração de aplicativo:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um `local.settings.json` arquivo que pode estruturar a configuração personalizada da seguinte maneira:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

De dentro do `Startup.Configure` método, você pode extrair valores da `IConfiguration` instância para seu tipo personalizado usando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Chamar `Bind` valores de cópia que têm nomes de propriedade correspondentes da configuração na instância personalizada. A instância de opções agora está disponível no contêiner IoC para injetar em uma função.

O objeto options é injetado na função como uma instância da interface genérica `IOptions` . Use a `Value` propriedade para acessar os valores encontrados em sua configuração.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Consulte o [padrão de opções em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) para obter mais detalhes sobre como trabalhar com opções.

> [!WARNING]
> Evite tentar ler valores de arquivos como *local. Settings. JSON* ou *appSettings. { Environment}. JSON* no plano de consumo. Os valores lidos desses arquivos relacionados a conexões de gatilho não estão disponíveis conforme o aplicativo é dimensionado porque a infraestrutura de hospedagem não tem acesso às informações de configuração.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

- [Como monitorar seu aplicativo de funções](functions-monitoring.md)
- [Práticas recomendadas para funções](functions-best-practices.md)
