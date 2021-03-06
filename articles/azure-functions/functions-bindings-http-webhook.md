---
title: Gatilhos e associações HTTP do Azure Functions
description: Aprenda a usar gatilhos e associações HTTP no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462098"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Visão geral de gatilhos e associações HTTP Azure Functions

Azure Functions pode ser invocado por meio de solicitações HTTP para criar APIs sem servidor e responder a [WebHooks](https://en.wikipedia.org/wiki/Webhook).

| Ação | Type |
|---------|---------|
| Executar uma função de uma solicitação HTTP | [Gatilho](./functions-bindings-http-webhook-trigger.md) |
| Retornar uma resposta HTTP de uma função |[Associação de saída](./functions-bindings-http-webhook-output.md) |

O código neste artigo usa como padrão a sintaxe do .NET Core, usada nas funções versão 2. x e superior. Para obter informações sobre a sintaxe 1.x, consulte os [modelos de funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Adicionar ao seu aplicativo de funções

### <a name="functions-2x-and-higher"></a>Funções 2. x e posteriores

Trabalhar com o gatilho e as associações exige que você referencie o pacote apropriado. O pacote NuGet é usado para bibliotecas de classes do .NET enquanto o pacote de extensão é usado para todos os outros tipos de aplicativos.

| Linguagem                                        | Adicionar por...                                   | Comentários 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalando o [pacote NuGet], versão 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrando o [pacote de extensão]          | A [extensão de ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) é recomendada para uso com Visual Studio Code. |
| Script C# (somente online em portal do Azure)         | Adicionando uma associação                            | Para atualizar as extensões de associação existentes sem precisar republicar seu aplicativo de funções, consulte [atualizar suas extensões]. |

[core tools]: ./functions-run-local.md
[pacote de extensão]: ./functions-bindings-register.md#extension-bundles
[Pacote NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Atualizar suas extensões]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funções 1.x

Os aplicativos do Functions 1. x têm automaticamente uma referência ao pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x.

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função de uma solicitação HTTP](./functions-bindings-http-webhook-trigger.md)
- [Retornar uma resposta HTTP de uma função](./functions-bindings-http-webhook-output.md)
