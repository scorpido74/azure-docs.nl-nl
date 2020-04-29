---
title: OpenVPN-clients configureren voor Azure VPN Gateway | Microsoft Docs
description: Stappen voor het configureren van OpenVPN-clients voor Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066085"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN Gateway

Dit artikel helpt u bij het configureren van **openvpn &reg; -protocol** -clients.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de stappen hebt voltooid voor het configureren van OpenVPN voor uw VPN-gateway. Zie [Configure openvpn for Azure VPN gateway](vpn-gateway-howto-openvpn.md)voor meer informatie.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang kunnen krijgen tot bronnen in een ander VNet, volgt u de instructies in het artikel [vnet-naar-vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-naar-vnet-verbinding in te stellen. Zorg ervoor dat BGP op de gateways en de verbindingen inschakelt, anders wordt het verkeer niet stroomt.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
