---
title: 'OpenVPN configureren in azure VPN Gateway: Power shell'
description: Lees hoe u Power shell kunt gebruiken om OpenVPN-protocol in te scha kelen op Azure VPN Gateway voor een punt-naar-site-omgeving.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 57bc53c28866b3f16e742c27b6a1600bfe6f44ab
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418820"
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
