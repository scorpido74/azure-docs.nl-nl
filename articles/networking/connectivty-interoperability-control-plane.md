---
title: 'Interoperabiliteit in Azure : Analyse van besturingsvlak'
description: In dit artikel vindt u de analyse van het controlevlak van de testopstelling die u gebruiken om de interoperabiliteit tussen ExpressRoute, een site-to-site VPN en virtueel netwerkpeering in Azure te analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518275"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabiliteit in Azure : Analyse van besturingsvlak

In dit artikel wordt de analyse van het controlevlak van de [testopstelling][Setup]beschreven. U ook de configuratie van de [testinstallatie][Configuration] en de analyse van het [gegevensvlak][Data-Analysis] van de testopstelling bekijken.

Control plane analyse onderzoekt in wezen routes die worden uitgewisseld tussen netwerken binnen een topologie. Controlevlakanalyse kan u helpen te begrijpen hoe verschillende netwerken de topologie bekijken.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub en spaak VNet perspectief

De volgende figuur illustreert het netwerk vanuit het perspectief van een hub virtueel netwerk (VNet) en een spaak VNet (gemarkeerd in het blauw). De figuur toont ook het autonome systeemnummer (ASN) van verschillende netwerken en routes die worden uitgewisseld tussen verschillende netwerken: 

![1][1]

De ASN van de Azure ExpressRoute-gateway van VNet verschilt van het ASN van Microsoft Enterprise Edge Routers (MEEs). Een ExpressRoute-gateway maakt gebruik van een privé ASN (een waarde van **65515)** en MeEs gebruiken openbare ASN (een waarde van **12076)** wereldwijd. Wanneer u ExpressRoute-peering configureert, omdat MSEE de peer is, gebruikt u **12076** als peer ASN. Aan de Azure-kant stelt MSEE eBGP-peering vast met de ExpressRoute-gateway. De dubbele eBGP-peering die de MSEE voor elke ExpressRoute-peering vaststelt, is transparant op het niveau van het controlevlak. Wanneer u daarom een routetabel ExpressRoute bekijkt, ziet u de ExpressRoute-gateway ASN van de VNet-gateway voor de voorvoegsels van de VNet. 

De volgende figuur toont een voorbeeld expressroutetabel: 

![5][5]

Binnen Azure is het ASN alleen belangrijk vanuit een peering-perspectief. Standaard is de ASN van zowel de ExpressRoute-gateway als de VPN-gateway in Azure VPN Gateway **65515.**

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>On-premises Locatie 1 en het externe VNet-perspectief via ExpressRoute 1

Zowel on-premises Locatie 1 als het externe VNet zijn via ExpressRoute 1 verbonden met de hub VNet. Ze delen hetzelfde perspectief van de topologie, zoals in het volgende diagram:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>On-premises Locatie 1 en het branch VNet perspectief via een site-to-site VPN

Zowel on-premises Locatie 1 als de branch VNet zijn verbonden met de VPN-gateway van een hub VNet via een site-to-site VPN-verbinding. Ze delen hetzelfde perspectief van de topologie, zoals in het volgende diagram:

![3][3]

## <a name="on-premises-location-2-perspective"></a>On-premises locatie 2-perspectief

On-premises locatie 2 is verbonden met een hub VNet via private peering van ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute en site-to-site VPN-connectiviteit in tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN via ExpressRoute

U een site-to-site VPN configureren door ExpressRoute Microsoft te gebruiken om privé gegevens uit te wisselen tussen uw on-premises netwerk en uw Azure VNets. Met deze configuratie u gegevens uitwisselen met vertrouwelijkheid, authenticiteit en integriteit. De gegevensuitwisseling is ook anti-replay. Zie [Site-to-site VPN via ExpressRoute Microsoft peering][S2S-Over-ExR]voor meer informatie over het configureren van een vpn van site-to-site in tunnelmodus met ExpressRoute Microsoft-peering. 

De primaire beperking van het configureren van een site-to-site VPN die Microsoft-peering gebruikt, is doorvoer. Doorvoer over de IPsec-tunnel wordt beperkt door de VPN-gatewaycapaciteit. De VPN-gatewaydoorvoer is lager dan de doorvoer van ExpressRoute. In dit scenario helpt het gebruik van de IPsec-tunnel voor zeer veilig verkeer en het gebruik van privé-peering voor al het andere verkeer het gebruik van de ExpressRoute-bandbreedte optimaliseren.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN als een veilig failoverpad voor ExpressRoute

ExpressRoute fungeert als een redundant circuit paar om een hoge beschikbaarheid te garanderen. U georedundante ExpressRoute-connectiviteit configureren in verschillende Azure-regio's. Zoals blijkt uit onze testopstelling, binnen een Azure-regio, u ook een site-to-site VPN gebruiken om een failoverpad voor uw ExpressRoute-connectiviteit te maken. Wanneer dezelfde voorvoegsels worden geadverteerd via zowel ExpressRoute als een site-to-site VPN, geeft Azure prioriteit aan ExpressRoute. Om asymmetrische routering tussen ExpressRoute en de site-to-site VPN te voorkomen, moet on-premises netwerkconfiguratie ook worden geciprocatedoor gebruik te maken van ExpressRoute-connectiviteit voordat deze gebruikmaakt van site-to-site VPN-connectiviteit.

Zie [ExpressRoute en site-to-site coëxistentie][ExR-S2S-CoEx]voor meer informatie over het configureren van coëxistentie van naast elkaar bestaande verbindingen voor ExpressRoute en een site-to-site VPN.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Back-endconnectiviteit uitbreiden naar spaakvnets en branchlocaties

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke VNet-connectiviteit met VNet-peering

Hub en spaak VNet architectuur wordt veel gebruikt. De hub is een VNet in Azure dat fungeert als een centraal verbindingspunt tussen uw gesproken VNets en uw on-premises netwerk. De spaken zijn VNets die peer met de hub, en die u gebruiken om workloads te isoleren. Verkeer stroomt tussen het on-premises datacenter en de hub via een ExpressRoute- of VPN-verbinding. Zie [Een netwerktopologie][Hub-n-Spoke]met hubspaak in Azure implementeren voor meer informatie over de architectuur.

In VNet-peering binnen een regio kunnen spaakvnets hub VNet-gateways (zowel VPN- als ExpressRoute-gateways) gebruiken om te communiceren met externe netwerken.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Branch VNet-connectiviteit met behulp van site-to-site VPN

U wilt misschien dat branch VNets, die zich in verschillende regio's bevinden, en on-premises netwerken met elkaar communiceren via een hub VNet. De native Azure-oplossing voor deze configuratie is site-to-site VPN-connectiviteit met behulp van een VPN. Een alternatief is het gebruik van een netwerk virtueel toestel (NVA) voor routing in de hub.

Zie [Wat is VPN Gateway voor][VPN] meer informatie en implementeer een zeer beschikbare [NVA.][Deploy-NVA]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [gegevensvlakanalyse][Data-Analysis] van de testinstallatie en azure-netwerkbewakingsfunctieweergaven.

Zie de [veelgestelde vragen over ExpressRoute][ExR-FAQ] voor:
-   Ontdek hoeveel ExpressRoute-circuits u verbinden met een ExpressRoute-gateway.
-   Ontdek hoeveel ExpressRoute-gateways u verbinden met een ExpressRoute-circuit.
-   Meer informatie over andere schaallimieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub en spaak VNet perspectief van de topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Locatie 1 en extern VNet perspectief van de topologie via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Locatie 1 en branch VNet perspectief van de topologie via een site-to-site VPN"
[4]: ./media/backend-interoperability/Loc2View.png "Locatie 2 perspectief van de topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 routetabel"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


