---
title: Een Always-On VPN-tunnel configureren
titleSuffix: Azure VPN Gateway
description: Stappen om Always On VPN-tunnel voor VPN-gateway te configureren
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371723"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Een AlwaysOn-VPN-apparaattunnel configureren

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>De gateway configureren

Configureer de VPN-gateway om IKEv2 en verificatie op basis van certificaten te gebruiken met behulp van het artikel [Point-to-Site VPN-verbinding configureren.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>De apparaattunnel configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Voer de volgende opdracht uit om het profiel te verwijderen:

![Opschonen](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Zie Azure [point-to-site-verbindingsproblemen voor](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) het oplossen van problemen met azure
