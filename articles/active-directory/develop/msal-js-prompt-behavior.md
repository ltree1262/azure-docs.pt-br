---
title: Comportamento de prompt de solicitação interativa (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como personalizar o comportamento de prompt em chamadas interativas usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695969"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento de prompt em solicitações interativas do MSAL. js

Quando um usuário estabeleceu uma sessão ativa do Azure AD com várias contas de usuário, a página de entrada do Azure AD solicitará, por padrão, que o usuário selecione uma conta antes de continuar a entrar. Os usuários não verão uma experiência de seleção de conta se houver apenas uma única sessão autenticada com o Azure AD.

A biblioteca MSAL. js (a partir do v 0.2.4) não envia um parâmetro de prompt durante as solicitações interativas `acquireTokenRedirect` ( `acquireTokenPopup``loginRedirect`, `loginPopup`e) e, portanto, não impõe nenhum comportamento de prompt. Para solicitações de token silencioso usando `acquireTokenSilent` o método, MSAL. js passa um parâmetro de prompt `none`definido como.

Com base em seu cenário de aplicativo, você pode controlar o comportamento do prompt para as solicitações interativas definindo o parâmetro prompt nos parâmetros de solicitação passados para os métodos. Por exemplo, se você quiser invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores de prompt podem ser passados ao autenticar com o Azure AD:

**logon:** Esse valor forçará o usuário a inserir as credenciais na solicitação de autenticação.

**select_account:** Esse valor dará ao usuário uma experiência de seleção de conta listando todas as contas na sessão.

**consentimento:** Esse valor invocará a caixa de diálogo de consentimento do OAuth que permite aos usuários conceder permissões ao aplicativo.

**nenhum:** Esse valor garantirá que o usuário não veja nenhum prompt interativo. É recomendável não passar esse valor para métodos interativos no MSAL. js, pois ele pode ter comportamentos inesperados. Em vez disso, `acquireTokenSilent` use o método para obter chamadas silenciosas.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre o `prompt` parâmetro no protocolo de [concessão implícita do OAuth 2,0](v2-oauth2-implicit-grant-flow.md) que a biblioteca do MSAL. js usa.
