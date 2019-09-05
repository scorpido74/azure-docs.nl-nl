---
title: Over ExpressRoute-gateways voor virtueel netwerk - Azure | Microsoft Docs
description: Meer informatie over virtuele netwerkgateways voor ExpressRoute. In dit artikel bevat informatie over gateway-SKU's en -typen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 5b74e387c6bee58acbbb7bae320a9bc72a4dda1c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376282"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Virtuele ExpressRoute-netwerkgateway en FastPath
Als u uw virtuele Azure-netwerk en uw on-premises netwerk wilt verbinden via ExpressRoute, moet u eerst een virtuele netwerk gateway maken. Een virtuele netwerk gateway fungeert twee doelen: IP-routes uitwisselen tussen de netwerken en netwerk verkeer routeren. In dit artikel worden de gateway typen, gateway-Sku's en geschatte prestaties per SKU uitgelegd. In dit artikel wordt ook ExpressRoute [FastPath](#fastpath)beschreven, een functie waarmee het netwerk verkeer van uw on-premises netwerk wordt omzeild om de virtuele netwerk gateway te omzeilen om de prestaties te verbeteren.

## <a name="gateway-types"></a>Gatewaytypen

Wanneer u een virtuele netwerkgateway maakt, moet u verschillende instellingen opgeven. Een van de vereiste instellingen '-GatewayType', geeft aan of de gateway wordt gebruikt voor ExpressRoute of VPN-verkeer. De twee gatewaytypen zijn:

* **VPN** - versleuteld verkeer wordt verzonden via het openbare Internet, gebruikt u het Gatewaytype 'Vpn'. Dit is ook een VPN-gateway genoemd. Site-naar-site-, punt-naar-site- en VNet-naar-VNet-verbindingen gebruiken allemaal een VPN-gateway.

* **ExpressRoute** : als u wilt verzenden van netwerkverkeer op een privéverbinding, gebruikt u het Gatewaytype 'ExpressRoute'. Dit wordt ook wel een ExpressRoute-gateway genoemd en is van het type van de gateway die wordt gebruikt bij het configureren van ExpressRoute.

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

### <a name="zrgw"></a>Zone-redundante gateway-SKU 's

U kunt ook ExpressRoute-gateways in Azure-Beschikbaarheidszones implementeren. Dit fysiek en logisch gescheiden in verschillende Beschikbaarheidszones, uw on-premises netwerkconnectiviteit naar Azure beveiligen tegen storingen van de zone-niveau.

![Zone-redundante ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone-redundante gateways specifieke nieuwe gateway-SKU's voor ExpressRoute-gateway gebruiken.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nieuwe gateway-SKU's ondersteunen ook andere implementatie-opties om het beste aan uw behoeften te voldoen. Bij het maken van een virtuele netwerkgateway met behulp van de nieuwe gateway-SKU's, hebt u ook de mogelijkheid om de gateway in een specifieke zone te implementeren. Dit is een zonegebonden gateway genoemd. Wanneer u een zonegebonden gateway implementeert, worden alle exemplaren van de gateway in dezelfde Beschikbaarheidszone geïmplementeerd.

## <a name="fastpath"></a>FastPath
ExpressRoute virtuele netwerk gateway is ontworpen voor het uitwisselen van netwerk routes en het routeren van netwerk verkeer. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en het virtuele netwerk te verbeteren. Als deze functie is ingeschakeld, verzendt FastPath netwerk verkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild. 

FastPath is alleen beschikbaar op [ExpressRoute direct](expressroute-erdirect-about.md) . Met andere woorden, u kunt deze functie alleen inschakelen als u [het virtuele netwerk verbindt](expressroute-howto-linkvnet-arm.md) met een ExpressRoute-circuit dat is gemaakt op een ExpressRoute-directe poort. FastPath moet nog een virtuele netwerk gateway maken voor het uitwisselen van routes tussen het virtuele netwerk en het on-premises netwerk. De gateway van het virtuele netwerk moet Ultra Performance of ErGw3AZ zijn.

FastPath biedt geen ondersteuning voor de volgende functies:
* UDR op Gateway-subnet: als u een UDR toepast op het gateway-subnet van het virtuele netwerk, wordt het netwerk verkeer van uw on-premises netwerk naar de gateway van het virtuele netwerk verzonden.
* VNet-peering: als u andere virtuele netwerken hebt die zijn gekoppeld aan het netwerk verkeer van uw on-premises netwerk naar de andere virtuele netwerken (dat wil zeggen de zogenaamde ' spoke ' VNets), blijven worden verzonden naar het virtuele netwerk. #b0. De tijdelijke oplossing is om alle virtuele netwerken rechtstreeks aan het ExpressRoute-circuit te koppelen.
* Basic Load Balander: als u een interne Basic-load balancer implementeert in uw virtuele netwerk of de Azure PaaS-service die u in uw virtuele netwerk implementeert, maakt gebruik van een Basic interne load balancer, het netwerk verkeer van uw on-premises netwerk naar de virtuele IP-adressen die worden gehost op de Basis load balancer worden verzonden naar de gateway van het virtuele netwerk. De oplossing is het bijwerken van de basis load balancer naar een [standaard Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview). 
 
## <a name="resources"></a>REST-API's en PowerShell-cmdlets
Zie voor aanvullende technische bronnen en de syntaxis van de specifieke vereisten bij het gebruik van REST-API's en PowerShell-cmdlets voor configuraties van virtuele gateway, de volgende pagina's:

| **Klassiek** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van ExpressRoute](expressroute-introduction.md) voor meer informatie over beschikbare verbindingsconfiguraties.

Zie [een virtuele netwerkgateway maken voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) voor meer informatie over het maken van ExpressRoute-gateways.

Zie [maken van een zone-redundante virtuele-netwerkgateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) voor meer informatie over het configureren van de zone-redundante gateways.

Zie [virtueel netwerk koppelen aan ExpressRoute](expressroute-howto-linkvnet-arm.md) voor meer informatie over het inschakelen van FastPath. 
