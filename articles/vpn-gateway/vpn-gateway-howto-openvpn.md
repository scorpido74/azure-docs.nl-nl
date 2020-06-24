---
title: 'OpenVPN configureren in azure VPN Gateway: Power shell'
description: Stappen voor het configureren van OpenVPN voor Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: de8d03467b5e44df1b9069c6db31d496785ff32e
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983846"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>OpenVPN configureren voor Azure punt-naar-site-VPN Gateway

Dit artikel helpt u bij het instellen van **OpenVPN®-protocol** op Azure VPN gateway. In dit artikel wordt ervan uitgegaan dat u al een werk punt-naar-site-omgeving hebt. Als u dit niet doet, gebruikt u de instructies in stap 1 om een punt-naar-site-VPN te maken.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. een punt-naar-site-VPN maken

Als u nog geen werkende Point-to-site-omgeving hebt, volgt u de instructie om er een te maken. Zie [een punt-naar-site-VPN maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md) voor het maken en configureren van een punt-naar-site-VPN-gateway met systeem eigen Azure-certificaat verificatie. 

> [!IMPORTANT]
> De basis-SKU wordt niet ondersteund voor OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Schakel OpenVPN in op de gateway

Schakel OpenVPN in op uw gateway. Zorg ervoor dat de gateway al is geconfigureerd voor punt-naar-site (IKEv2 of SSTP) voordat u de volgende opdrachten uitvoert:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Volgende stappen

Zie [openvpn-clients configureren](vpn-gateway-howto-openvpn-clients.md)voor meer informatie over het configureren van clients voor openvpn.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
