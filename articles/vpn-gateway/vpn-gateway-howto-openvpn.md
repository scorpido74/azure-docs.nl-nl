---
title: 'OpenVPN configureren op Azure VPN Gateway: PowerShell'
description: Stappen om OpenVPN voor Azure VPN Gateway te configureren
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162404"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>OpenVPN configureren voor Azure point-to-site VPN-gateway

Met dit artikel u **OpenVPN® Protocol** instellen op Azure VPN Gateway. In het artikel wordt ervan uitgegaan dat u al een werkpunt-naar-site-omgeving hebt. Als u dit niet doet, gebruikt u de instructies in stap 1 om een point-to-site VPN te maken.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Maak een point-to-site VPN

Als u nog geen functionerende point-to-site-omgeving hebt, volgt u de instructie om er een te maken. Zie [Een point-to-site VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) maken om een point-to-site VPN-gateway te maken en te configureren met native Azure-certificaatverificatie. 

> [!IMPORTANT]
> De Basic SKU wordt niet ondersteund voor OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. OpenVPN inschakelen op de gateway

Schakel OpenVPN in op uw gateway. Controleer of de gateway al is geconfigureerd voor point-to-site (IKEv2 of SSTP) voordat u de volgende opdrachten uitvoert:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Volgende stappen

Zie [OpenVPN-clients configureren](vpn-gateway-howto-openvpn-clients.md)om clients voor OpenVPN te configureren.

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**
