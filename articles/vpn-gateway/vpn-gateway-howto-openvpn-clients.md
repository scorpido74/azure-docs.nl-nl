---
title: OpenVPN-clients configureren voor Azure VPN Gateway | Microsoft Docs
description: Stappen voor het configureren van OpenVPN-clients voor Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984083"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN Gateway

Dit artikel helpt u bij het configureren van **openvpn- &reg; protocol** -clients.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de stappen hebt voltooid voor het configureren van OpenVPN voor uw VPN-gateway. Zie [Configure openvpn for Azure VPN gateway](vpn-gateway-howto-openvpn.md)voor meer informatie.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang kunnen krijgen tot bronnen in een ander VNet, volgt u de instructies in het artikel [vnet-naar-vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-naar-vnet-verbinding in te stellen. Zorg ervoor dat BGP op de gateways en de verbindingen inschakelt, anders wordt het verkeer niet stroomt.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
