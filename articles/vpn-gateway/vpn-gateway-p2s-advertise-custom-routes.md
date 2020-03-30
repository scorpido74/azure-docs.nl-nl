---
title: 'Azure VPN-gateway: adverteer aangepaste routes voor P2S VPN-clients'
description: Stappen om aangepaste routes naar uw point-to-site-clients te adverteren
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151905"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Aangepaste routes adverteren voor P2S VPN-clients

Misschien wilt u aangepaste routes adverteren naar al uw point-to-site VPN-clients. Wanneer u bijvoorbeeld opslageindpunten in uw VNet hebt ingeschakeld en wilt dat de externe gebruikers toegang hebben tot deze opslagaccounts via de VPN-verbinding. U het IP-adres van het opslageindpunt adverteren voor al uw externe gebruikers, zodat het verkeer naar het opslagaccount over de VPN-tunnel gaat en niet het openbare internet.

![Voorbeeld van een verbinding tussen meerdere locaties met Azure VPN Gateway](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Aangepaste routes adverteren

Als u aangepaste routes `Set-AzVirtualNetworkGateway cmdlet`wilt adverteren, gebruikt u de . In het volgende voorbeeld ziet u hoe u het IP-adres adverteert voor de [contoso-opslagaccounttabellen](https://contoso.table.core.windows.net).

1. Ping *contoso.table.core.windows.net* en noteer het IP-adres. Bijvoorbeeld:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Voer de volgende PowerShell-opdrachten uit:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Als u meerdere aangepaste routes wilt toevoegen, gebruikt u een coma en spaties om de adressen te scheiden. Bijvoorbeeld:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Aangepaste routes weergeven

Gebruik het volgende voorbeeld om aangepaste routes weer te geven:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Aangepaste routes verwijderen

Gebruik het volgende voorbeeld om aangepaste routes te verwijderen:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Volgende stappen

Zie Over routering van punt tot locatie voor aanvullende [P2S-routeringsgegevens.](vpn-gateway-about-point-to-site-routing.md)
