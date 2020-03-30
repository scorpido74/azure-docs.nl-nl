---
title: Een Always-On VPN-gebruikerstunnel configureren
titleSuffix: Azure VPN Gateway
description: In dit artikel wordt beschreven hoe u een Always On VPN-gebruikerstunnel configureert voor uw VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502271"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Een AlwaysOn-VPN-gebruikerstunnel configureren

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>De gateway configureren

 Gebruik de instructies in het artikel [Een vpn-verbindingsartikel configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md) om de VPN-gateway te configureren om IKEv2 en verificatie op basis van certificaten te gebruiken.

## <a name="configure-a-user-tunnel"></a>Een gebruikerstunnel configureren

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u een profiel wilt verwijderen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppel de verbinding los en schakel het selectievakje **Verbinding automatisch** uit.

   ![Opschonen](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure point-to-site-verbindingsproblemen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor het oplossen van verbindingsproblemen.
