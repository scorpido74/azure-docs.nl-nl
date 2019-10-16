---
title: Over ExpressRoute virtuele netwerk gateways-Azure | Microsoft Docs
description: Meer informatie over virtuele netwerk gateways voor ExpressRoute. Dit artikel bevat informatie over Gateway-Sku's en-typen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ba03d643c8d3770da60d4225d6c2b84d2a07766f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325542"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Virtuele ExpressRoute-netwerkgateway en FastPath
Als u uw virtuele Azure-netwerk en uw on-premises netwerk wilt verbinden via ExpressRoute, moet u eerst een virtuele netwerk gateway maken. Een virtuele netwerk gateway fungeert twee doelen: IP-routes uitwisselen tussen de netwerken en netwerk verkeer routeren. In dit artikel worden de gateway typen, gateway-Sku's en geschatte prestaties per SKU uitgelegd. In dit artikel wordt ook ExpressRoute [FastPath](#fastpath)beschreven, een functie waarmee het netwerk verkeer van uw on-premises netwerk wordt omzeild om de virtuele netwerk gateway te omzeilen om de prestaties te verbeteren.

## <a name="gateway-types"></a>Gatewaytypen

Wanneer u een virtuele netwerk gateway maakt, moet u verschillende instellingen opgeven. Een van de vereiste instellingen, '-gateway type ', geeft aan of de gateway wordt gebruikt voor ExpressRoute of VPN-verkeer. De twee gateway typen zijn:

* **VPN** : als u versleuteld verkeer via het open bare Internet wilt verzenden, gebruikt u het gateway type ' VPN '. Dit wordt ook wel een VPN-gateway genoemd. Site-naar-site-, punt-naar-site- en VNet-naar-VNet-verbindingen gebruiken allemaal een VPN-gateway.

* **ExpressRoute** : als u netwerk verkeer wilt verzenden op een particuliere verbinding, gebruikt u het gateway type ' ExpressRoute '. Dit wordt ook wel een ExpressRoute-gateway genoemd en is het type gateway dat wordt gebruikt bij het configureren van ExpressRoute.

Elk virtueel netwerk kan maar één virtuele netwerkgateway per type gateway hebben. U kunt voor een virtueel netwerk bijvoorbeeld één gateway gebruiken die -GatewayType Vpn gebruikt en één die -GatewayType ExpressRoute gebruikt.

## <a name="gwsku"></a>Gateway-SKU's
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Als u uw gateway wilt bijwerken naar een krachtigere gateway-SKU, kunt u in de meeste gevallen de Power shell-cmdlet ' resize-AzVirtualNetworkGateway ' gebruiken. Dit werkt voor upgrades naar Standard-en high performance-Sku's. Als u echter een upgrade naar de Ultra Performance-SKU wilt uitvoeren, moet u de gateway opnieuw maken. Het opnieuw maken van een gateway leidt tot uitval tijd.

### <a name="aggthroughput"></a>Geschatte prestaties per gateway-SKU
In de volgende tabel ziet u de gateway typen en de geschatte prestaties. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Toepassings prestaties zijn afhankelijk van meerdere factoren, zoals de end-to-end-latentie en het aantal verkeer dat de toepassing opent. De getallen in de tabel vertegenwoordigen de bovengrens die de toepassing kan betreffen in een ideale omgeving.
>
>

### <a name="zrgw"></a>Zone-redundante gateway-Sku's

U kunt ook ExpressRoute-gateways implementeren in Azure-beschikbaarheidszones. Deze worden fysiek en logisch gescheiden in verschillende Beschikbaarheidszones, waarbij uw on-premises netwerk connectiviteit met Azure wordt beschermd tegen storingen op zone niveau.

![Zone-redundante ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone-redundante gateways gebruiken specifieke nieuwe gateway-Sku's voor ExpressRoute-gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nieuwe gateway-Sku's bieden ook ondersteuning voor andere implementatie opties die het beste aansluiten bij uw behoeften. Wanneer u een virtuele netwerk gateway maakt met behulp van de nieuwe gateway-Sku's, hebt u ook de mogelijkheid om de gateway in een specifieke zone te implementeren. Dit wordt een zonegebonden-gateway genoemd. Wanneer u een zonegebonden-gateway implementeert, worden alle exemplaren van de gateway geïmplementeerd in dezelfde beschikbaarheids zone.

## <a name="fastpath"></a>FastPath
ExpressRoute virtuele netwerk gateway is ontworpen voor het uitwisselen van netwerk routes en het routeren van netwerk verkeer. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en het virtuele netwerk te verbeteren. Als deze functie is ingeschakeld, verzendt FastPath netwerk verkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild. 

FastPath is beschikbaar op alle ExpressRoute-circuits. Er is nog een virtuele netwerk gateway vereist voor het uitwisselen van routes tussen het virtuele netwerk en het on-premises netwerk. De gateway van het virtuele netwerk moet Ultra Performance of ErGw3AZ zijn.

FastPath biedt geen ondersteuning voor de volgende functies:
* UDR op Gateway-subnet: als u een UDR toepast op het gateway-subnet van het virtuele netwerk, wordt het netwerk verkeer van uw on-premises netwerk naar de gateway van het virtuele netwerk verzonden.
* VNet-peering: als u andere virtuele netwerken hebt die zijn gekoppeld aan het netwerk verkeer van uw on-premises netwerk naar de andere virtuele netwerken (dat wil zeggen de zogenaamde ' spoke ' VNets), blijven worden verzonden naar het virtuele netwerk. #b0. De tijdelijke oplossing is om alle virtuele netwerken rechtstreeks aan het ExpressRoute-circuit te koppelen.
* Basis Load Balancer: als u een intern interne load balancer in uw virtuele netwerk implementeert of de Azure PaaS-service die u in uw virtuele netwerk implementeert, maakt gebruik van een Basic interne load balancer, het netwerk verkeer van uw on-premises netwerk naar de virtuele IP-adressen die worden gehost op de Basis load balancer worden verzonden naar de gateway van het virtuele netwerk. De oplossing is het bijwerken van de basis load balancer naar een [standaard Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
* Privé-koppeling: als u vanuit uw on-premises netwerk verbinding maakt met een [persoonlijk eind punt](../private-link/private-link-overview.md) in uw virtuele netwerk, gaat de verbinding via de gateway van het virtuele netwerk.
 
## <a name="resources"></a>REST Api's en Power shell-cmdlets
Zie de volgende pagina's voor aanvullende technische bronnen en specifieke syntaxis vereisten voor het gebruik van REST Api's en Power shell-cmdlets voor configuraties van virtuele netwerk gateways:

| **Klassiek** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van ExpressRoute](expressroute-introduction.md) voor meer informatie over de beschik bare verbindings configuraties.

Zie [een virtuele netwerk gateway maken voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) voor meer informatie over het maken van ExpressRoute-gateways.

Zie [een zone maken-redundante virtuele netwerk gateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) voor meer informatie over het configureren van zone-redundante gateways.

Zie [virtueel netwerk koppelen aan ExpressRoute](expressroute-howto-linkvnet-arm.md) voor meer informatie over het inschakelen van FastPath. 
