---
title: Conectar usuários em aplicativos de página única JavaScript com código de autenticação | Azure
titleSuffix: Microsoft identity platform
description: Saiba como um aplicativo JavaScript pode chamar uma API que exige tokens de acesso usando a plataforma de identidade da Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871514"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Início Rápido: Conectar usuários e obter um token de acesso em um SPA JavaScript usando o fluxo de código de autenticação 

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem ser alterados antes da GA (disponibilidade geral).


Este guia de início rápido usa MSAL.js 2.0 com o fluxo de código de autorização. Para usar o MSAL.js 1.0 com o fluxo implícito, confira [este guia de início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

Neste início rápido, você usará um exemplo de código para aprender como um SPA (aplicativo de página única) do JavaScript pode conectar usuários de contas pessoais, contas corporativas e de estudante. Um SPA JavaScript também pode obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API Web. Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma assinatura do Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (para editar arquivos de projeto)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrar e baixar o aplicativo de início rápido
> Para iniciar seu aplicativo de início rápido, use qualquer uma das opções a seguir.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Expresso): Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Entre no [portal do Azure](https://portal.azure.com).
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione a conta na parte superior direita e defina sua sessão do portal para o locatário do Azure Active Directory que deseja usar.
> 1. Selecione [Registros do Aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Insira um nome para seu aplicativo.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione **Registrar**.
> 1. Acesse o painel do início rápido e siga as instruções para baixar e configurar automaticamente seu novo aplicativo.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Entre no [portal do Azure](https://portal.azure.com).
>
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta na parte superior direita e defina sua sessão do portal para o locatário Azure AD que deseja usar.
> 1. Selecione [Registros do Aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selecione **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione **Registrar**. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
> 1. No painel esquerdo do aplicativo registrado, selecione **Autenticação**.
> 1. Em **Configurações da Plataforma**, selecione **Adicionar uma plataforma**. Um painel será aberto à esquerda. Lá, selecione a região **Aplicativos de Página Única**.
> 1. Ainda à esquerda, defina o valor do **URI de Redirecionamento** como `http://localhost:3000/`. 
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para tornar o exemplo de código neste trabalho de início rápido, você precisa adicionar um `redirectUri` como `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-javascript/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor Web usando Node.js, [baixe os arquivos de projeto de núcleo](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor Web usando o Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript
>
> Na pasta *app*, edite *authConfig.js* e defina os valores `clientID`, `authority` e `redirectUri` em `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Em que:
> - *\<Insira_a_ID_do_Aplicativo_Aqui>* é a **ID do Aplicativo (cliente)** do aplicativo registrado.
> - *\<Insira_a_ID_da_Instância_de_Nuvem_Aqui>* é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, basta inserir *https://login.microsoftonline.com/* . Para nuvens **nacionais** (por exemplo, China), confira [Nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - *\<Insira_as_informações_de_Locatário_aqui>* é definido para uma das seguintes opções:
>    - Se o seu aplicativo for compatível com as *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou **Nome do locatário** (por exemplo, *contoso.microsoft.com*).
>    - Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**.
>    - Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**.
> - *\<Enter_the_Redirect_Uri_Here>* é `http://localhost:3000`
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)** , **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do registro do aplicativo no portal do Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Etapa 3: seu aplicativo está configurado e pronto para ser executado
> Configuramos seu projeto com os valores das propriedades do seu aplicativo.

> [!div renderon="docs"]
>
> Em seguida, ainda na mesma pasta, edite o arquivo *graphConfig.js* para definir o `graphMeEndpoint` e o `graphMailEndpoint` para o objeto `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Insira_o_Ponto_de_Extremidade_Aqui>* é o ponto de extremidade no qual as chamadas à API serão feitas. Para o serviço principal ou global da API do Microsoft Graph, insira `https://graph.microsoft.com`. Para obter mais informações, confira [Implantação em uma nuvem nacional](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

Execute o projeto com um servidor Web usando o [Node.js](https://nodejs.org/en/download/):

1. Para iniciar o servidor, execute os seguintes comandos no diretório do projeto:
    ```bash
    npm install
    npm start
    ```
1. Navegue até `http://localhost:3000/`.

1. Selecione **Entrar** para iniciar o processo de conexão e chame a API do Microsoft Graph.

    Na primeira vez em que entrar, você deverá fornecer seu consentimento para permitir que o aplicativo acesse seu perfil e conecte você. Depois que você se conectar com êxito, as informações do seu perfil de usuário deverão ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona

![Como o exemplo de SPA do JavaScript funciona: 1. O SPA inicia a entrada. 2. O SPA adquire um token de ID da plataforma de identidade da Microsoft. 3. O SPA chama o token de aquisição. 4. A plataforma de identidade da Microsoft retorna um token de acesso para o SPA. 5. O SPA faz e a solicitação HTTP GET com o token de acesso para a API do Microsoft Graph. 6. A API do Graph retorna uma resposta HTTP para o SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL.js conecta usuários e solicita os tokens que são usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O arquivo *index.html* de exemplo contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> É possível substituir a versão anterior pela mais recente em [versões MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Como alternativa, se tiver instalado o Node.js, você poderá baixar a versão mais recente usando o npm (Gerenciador de Pacotes do Node.js):

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Próximas etapas

O [repositório GitHub do MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) contém documentação adicional da biblioteca, perguntas frequentes e oferece suporte a problemas.

Para obter um guia passo a passo mais detalhado sobre como criar aplicativo para este início rápido, confira:

> [!div class="nextstepaction"]
> [Tutorial para entrar e chamar o MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
