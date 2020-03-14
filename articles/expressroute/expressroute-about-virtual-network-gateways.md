---
title: Over ExpressRoute-gateways voor virtueel netwerk - Azure | Microsoft Docs
description: Meer informatie over virtuele netwerkgateways voor ExpressRoute. In dit artikel bevat informatie over gateway-SKU's en -typen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281416"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Over ExpressRoute van virtuele netwerk gateways

Als u uw virtuele Azure-netwerk en uw on-premises netwerk wilt verbinden via ExpressRoute, moet u eerst een virtuele netwerk gateway maken. Een virtuele netwerk gateway fungeert twee doelen: IP-routes uitwisselen tussen de netwerken en netwerk verkeer routeren. In dit artikel worden de gateway typen, gateway-Sku's en geschatte prestaties per SKU uitgelegd. In dit artikel wordt ook ExpressRoute [FastPath](#fastpath)beschreven, een functie waarmee het netwerk verkeer van uw on-premises netwerk wordt omzeild om de virtuele netwerk gateway te omzeilen om de prestaties te verbeteren.

## <a name="gateway-types"></a>Gatewaytypen

Wanneer u een virtuele netwerkgateway maakt, moet u verschillende instellingen opgeven. Een van de vereiste instellingen '-GatewayType', geeft aan of de gateway wordt gebruikt voor ExpressRoute of VPN-verkeer. De twee gatewaytypen zijn:

* **VPN** : als u versleuteld verkeer via het open bare Internet wilt verzenden, gebruikt u het gateway type ' VPN '. Dit is ook een VPN-gateway genoemd. Site-naar-site-, punt-naar-site- en VNet-naar-VNet-verbindingen gebruiken allemaal een VPN-gateway.

* **ExpressRoute** : als u netwerk verkeer wilt verzenden op een particuliere verbinding, gebruikt u het gateway type ' ExpressRoute '. Dit wordt ook wel een ExpressRoute-gateway genoemd en is van het type van de gateway die wordt gebruikt bij het configureren van ExpressRoute.

Elk virtueel netwerk kan maar één virtuele netwerkgateway per type gateway hebben. U kunt voor een virtueel netwerk bijvoorbeeld één gateway gebruiken die -GatewayType Vpn gebruikt en één die -GatewayType ExpressRoute gebruikt.

## <a name="gwsku"></a>Gateway-SKU's
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Als u uw gateway wilt bijwerken naar een krachtigere gateway-SKU, kunt u in de meeste gevallen de Power shell-cmdlet ' resize-AzVirtualNetworkGateway ' gebruiken. Dit werkt voor upgrades naar Standard en HighPerformance-SKU's. Echter, om te upgraden naar de SKU UltraPerformance, moet u de gateway opnieuw maken. Opnieuw maken van een gateway veroorzaakt uitvaltijd.

### <a name="aggthroughput"></a>Geschatte prestaties per gateway-SKU
De volgende tabel ziet u de gatewaytypen en de geschatte prestaties. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> De prestaties van toepassingen, is afhankelijk van meerdere factoren, zoals de end-to-end-latentie en het aantal verkeer stroomt van die de toepassing wordt geopend. De getallen in de tabel staan de bovengrens die de toepassing in theorie in een ideale omgeving bereiken kan.
>
>

## <a name="gwsub"></a>Gateway-subnet

Voordat u een ExpressRoute-gateway maakt, moet u een gateway-subnet maken. Het gateway-subnet bevat de IP-adressen die door de virtuele netwerk gateway-Vm's en-services worden gebruikt. Wanneer u de virtuele netwerk gateway maakt, worden gateway-Vm's geïmplementeerd naar het gateway-subnet en geconfigureerd met de vereiste ExpressRoute-gateway-instellingen. Implementeer nooit iets anders (bijvoorbeeld extra Vm's) naar het gateway-subnet. Het gateway-subnet moet de naam ' GatewaySubnet ' hebben om goed te kunnen werken. Als u het gateway-subnet ' GatewaySubnet ' noemt, weet Azure dat dit het subnet is voor het implementeren van de virtuele netwerk gateway-Vm's en-services naar.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gateway-subnet worden toegewezen aan de gateway-Vm's en Gateway Services. Sommige configuraties vereisen meer IP-adressen dan andere. 

Wanneer u de grootte van het gateway-subnet plant, raadpleegt u de documentatie voor de configuratie die u wilt maken. Zo is voor de configuratie ExpressRoute/VPN Gateway naast elkaar een groter gateway-subnet vereist dan de meeste andere configuraties. Daarnaast wilt u er mogelijk voor zorgen dat uw gateway subnet voldoende IP-adressen bevat voor mogelijke toekomstige extra configuraties. Hoewel u een gateway-subnet kunt maken als/29, raden we u aan een gateway-subnet van/27 of groter te maken (/27,/26 enzovoort) als u over de beschik bare adres ruimte beschikt. Dit is geschikt voor de meeste configuraties.

In het volgende voor beeld van Resource Manager Power shell wordt een gateway-subnet met de naam GatewaySubnet weer gegeven. U kunt in de CIDR-notatie een/27 opgeven, zodat er voldoende IP-adressen zijn voor de meeste configuraties die momenteel bestaan.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>Zone-redundante gateway-Sku's

U kunt ook ExpressRoute-gateways in Azure-Beschikbaarheidszones implementeren. Dit fysiek en logisch gescheiden in verschillende Beschikbaarheidszones, uw on-premises netwerkconnectiviteit naar Azure beveiligen tegen storingen van de zone-niveau.

![Zone-redundante ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone-redundante gateways specifieke nieuwe gateway-SKU's voor ExpressRoute-gateway gebruiken.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nieuwe gateway-SKU's ondersteunen ook andere implementatie-opties om het beste aan uw behoeften te voldoen. Bij het maken van een virtuele netwerkgateway met behulp van de nieuwe gateway-SKU's, hebt u ook de mogelijkheid om de gateway in een specifieke zone te implementeren. Dit is een zonegebonden gateway genoemd. Wanneer u een zonegebonden gateway implementeert, worden alle exemplaren van de gateway in dezelfde Beschikbaarheidszone geïmplementeerd.

## <a name="fastpath"></a>FastPath

ExpressRoute virtuele netwerk gateway is ontworpen voor het uitwisselen van netwerk routes en het routeren van netwerk verkeer. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en het virtuele netwerk te verbeteren. Als deze functie is ingeschakeld, verzendt FastPath netwerk verkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild.

Zie [about FastPath](about-fastpath.md)(Engelstalig) voor meer informatie over FastPath, met inbegrip van beperkingen en vereisten.

## <a name="resources"></a>REST Api's en Power shell-cmdlets
Zie voor aanvullende technische bronnen en de syntaxis van de specifieke vereisten bij het gebruik van REST-API's en PowerShell-cmdlets voor configuraties van virtuele gateway, de volgende pagina's:

| **Klassiek** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST-API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST-API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Volgende stappen

Zie [ExpressRoute Overview](expressroute-introduction.md)(Engelstalig) voor meer informatie over de beschik bare verbindings configuraties.

Zie [een virtuele netwerk gateway maken voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)voor meer informatie over het maken van ExpressRoute-gateways.

Zie [een zone maken-redundante virtuele netwerk gateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)voor meer informatie over het configureren van zone-redundante gateways.

Zie [about FastPath](about-fastpath.md)(Engelstalig) voor meer informatie over FastPath.