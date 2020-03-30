---
title: Een Always-On VPN-tunnel configureren
titleSuffix: Azure Virtual WAN
description: Stappen om Always On VPN-apparaattunnel voor Virtual WAN te configureren
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502852"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Een Always On VPN-apparaattunnel configureren voor Virtual WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een point-to-site-configuratie maken en de virtuele hubtoewijzing bewerken. Zie de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [De hubtoewijzing bewerken](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>De apparaattunnel configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Voer de volgende opdracht uit om het profiel te verwijderen:

![Opschonen](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen voor](virtual-wan-faq.md)meer informatie over Virtual WAN.