---
title: Een permanente VPN-gebruikers tunnel configureren
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt beschreven hoe u een always on-VPN-gebruikers tunnel configureert voor uw virtuele WAN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: 72287403e8406e0cfce83a69a5b9d3f58c693b8b
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750496"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Een always on-VPN-gebruikers tunnel configureren voor virtuele WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een punt-naar-site-configuratie maken en de toewijzing van de virtuele hub bewerken. Raadpleeg de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [De hub-toewijzing bewerken](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Een gebruikers tunnel configureren

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u een profiel wilt verwijderen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Verbreek de verbinding en schakel het selectie vakje **automatisch verbinding maken** uit.

   ![Opschonen](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
