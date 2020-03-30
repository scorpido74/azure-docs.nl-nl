---
title: OpenVPN-clients configureren voor Azure VPN-gateway| Microsoft Documenten
description: Stappen om OpenVPN-clients voor Azure VPN-gateway te configureren
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066085"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN-gateway

In dit artikel u de clients van **het OpenVPN-protocol &reg; ** configureren.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de stappen hebt voltooid om OpenVPN voor uw VPN-gateway te configureren. Zie [OpenVPN configureren voor Azure VPN Gateway voor](vpn-gateway-howto-openvpn.md)meer informatie.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang hebben tot bronnen in een andere VNet, volg dan de instructies op het [VNet-naar-VNet-artikel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-to-vnet-verbinding in te stellen. Zorg ervoor dat u BGP inschakelt op de gateways en de verbindingen, anders stroomt het verkeer niet.

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**
