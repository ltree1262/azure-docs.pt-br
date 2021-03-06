---
title: Criar um aplicativo de área de trabalho que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de área de trabalho que chama APIs da Web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882993"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: aplicativo de desktop que chama APIs da Web

Saiba tudo o que você precisa para criar um aplicativo de desktop que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introdução

Se você ainda não fez isso, crie seu primeiro aplicativo seguindo o guia de início rápido da área de trabalho do .NET, o início rápido do Plataforma Universal do Windows (UWP) ou o início rápido do aplicativo macOS nativo:

> [!div class="nextstepaction"]
> [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo nativo do macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Visão geral

Você escreve um aplicativo de área de trabalho e deseja conectar usuários ao seu aplicativo e chamar APIs da Web, como Microsoft Graph, outras APIs da Microsoft ou sua própria API Web. Você tem várias possibilidades:

- Você pode usar a aquisição de token interativo:

  - Se o seu aplicativo de área de trabalho oferecer suporte a controles gráficos, por exemplo, se for um aplicativo Windows. Form, um aplicativo WPF ou um aplicativo macOS nativo.
  - Ou, se for um aplicativo .NET Core e você concordar em ter a interação de autenticação com o Azure Active Directory (Azure AD) acontecerá no navegador do sistema.

- Para aplicativos hospedados do Windows, também é possível para aplicativos em execução em computadores ingressados em um domínio do Windows ou o Azure AD ingressado para adquirir um token silenciosamente usando a autenticação integrada do Windows.
- Por fim, embora não seja recomendado, você pode usar um nome de usuário e uma senha em aplicativos cliente públicos. Ainda é necessário em alguns cenários como o DevOps. O uso de impõe restrições em seu aplicativo. Por exemplo, ele não pode entrar em um usuário que precisa executar a autenticação multifator (acesso condicional). Além disso, seu aplicativo não se beneficiará do SSO (logon único).

  Isso também vai contra os princípios da autenticação moderna e só é oferecido por ser herdado.

  ![Aplicativo de desktop](media/scenarios/desktop-app.svg)

- Se você escrever uma ferramenta de linha de comando portátil, provavelmente um aplicativo .NET Core que é executado no Linux ou no Mac, e se você aceitar que a autenticação será delegada ao navegador do sistema, poderá usar a autenticação interativa. O .NET Core não fornece um [navegador da Web](https://aka.ms/msal-net-uses-web-browser), portanto, a autenticação ocorre no navegador do sistema. Caso contrário, a melhor opção nesse caso é usar o fluxo de código do dispositivo. Esse fluxo também é usado para aplicativos sem um navegador, como aplicativos de IoT.

  ![Aplicativo com navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificações

Os aplicativos de área de trabalho têm várias especificidades. Eles dependem principalmente se seu aplicativo usa autenticação interativa ou não.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo de desktop: registro de aplicativo](scenario-desktop-app-registration.md)
