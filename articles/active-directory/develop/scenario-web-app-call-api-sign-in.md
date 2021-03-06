---
title: Remover contas do cache de token na saída-plataforma de identidade da Microsoft | Azure
description: Saiba como remover uma conta do cache de token ao sair
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181640"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Um aplicativo Web que chama APIs da Web: remover contas do cache de token na saída global

Você aprendeu a adicionar a entrada ao seu aplicativo Web no [aplicativo Web que conecta os usuários: entrar e sair](scenario-web-app-sign-user-sign-in.md).

A saída é diferente para um aplicativo Web que chama APIs da Web. Quando o usuário sai do seu aplicativo ou de qualquer aplicativo, você deve remover os tokens associados a esse usuário do cache de token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Interceptar o retorno de chamada após a saída única

Para limpar a entrada de cache de token associada à conta que foi desconectada, seu aplicativo pode interceptar o evento After `logout` . Os aplicativos Web armazenam tokens de acesso para cada usuário em um cache de token. Ao interceptar o retorno `logout` de chamada após, o aplicativo Web pode remover o usuário do cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web cuida da implementação da saída para você.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O exemplo ASP.NET não remove contas do cache na saída global.

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java não remove contas do cache na saída global.

# <a name="python"></a>[Python](#tab/python)

O exemplo de Python não remove contas do cache na saída global.

---

## <a name="next-steps"></a>Próximas etapas

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
