---
title: Over Azure ExpressRoute FastPath
description: Meer informatie over Azure ExpressRoute FastPath om netwerkverkeer te verzenden door de gateway te omzeilen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282848"
---
# <a name="about-expressroute-fastpath"></a>Over ExpressRoute FastPath

ExpressRoute virtual network gateway is ontworpen om netwerkroutes uit te wisselen en netwerkverkeer te routeren. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en uw virtuele netwerk te verbeteren. Wanneer fastpath is ingeschakeld, stuurt het netwerkverkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild.

## <a name="requirements"></a>Vereisten

### <a name="circuits"></a>Circuits

FastPath is beschikbaar op alle ExpressRoute-circuits.

### <a name="gateways"></a>Gateways

FastPath vereist nog steeds een virtuele netwerkgateway om routes uit te wisselen tussen virtueel netwerk en on-premises netwerk. Zie [ExpressRoute virtual network gateways](expressroute-about-virtual-network-gateways.md)voor meer informatie over virtuele netwerkgateways en ExpressRoute, inclusief prestatie-informatie en gateway-SKU's.

Om FastPath te configureren, moet de virtuele netwerkgateway het als volgt zijn:

* Ultraprestaties
* ErGw3AZ

## <a name="supported-features"></a>Ondersteunde functies

Hoewel FastPath de meeste configuraties ondersteunt, ondersteunt het de volgende functies niet:

* UDR op het gatewaysubnet: Als u een UDR toepast op het gatewaysubnet van uw virtuele netwerk, wordt het netwerkverkeer van uw on-premises netwerk nog steeds naar de virtuele netwerkgateway verzonden.

* VNet Peering: Als u andere virtuele netwerken hebt die zijn gekoppeld aan de netwerkverbinding met ExpressRoute, wordt het netwerkverkeer van uw on-premises netwerk naar de andere virtuele netwerken (d.w.z. de zogenaamde "Spoke" VNets) naar het virtuele netwerk verzonden Gateway. De tijdelijke oplossing is om alle virtuele netwerken rechtstreeks met het ExpressRoute-circuit te verbinden.

* Basic Load Balancer: Als u een Basic internal load balancer implementeert in uw virtuele netwerk of de Azure PaaS-service die u implementeert in uw virtuele netwerk, maakt u gebruik van een Basic interne load balancer, het netwerkverkeer van uw on-premises netwerk naar de virtuele IP's die op de Basic load balancer wordt naar de virtuele netwerkgateway verzonden. De oplossing is om de Basic load balancer te upgraden naar een [standaard load balancer.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

* Private Link: Als u verbinding maakt met een [privéeindpunt](../private-link/private-link-overview.md) in uw virtuele netwerk vanuit uw on-premises netwerk, gaat de verbinding via de virtuele netwerkgateway.
 
## <a name="next-steps"></a>Volgende stappen

Zie Een virtueel [netwerk koppelen aan ExpressRoute als](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)u FastPath wilt inschakelen.
