---
title: Migrar aplicativos e APIs para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como usar b2clogin.com nas URLs de redirecionamento do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189983"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Definir URLs de redirecionamento para b2clogin.com do Azure Active Directory B2C

Ao configurar um provedor de identidade para inscrição e entrada em seu aplicativo Azure Active Directory B2C (Azure AD B2C), você precisa especificar uma URL de redirecionamento. Você não deve mais fazer referência a *login.microsoftonline.com* em seus aplicativos e APIs. Em vez disso, use *b2clogin.com* para todos os novos aplicativos e migre os aplicativos existentes do *login.microsoftonline.com* para o *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Substituição de login.microsoftonline.com

Em 04 de dezembro de 2019, anunciamos a desativação agendada do suporte do login.microsoftonline.com no Azure AD B2C em **04 de dezembro de 2020**:

[Azure Active Directory B2C está preterindo login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

A substituição de login.microsoftonline.com entrará em vigor para todos os locatários de Azure AD B2C em 04 de dezembro de 2020, fornecendo aos locatários um (1) ano para migrar para o b2clogin.com. Novos locatários criados após 04 de dezembro de 2019 não aceitarão solicitações de login.microsoftonline.com. Todas as funcionalidades permanecem as mesmas no ponto de extremidade b2clogin.com.

A substituição de login.microsoftonline.com não afeta Azure Active Directory locatários. Somente Azure Active Directory B2C locatários são afetados por essa alteração.

## <a name="benefits-of-b2clogincom"></a>Benefícios do b2clogin.com

Quando você usa *b2clogin.com* como a URL de redirecionamento:

* O espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
* Suas URLs de redirecionamento não precisam mais incluir uma referência à Microsoft.
* Há suporte para o código do lado do cliente JavaScript (atualmente em [Visualização](user-flow-javascript-overview.md)) em páginas personalizadas. Devido a restrições de segurança, os elementos de código JavaScript e de formulário HTML são removidos de páginas personalizadas se você usar *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Visão geral das alterações necessárias

Há várias modificações que talvez você precise fazer para migrar seus aplicativos para o *b2clogin.com*:

* Altere a URL de redirecionamento nos aplicativos do provedor de identidade para fazer referência a *b2clogin.com*.
* Atualize seus aplicativos de Azure AD B2C para usar o *b2clogin.com* em seu fluxo de usuário e referências de ponto de extremidade de token.
* Atualize todas as **origens permitidas** que você definiu nas configurações de CORS para [personalização da interface do usuário](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Alterar URLs de redirecionamento do provedor de identidade

Em cada site do provedor de identidade no qual você criou um aplicativo, altere todas as URLs confiáveis para redirecionar para `your-tenant-name.b2clogin.com` o em vez de *login.microsoftonline.com*.

Há dois formatos que você pode usar para suas URLs de redirecionamento b2clogin.com. A primeira fornece o benefício de não ter "Microsoft" exibido em qualquer lugar na URL usando a ID do locatário (um GUID) no lugar do seu nome de domínio do locatário:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A segunda opção usa o nome de domínio do locatário na forma `your-tenant-name.onmicrosoft.com`de. Por exemplo:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Para ambos os formatos:

* Substitua `{your-tenant-name}` pelo nome de seu locatário do Azure AD B2C.
* Remova `/te` se ele existir na URL.

## <a name="update-your-applications-and-apis"></a>Atualizar seus aplicativos e APIs

O código em seus aplicativos e APIs habilitados para Azure AD B2C pode se `login.microsoftonline.com` referir a vários lugares. Por exemplo, seu código pode ter referências a fluxos de usuário e pontos de extremidade de token. Atualize o seguinte para, em `your-tenant-name.b2clogin.com`vez disso, fazer referência a:

* Ponto de extremidade de autorização
* Ponto de extremidade de token
* Emissor do token

Por exemplo, o ponto de extremidade de autoridade para a política de inscrição/entrada da Contoso agora seria:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Para obter informações sobre como migrar aplicativos Web baseados em OWIN para o b2clogin.com, consulte [migrar uma API Web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar APIs de gerenciamento de API do Azure protegidas por Azure AD B2C, consulte a seção [migrar para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) de [proteger uma API de gerenciamento de API do Azure com Azure ad B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>MSAL (Biblioteca de Autenticação da Microsoft)

### <a name="validateauthority-property"></a>Propriedade ValidateAuthority

Se você estiver usando o [MSAL.net][msal-dotnet] v2 ou anterior, defina a propriedade **ValidateAuthority** como `false` na instanciação do cliente para permitir redirecionamentos para o *b2clogin.com*. Essa configuração não é necessária para o MSAL.NET v3 e superior.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Se você estiver usando o [MSAL para JavaScript][msal-js]:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como migrar aplicativos Web baseados em OWIN para o b2clogin.com, consulte [migrar uma API Web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).

Para migrar APIs de gerenciamento de API do Azure protegidas por Azure AD B2C, consulte a seção [migrar para b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) de [proteger uma API de gerenciamento de API do Azure com Azure ad B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
