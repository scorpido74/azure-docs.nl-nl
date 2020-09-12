---
title: Een tunnel voor altijd-op-VPN configureren
titleSuffix: Azure VPN Gateway
description: Meer informatie over het gebruik van gateways met Windows 10 altijd aan om permanente tunnels in te stellen en te configureren in Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: ab951375fdf9c5317e2c3a2f67271666d0987142
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435844"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Een AlwaysOn-VPN-apparaattunnel configureren

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>De gateway configureren

Configureer de VPN-gateway voor het gebruik van IKEv2 en verificatie op basis van certificaten met behulp van het artikel [een punt-naar-site-VPN-verbinding configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>De tunnel van het apparaat configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

![Opschonen](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) voor probleem oplossing
