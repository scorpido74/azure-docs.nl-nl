---
title: Aangepaste routes adverteren voor punt-naar-site-VPN-clients | Azure | Microsoft Docs
description: Stappen voor het adverteren van aangepaste routes naar uw punt-naar-site-clients
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2019
ms.author: cherylmc
ms.openlocfilehash: 18a0effcf5157ec2797707db78f9614ef12a4669
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310241"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Aangepaste routes adverteren voor P2S VPN-clients

Mogelijk wilt u aangepaste routes adverteren naar al uw punt-naar-site-VPN-clients. Wanneer u bijvoorbeeld opslag eindpunten in uw VNet hebt ingeschakeld en wilt dat externe gebruikers toegang hebben tot deze opslag accounts via de VPN-verbinding. U kunt het IP-adres van het opslag eindpunt aankondigen aan al uw externe gebruikers, zodat het verkeer naar het opslag account via de VPN-tunnel verloopt en niet het open bare Internet.

## <a name="to-advertise-custom-routes"></a>Aangepaste routes adverteren

Gebruik de `Set-AzVirtualNetworkGateway cmdlet`om aangepaste routes te adverteren. In het volgende voor beeld ziet u hoe u het IP-adres voor de [Conotoso](https://contoso.table.core.windows.net).

1. Ping *contoso.table.core.Windows.net* en noteer het IP-adres. Bijvoorbeeld:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Voer de volgende Power shell-opdrachten uit:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Als u meerdere aangepaste routes wilt toevoegen, gebruikt u een coma en spaties om de adressen van elkaar te scheiden. Bijvoorbeeld:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Aangepaste routes weer geven

Gebruik het volgende voor beeld om aangepaste routes weer te geven:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    $gw.CustomRoutes | Format-List
    ```

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site Routing](vpn-gateway-about-point-to-site-routing.md)(Engelstalig) voor meer informatie over P2S-route ring.
