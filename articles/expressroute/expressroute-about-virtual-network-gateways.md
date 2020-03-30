---
title: Over Virtuele Netwerkgateways van ExpressRoute - Azure| Microsoft Documenten
description: Meer informatie over virtuele netwerkgateways voor ExpressRoute. Dit artikel bevat informatie over gateway SKU's en typen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281416"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Over ExpressRoute virtuele netwerkgateways

Als u uw virtuele Azure-netwerk en uw on-premises netwerk via ExpressRoute wilt verbinden, moet u eerst een virtuele netwerkgateway maken. Een virtuele netwerkgateway dient twee doelen: uitwisseling van IP-routes tussen de netwerken en routenetwerkverkeer. In dit artikel worden gatewaytypen, gateway-SKU's en geschatte prestaties van SKU uitgelegd. In dit artikel wordt ook uitgelegd ExpressRoute [FastPath](#fastpath), een functie waarmee het netwerkverkeer van uw on-premises netwerk de virtuele netwerkgateway kan omzeilen om de prestaties te verbeteren.

## <a name="gateway-types"></a>Gatewaytypen

Wanneer u een virtuele netwerkgateway maakt, moet u verschillende instellingen opgeven. Een van de vereiste instellingen, '-GatewayType', geeft aan of de gateway wordt gebruikt voor ExpressRoute of VPN-verkeer. De twee gatewaytypen zijn:

* **Vpn** - Om versleuteld verkeer over het openbare internet te verzenden, gebruikt u het gatewaytype 'Vpn'. Dit wordt ook wel een VPN-gateway genoemd. Site-naar-site-, punt-naar-site- en VNet-naar-VNet-verbindingen gebruiken allemaal een VPN-gateway.

* **ExpressRoute** - Om netwerkverkeer op een privéverbinding te verzenden, gebruikt u het gatewaytype 'ExpressRoute'. Dit wordt ook wel een ExpressRoute-gateway genoemd en is het type gateway dat wordt gebruikt bij het configureren van ExpressRoute.

Elk virtueel netwerk kan maar één virtuele netwerkgateway per type gateway hebben. U kunt voor een virtueel netwerk bijvoorbeeld één gateway gebruiken die -GatewayType Vpn gebruikt en één die -GatewayType ExpressRoute gebruikt.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Als u uw gateway wilt upgraden naar een krachtigere gateway SKU, u in de meeste gevallen de PowerShell-cmdlet 'Resize-AzVirtualNetworkGateway' gebruiken. Dit werkt voor upgrades naar Standard en HighPerformance SKU's. Als u echter wilt upgraden naar de UltraPerformance SKU, moet u de gateway opnieuw maken. Het opnieuw maken van een gateway leidt tot downtime.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Geschatte prestaties van gateway SKU
In de volgende tabel worden de gatewaytypen en de geschatte prestaties weergegeven. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Toepassingsprestaties zijn afhankelijk van meerdere factoren, zoals de end-to-end latentie en het aantal verkeersstromen dat de toepassing opent. De getallen in de tabel vertegenwoordigen de bovengrens die de toepassing theoretisch kan bereiken in een ideale omgeving.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewaysubnet

Voordat u een ExpressRoute-gateway maakt, moet u een gateway-subnet maken. Het gatewaysubnet bevat de IP-adressen die de virtuele netwerkgateway VM's en -services gebruiken. Wanneer u uw virtuele netwerkgateway maakt, worden gateway-VM's geïmplementeerd op het gatewaysubnet en geconfigureerd met de vereiste ExpressRoute-gateway-instellingen. Implementeer nooit iets anders (bijvoorbeeld extra VM's) naar het gatewaysubnet. Het gatewaysubnet moet 'GatewaySubnet' heten om goed te kunnen werken. Als u het gateway-subnet 'GatewaySubnet' noemt, laat Azure weten dat dit het subnet is om de virtuele netwerkgateway VM's en -services te implementeren.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gatewaysubnet worden toegewezen aan de gateway-VM's en gatewayservices. Sommige configuraties vereisen meer IP-adressen dan andere. 

Wanneer u uw subnetgrootte van de gateway plant, raadpleegt u de documentatie voor de configuratie die u wilt maken. De ExpressRoute/VPN Gateway-coëxistentieconfiguratie vereist bijvoorbeeld een groter gatewaysubnet dan de meeste andere configuraties. Bovendien u ervoor zorgen dat uw gatewaysubnet voldoende IP-adressen bevat om eventuele toekomstige aanvullende configuraties mogelijk te kunnen aanpassen. Hoewel u een gateway-subnet zo klein als /29 maken, raden we u aan een gateway-subnet van /27 of groter (/27, /26 enz.) te maken als u de beschikbare adresruimte hebt om dit te doen. Dit is geschikt voor de meeste configuraties.

In het volgende voorbeeld van Resource Manager PowerShell wordt een gateway-subnet met de naam GatewaySubnet weergegeven. U de CIDR-notatie een /27 zien, waardoor er voldoende IP-adressen zijn voor de meeste configuraties die momenteel bestaan.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zoneredundante gateway SKU's

U ExpressRoute-gateways ook implementeren in Azure Availability Zones. Dit scheidt ze fysiek en logisch in verschillende beschikbaarheidszones, waardoor uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen fouten op zoneniveau.

![Zoneredundante ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zoneredundante gateways maken gebruik van specifieke nieuwe gateway SKU's voor ExpressRoute gateway.

* ErGw1AZ
* ErGw2AZ ErGw2AZ
* ErGw3AZ

De nieuwe gateway SKU's ondersteunen ook andere implementatieopties die het beste bij uw behoeften passen. Wanneer u een virtuele netwerkgateway maakt met de nieuwe gateway-SKU's, hebt u ook de mogelijkheid om de gateway in een specifieke zone te implementeren. Dit wordt aangeduid als een zonale gateway. Wanneer u een zonale gateway implementeert, worden alle exemplaren van de gateway geïmplementeerd in dezelfde beschikbaarheidszone.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute virtual network gateway is ontworpen om netwerkroutes uit te wisselen en netwerkverkeer te routeren. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en uw virtuele netwerk te verbeteren. Wanneer fastpath is ingeschakeld, stuurt het netwerkverkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild.

Zie [Over FastPath](about-fastpath.md)voor meer informatie over FastPath, inclusief beperkingen en vereisten.

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API's en PowerShell-cmdlets
Zie de volgende pagina's voor aanvullende technische bronnen en specifieke syntaxisvereisten bij het gebruik van REST API's en PowerShell-cmdlets voor virtuele netwerkgatewayconfiguraties:

| **Klassiek** | **Resource Manager** |
| --- | --- |
| [Powershell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Powershell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST-API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST-API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Volgende stappen

Zie [Overzicht van ExpressRoute](expressroute-introduction.md)voor meer informatie over beschikbare verbindingsconfiguraties.

Zie [Een virtuele netwerkgateway voor ExpressRoute maken voor](expressroute-howto-add-gateway-resource-manager.md)meer informatie over het maken van ExpressRoute-gateways.

Zie [Een zoneredundante virtuele netwerkgateway maken](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)voor meer informatie over het configureren van zoneredundante gateways.

Zie Over FastPath voor meer informatie over [FastPath.](about-fastpath.md)