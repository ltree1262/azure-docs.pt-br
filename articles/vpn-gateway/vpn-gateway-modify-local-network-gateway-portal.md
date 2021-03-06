---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: portal do Azure'
description: Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local usando o portal do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864019"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as configurações de gateway de rede local usando o portal do Azure

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações de gateway de rede local. Também é possível modificar essas configurações usando um método diferente, selecionando uma opção diferente da lista a seguir:

Antes de excluir a conexão, convém baixar a configuração dos seus dispositivos de conexão para obter a PSK definida. Dessa forma, não é necessário redefini-la do outro lado.

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificar os prefixos de endereço IP

Quando você modifica os prefixos de endereço IP, as etapas a que seguir dependem se o gateway de rede local tem uma conexão.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificar o endereço IP do gateway

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. Quando você altera o endereço IP público, as etapas a seguir dependem se o gateway de rede local possui uma conexão.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Próximas etapas

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).
