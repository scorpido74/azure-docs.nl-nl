---
title: Een permanente VPN-gebruikers tunnel configureren
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt beschreven hoe u een always on-VPN-gebruikers tunnel configureert voor uw virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016348"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Een always on-VPN-gebruikers tunnel configureren voor virtuele WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een punt-naar-site-configuratie maken en de toewijzing van de virtuele hub bewerken. Raadpleeg de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Hub maken met P2S-gateway](virtual-wan-point-to-site-portal.md#hub)

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
