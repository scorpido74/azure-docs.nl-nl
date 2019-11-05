---
title: Aangepaste verkeers selecters voor Azure VPN Gateway-verbindingen | Microsoft Docs
description: Meer informatie over het gebruik van aangepaste verkeers selecties op VPN Gateway verbindingen
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512156"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Aangepaste verkeers selecties voor VPN Gateway verbindingen

U kunt aangepaste verkeers selecties instellen op uw VPN-gateway verbindingen. Met de mogelijkheid om verkeer te selecteren, kunt u de adres ruimten beperken aan beide zijden van de VPN-tunnels waarvoor u verkeer wilt verzenden. Aangepaste verkeers selecties zijn vooral handig wanneer u een grote VNet-adres ruimte hebt, maar u een van uw subnetten wilt gebruiken voor IPsec/IKE-onderhandeling.

Aangepaste Traffic-selecters kunnen worden toegevoegd tijdens het maken van een nieuwe verbinding, of kunnen worden bijgewerkt voor een bestaande verbinding. De para meter `TrafficSelectorPolicies` bestaat uit een matrix met verkeers selecties. Elke verkeers kiezer bevat een verzameling lokale en externe adresbereiken in CIDR-indeling.

## <a name="add-custom-traffic-selectors"></a>Aangepaste verkeers selecties toevoegen

In de volgende voor beelden ziet u hoe aangepaste verkeers selecties kunnen worden gemaakt voor een virtuele netwerk gateway verbinding met behulp van Power shell-opdrachten. Zie [een site-naar-site-verbinding configureren](vpn-gateway-create-site-to-site-rm-powershell.md)voor meer informatie over het maken van een virtuele netwerk gateway verbinding.

1. Stel waarden in voor de para meter *trafficselectorpolicies* in $trafficselectorpolicy. Wanneer u dit instelt, moet u er rekening mee houden dat de waarde *New-AzTrafficSelectorPolicy* lokale en externe adresbereiken in een matrix heeft.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Maak een nieuwe virtuele netwerk gateway verbinding. Wijzig de waarde-para meters voor uw vereisten.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

U kunt ook aangepaste verkeers selecties voor een bestaande virtuele netwerk gateway verbinding bijwerken met behulp van ' set-AzVirtualNetworkGatewayConnection '. Zie [verbinding met virtuele netwerk gateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?)voor Power shell-waarden.

## <a name="next-steps"></a>Volgende stappen

Zie [over VPN gateway](vpn-gateway-about-vpngateways.md)voor meer informatie over VPN-gateways.