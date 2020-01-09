---
title: Over Azure ExpressRoute FastPath
description: Meer informatie over Azure ExpressRoute FastPath voor het verzenden van netwerk verkeer door de gateway over te slaan
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: c03be46207e7698d0557729c546488412c0cc5dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437104"
---
# <a name="about-expressroute-fastpath"></a>Over ExpressRoute FastPath

ExpressRoute virtuele netwerk gateway is ontworpen voor het uitwisselen van netwerk routes en het routeren van netwerk verkeer. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en het virtuele netwerk te verbeteren. Als deze functie is ingeschakeld, verzendt FastPath netwerk verkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild.

## <a name="requirements"></a>Vereisten

### <a name="circuits"></a>Circuits

FastPath is beschikbaar op alle ExpressRoute-circuits.

### <a name="gateways"></a>Gateways

FastPath moet nog een virtuele netwerk gateway maken voor het uitwisselen van routes tussen het virtuele netwerk en het on-premises netwerk. Zie [virtuele netwerk gateways van ExpressRoute](expressroute-about-virtual-network-gateways.md)voor meer informatie over virtuele netwerk gateways en ExpressRoute, met inbegrip van prestatie gegevens en gateway-sku's.

Voor het configureren van FastPath moet de gateway van het virtuele netwerk een van de volgende zijn:

* Ultra Performance
* ErGw3AZ

#### <a name="aggthroughput"></a>Geschatte prestaties per gateway-SKU
De volgende tabel ziet u de gatewaytypen en de geschatte prestaties. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> De prestaties van toepassingen, is afhankelijk van meerdere factoren, zoals de end-to-end-latentie en het aantal verkeer stroomt van die de toepassing wordt geopend. De getallen in de tabel staan de bovengrens die de toepassing in theorie in een ideale omgeving bereiken kan.
>
>

## <a name="supported-features"></a>Ondersteunde functies

Hoewel FastPath de meeste configuraties ondersteunt, biedt het geen ondersteuning voor de volgende functies:

* UDR op het gateway-subnet: als u een UDR toepast op het gateway-subnet van het virtuele netwerk, wordt het netwerk verkeer van uw on-premises netwerk naar de gateway van het virtuele netwerk verzonden.

* VNet-peering: als u andere virtuele netwerken hebt die zijn gekoppeld aan ExpressRoute, wordt het netwerk verkeer van uw on-premises netwerk naar de andere virtuele netwerken (d.w.z. de zogenaamde "spoke" VNets) naar het virtuele netwerk verzonden. #b0. De tijdelijke oplossing is om alle virtuele netwerken rechtstreeks aan het ExpressRoute-circuit te koppelen.

* Basis Load Balancer: als u een intern interne load balancer in uw virtuele netwerk implementeert of de Azure PaaS-service die u in uw virtuele netwerk implementeert, maakt gebruik van een Basic interne load balancer, het netwerk verkeer van uw on-premises netwerk naar de virtuele IP-adressen die worden gehost op de Basis load balancer worden verzonden naar de gateway van het virtuele netwerk. De oplossing is het bijwerken van de basis load balancer naar een [standaard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privé-koppeling: als u vanuit uw on-premises netwerk verbinding maakt met een [persoonlijk eind punt](../private-link/private-link-overview.md) in uw virtuele netwerk, wordt de verbinding via de gateway van het virtuele netwerk door lopen.
 
## <a name="next-steps"></a>Volgende stappen

Zie [een virtueel netwerk koppelen aan ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)om FastPath in te scha kelen.
