---
title: Condição de localização em Azure Active Directory acesso condicional
description: Saiba como usar a condição de localização para controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263225"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Qual é a condição de local em Azure Active Directory acesso condicional? 

Com o [acesso condicional do Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados podem acessar seus aplicativos de nuvem. A condição de local de uma política de acesso condicional permite que você vincule as configurações de controles de acesso aos locais de rede dos usuários.

Este artigo fornece as informações necessárias para configurar a condição de localização.

## <a name="locations"></a>Locais

O Azure AD permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar na Internet pública. Com a condição de localização, você pode controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário. Casos de uso comuns para a condição de localização são:

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa.
- Bloqueando o acesso para usuários que acessam um serviço de países e regiões específicas.

Um local é um rótulo para um local de rede que representa um local nomeado ou IPs confiáveis de autenticação multifator.

## <a name="named-locations"></a>Localizações nomeadas

Com os locais nomeados, você pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões.

Você pode acessar seus locais nomeados na seção **gerenciar** da página acesso condicional.

![Locais nomeados no acesso condicional](./media/location-condition/02.png)

A localização nomeada tem os seguintes componentes:

![Criar um novo local nomeado](./media/location-condition/42.png)

- **Nome** – O nome de exibição de uma localização nomeada.
- **Intervalos de IP**: um ou mais intervalos de endereço IPv4 no formato CIDR. Não há suporte para a especificação de um intervalo de endereços IPv6.

   > [!NOTE]
   > Os intervalos de endereços IPv6 não podem ser incluídos atualmente em um local nomeado. Isso significa que os intervalos de IPv6 não podem ser excluídos de uma política de acesso condicional.

- **Marcar como local confiável** – Um sinalizador que você pode definir para uma localização nomeada indicar um local confiável. Normalmente, os locais confiáveis são áreas de rede controladas pelo departamento de TI. Além do acesso condicional, os locais nomeados confiáveis também são usados pelos relatórios de segurança do Azure Identity Protection e do Azure AD para reduzir os [falsos positivos](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Países/regiões** - esta opção permite que você selecione um ou mais países ou regiões para definir uma localização nomeada.
- **Incluir áreas desconhecidas** – alguns endereços IP não são mapeados para um país ou região específica. Esta opção permite que você escolha se esses endereços IP devem ser incluídos na localização nomeada. Use essa configuração quando a política usando a localização nomeada deve aplicar-se a localizações desconhecidas.

O número de localizações que você pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. Você pode configurar locais com base nas seguintes limitações:

- Uma localização nomeada com até 1200 intervalos de IP.
- Um máximo de 90 localizações nomeadas com um intervalo IP atribuído a cada um delas.

A política de acesso condicional se aplica ao tráfego IPv4 e IPv6. Atualmente, os locais nomeados não permitem que os intervalos de IPv6 sejam configurados. Essa limitação causa as seguintes situações:

- A política de acesso condicional não pode ser direcionada para intervalos IPv6 específicos
- A política de acesso condicional não pode excluir intervalos IPV6 específicos

Se uma política estiver configurada para ser aplicada a "qualquer local", ela será aplicada ao tráfego IPv4 e IPv6. Os locais nomeados configurados para países e regiões especificados só dão suporte a endereços IPv4. O tráfego IPv6 só será incluído se a opção "incluir áreas desconhecidas" estiver selecionada.

## <a name="trusted-ips"></a>IPs confiáveis

Você também pode configurar intervalos de endereços IP que representam a Intranet local da sua organização nas [configurações do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esse recurso permite que você configure até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, consulte [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se você tiver IPs confiáveis configurados, eles aparecerão como **IPs confiáveis MFA** na lista de locais para a condição de localização.

### <a name="skipping-multi-factor-authentication"></a>Ignorando a autenticação multifator

Na página de configurações do serviço de autenticação multifator, você pode identificar os usuários da Intranet corporativa selecionando **Ignorar autenticação multifator para solicitações de usuários federados na minha Intranet**. Essa configuração indica que a declaração de rede corporativa interna, que é emitida pelo AD FS, deve ser confiável e usada para identificar o usuário como estando na rede corporativa. Para obter mais informações, consulte [habilitar o recurso de IPs confiáveis usando o acesso condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Depois de marcar essa opção, incluindo o local nomeado, os **IPs confiáveis do MFA** serão aplicados a qualquer política com essa opção selecionada.

Para aplicativos móveis e de área de trabalho, que têm tempos de vida de longa duração de sessão, o acesso condicional é reavaliado periodicamente. O padrão é uma vez a cada hora. Quando a declaração da rede corporativa interna é emitida somente no momento da autenticação inicial, o Microsoft Azure AD pode não ter uma lista de intervalos de IP confiáveis. Nesse caso, é mais difícil determinar se o usuário ainda está na rede corporativa:

1. Verifique se o endereço IP do usuário está em um dos intervalos de IP confiáveis.
2. Verifique se os três primeiros octetos do endereço IP do usuário correspondem aos três primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial quando a declaração de rede corporativa interna foi emitida originalmente e a localização do usuário foi validada.

Se ambas as etapas falharem, um usuário é considerado como não estando em um IP confiável.

## <a name="location-condition-configuration"></a>Configuração de condição de localização

Quando você configurar a condição de localização, você tem a opção de fazer a distinção entre:

- Qualquer local
- Todos os locais confiáveis
- Locais selecionados

![Configuração de condição de localização](./media/location-condition/01.png)

### <a name="any-location"></a>Qualquer local

Por padrão, selecionar **Qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Essa configuração não está limitada aos endereços IP que você configurou como localização nomeada. Quando seleciona **Qualquer local**, você ainda pode excluir locais específicos de uma política. Por exemplo, você pode aplicar uma política para todos os locais confiáveis de exceções de locais para definir o escopo para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção se aplica a:

- Todos os locais que foram marcados como local confiável
- **IPs confiáveis de MFA** (se configurado)

### <a name="selected-locations"></a>Locais selecionados

Com essa opção, você pode selecionar uma ou mais localizações nomeadas. Para uma política com essa configuração a ser aplicada, um usuário precisa se conectar de qualquer um dos locais selecionados. Quando você clica em **Selecionar**, é aberto o controle de seleção da rede nomeada que mostra a lista de redes nomeadas. A lista também mostra se o local de rede foi marcado como confiável. A localização nomeada chamada **IPs confiáveis de MFA** é usada para incluir as configurações de IP podem ser configuradas na página de configuração do serviço de autenticação multifator.

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="when-is-a-location-evaluated"></a>Quando um local é avaliado?

As políticas de acesso condicional são avaliadas quando:

- Um usuário inicialmente entra em um aplicativo Web, aplicativo da área de trabalho ou móvel.
- Um aplicativo da área de trabalho ou móvel que usa autenticação moderna, usa um token de atualização para adquirir um novo token de acesso. Por padrão, essa verificação é uma vez por hora.

Essa verificação significa que, para aplicativos móveis e de desktop usando autenticação moderna, uma alteração no local seria detectada dentro de uma hora após a alteração do local de rede. Para aplicativos da área de trabalho e móveis que não utilizam autenticação moderna, a política é aplicada em cada solicitação de token. A frequência da solicitação pode variar de acordo com o aplicativo. Da mesma forma, para aplicativos Web, a política é aplicada no início da entrada e é adequada para o tempo de vida da sessão no aplicativo Web. Devido às diferenças nos tempos de vida da sessão em todos os aplicativos, o tempo entre a avaliação de política também varia. Sempre que o aplicativo solicitar um novo token de entrada, a política será aplicada.

Por padrão, o Microsoft Azure AD emite um token de hora em hora. Depois de remover a rede corporativa, em uma hora a política é aplicada para aplicativos que usam autenticação moderna.

### <a name="user-ip-address"></a>Endereço IP do usuário

O endereço IP que é usado na avaliação de política é o endereço IP público do usuário. Para dispositivos em uma rede privada, esse endereço IP não é o IP do dispositivo do usuário na intranet, é o endereço usado pela rede para se conectar à Internet pública.

> [!WARNING]
> Se o dispositivo tiver apenas um endereço IPv6, não haverá suporte para configurar a condição de local.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e download de localizações nomeadas

Quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou fazer o download de um arquivo CSV com os intervalos de IP. Um carregamento substitui os intervalos de IP na lista com aqueles do arquivo. Cada linha do arquivo contém um intervalo de endereços IP no formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies e VPNs na nuvem

Quando você usa um proxy ou solução VPN hospedado na nuvem, o endereço IP que o Microsoft Azure AD usa ao avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público dos usuários não é usado porque não há validações que venham de uma fonte confiável, portanto apresentaria um método para simular um endereço IP.

Quando um proxy na nuvem está em uso, uma política que é usada para solicitar um dispositivo ingressado no domínio pode ser usada, ou a declaração corpnet interna do AD FS.

### <a name="api-support-and-powershell"></a>Suporte à API e PowerShell

A API e o PowerShell ainda não têm suporte para locais nomeados ou para políticas de acesso condicional.

## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
