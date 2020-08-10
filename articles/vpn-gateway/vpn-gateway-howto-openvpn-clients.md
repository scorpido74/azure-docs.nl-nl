---
title: OpenVPN-clients configureren voor Azure VPN Gateway | Microsoft Docs
description: Meer informatie over het configureren van OpenVPN voor Azure VPN Gateway voor clients met Windows, Linux en Mac-besturings systemen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 92447a541726c1c11b7b10d6d52cf91cfc07f945
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036858"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN Gateway

Dit artikel helpt u bij het configureren van **openvpn- &reg; protocol** -clients.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de stappen hebt voltooid voor het configureren van OpenVPN voor uw VPN-gateway. Zie [Configure openvpn for Azure VPN gateway](vpn-gateway-howto-openvpn.md)voor meer informatie.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang kunnen krijgen tot bronnen in een ander VNet, volgt u de instructies in het artikel [vnet-naar-vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-naar-vnet-verbinding in te stellen. Zorg ervoor dat BGP op de gateways en de verbindingen inschakelt, anders wordt het verkeer niet stroomt.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
