---
title: Een Always-On VPN-gebruikerstunnel configureren
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt beschreven hoe u een Always On VPN-gebruikerstunnel configureert voor uw Virtuele WAN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502865"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Een Always On VPN-gebruikerstunnel configureren voor Virtual WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een point-to-site-configuratie maken en de virtuele hubtoewijzing bewerken. Zie de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [De hubtoewijzing bewerken](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Een gebruikerstunnel configureren

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u een profiel wilt verwijderen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppel de verbinding los en schakel het selectievakje **Verbinding automatisch** uit.

   ![Opschonen](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen voor](virtual-wan-faq.md)meer informatie over Virtual WAN.
