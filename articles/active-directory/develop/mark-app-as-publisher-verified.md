---
title: Marcar um aplicativo como “editor verificado” – Plataforma de identidade da Microsoft | Azure | Azure
description: Descreve como marcar um aplicativo como “editor verificado”. Quando um aplicativo é marcado como verificado pelo editor, isso significa que o editor verificou sua identidade usando uma conta do Microsoft Partner Network que concluiu o processo de verificação e associou essa conta do MPN ao registro de aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595685"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>Marcar seu aplicativo como “editor verificado” (visualização)

Quando um aplicativo é marcado como editor verificado, isso significa que o editor verificou sua identidade usando uma conta do Microsoft Partner Network (MPN) e associou essa conta do MPN ao registro de aplicativo. Este artigo descreve como concluir o processo de [verificação do editor (visualização)](publisher-verification-overview.md).

## <a name="quickstart"></a>Guia de Início Rápido
Se você já estiver inscrito no Microsoft Partner Network (MPN) e tiver atendido aos [pré-requisitos](publisher-verification-overview.md#requirements), poderá começar imediatamente: 

1. Navegue até a visualização [Portal de registro de aplicativo](https://aka.ms/PublisherVerificationPreview).

1. Escolha um aplicativo e clique em **Identidade visual**. 

1. Clique em **Adicionar ID do MPN para verificar o editor** e analise os requisitos listados.

1. Insira sua ID do MPN e clique em **Verificar e salvar**.

Para obter mais detalhes sobre benefícios específicos, requisitos e perguntas frequentes, consulte a [visão geral](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Marcar seu aplicativo como “editor verificado”
Verifique se você atendeu aos [pré-requisitos](publisher-verification-overview.md#requirements) e siga estas etapas para marcar seus aplicativos como “editor verificado”.  

1. Garanta que você está conectado com uma conta organizacional (Azure Active Directory) autorizada a fazer alterações nos aplicativos que você deseja marcar como “editor verificado” e na conta MPN no Partner Center. 

    - No Azure Active Directory, esse usuário deve ser o proprietário do aplicativo ou ter uma das seguintes funções: Administrador de aplicativos, administrador de aplicativos de nuvem, administrador global. 

    - No Partner Center, esse usuário deve ter as seguintes funções: Administrador de MPN, administrador de contas ou um administrador global (essa é uma função compartilhada controlada no Azure Active Directory). 

1. Navegue até a versão de visualização do portal de registro de aplicativo:  

1. Clique em um aplicativo que você deseja marcar como Editor Verificado e abra a folha de identidade visual. 

1. Garanta que o domínio do editor do aplicativo esteja definido adequadamente. Esse domínio deve ser: 

    - Adicionado a um locatário do Azure Active Directory com o domínio personalizado verificado pelo DNS,  

    - Corresponder ao domínio do endereço de email usado durante o processo de verificação para sua conta do MPN. 

1. Clique em **Adicionar ID do MPN para verificar o editor** próximo à parte inferior da página. 

1. Insira sua **ID do MPN**. Essa ID do MPN deve ser para: 

    - Uma conta do Microsoft Partner Network válida que concluiu o processo de verificação.  

    - A PGA (conta global do parceiro) da sua organização. 

1. Clique em **Verificar e salvar**. 

1. Aguarde o processamento da solicitação. Isso pode levar alguns minutos. 

1. Se a verificação tiver sido bem-sucedida, a janela de verificação do editor será fechada, e você voltará para a folha de identidade visual. Você verá o selo Verificado em azul próximo ao seu **nome de exibição de editor verificado**. 

1. Os usuários que forem solicitados a dar consentimento ao seu aplicativo começarão a ver o selo assim que você tiver finalizado o processo com êxito. Porém, pode levar algum tempo para que isso seja replicado em todo o sistema. 

1. Teste essa funcionalidade. Para isso, entre em seu aplicativo e garanta que o selo Verificado apareça na tela de consentimento. Se você estiver conectado como um usuário que concedeu consentimento ao aplicativo, poderá usar o parâmetro de consulta *prompt=consent* para forçar uma solicitação de consentimento. 

1. Repita esse processo conforme necessário para qualquer outro aplicativo para o qual você deseja que o selo seja exibido. Você pode usar o Microsoft Graph para fazer isso mais rapidamente em massa, e os cmdlets do PowerShell estarão disponíveis em breve. Consulte [Fazer chamadas de Microsoft API Graph](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) para obter mais informações. 

É isso! Informe-nos se você tiver comentários sobre o processo, os resultados ou o recurso em geral. 

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas, leia as [informações de solução de problemas](troubleshoot-publisher-verification.md).