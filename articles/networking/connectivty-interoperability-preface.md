---
title: 'Interoperabiliteit in Azure : Testopstelling | Microsoft Documenten'
description: In dit artikel wordt een testopstelling beschreven die u gebruiken om de interoperabiliteit tussen ExpressRoute, een site-to-site VPN en virtueel netwerkpeeren in Azure te analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 3aec41a145d2c94a45a453393831902069b9c41b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518196"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabiliteit in Azure : Testsetup

In dit artikel wordt een testopstelling beschreven die u gebruiken om te analyseren hoe Azure-netwerkservices samenwerken op het niveau van het controlevlak en het niveau van het gegevensvlak. Laten we eens kort kijken naar de Azure-netwerkcomponenten:

-   **Azure ExpressRoute:** gebruik privé-peering in Azure ExpressRoute om privé-IP-ruimten in uw on-premises netwerk rechtstreeks te verbinden met uw Azure Virtual Network-implementaties. ExpressRoute kan u helpen een hogere bandbreedte en een privéverbinding te bereiken. Veel ExpressRoute eco-partners bieden ExpressRoute-connectiviteit met SLA's. Zie [Inleiding tot ExpressRoute][ExpressRoute]voor meer informatie over ExpressRoute en voor meer informatie over het configureren van ExpressRoute.
-   **Vpn van site-to-site**: U Azure VPN Gateway gebruiken als een site-to-site VPN om een on-premises netwerk veilig te verbinden met Azure via internet of via ExpressRoute. Zie [VPN-gateway configureren][VPN]voor meer informatie over het configureren van een vpn van site-to-site om verbinding te maken met Azure.
-   **VNet-peering**: Gebruik VNet-peering (virtual network) om connectiviteit tussen VNets in Azure Virtual Network tot stand te brengen. Zie de [zelfstudie over VNet-peering voor][VNet]meer informatie over VNet-peering.

## <a name="test-setup"></a>Testopstelling

De volgende figuur illustreert de testopstelling:

![1][1]

Het middelpunt van de testopstelling is de hub VNet in Azure Region 1. De hub VNet is op de volgende manieren verbonden met verschillende netwerken:

-   De hub VNet is verbonden met de spaak VNet met behulp van VNet peering. De spaak VNet heeft op afstand toegang tot beide gateways in de hub VNet.
-   De hub VNet is verbonden met de tak VNet door gebruik te maken van site-to-site VPN. De connectiviteit maakt gebruik van eBGP om routes uit te wisselen.
-   De hub VNet is verbonden met het on-premises Locatie 1-netwerk door ExpressRoute private peering als primaire pad te gebruiken. Het maakt gebruik van site-to-site VPN-connectiviteit als back-up pad. In de rest van dit artikel verwijzen we naar dit ExpressRoute circuit als ExpressRoute 1. ExpressRoute-circuits bieden standaard redundante connectiviteit voor hoge beschikbaarheid. Op ExpressRoute 1 is de subinterface van de secundaire klantrand (CE) router die wordt geconfronteerd met de secundaire Microsoft Enterprise Edge Router (MSEE) uitgeschakeld. Een rode lijn over de pijl met dubbele lijn in de voorgaande figuur vertegenwoordigt de subinterface van de uitgeschakelde CE-router.
-   De hub VNet is verbonden met het on-premises Locatie 2-netwerk via een andere ExpressRoute private peering. In de rest van dit artikel verwijzen we naar dit tweede ExpressRoute circuit als ExpressRoute 2.
-   ExpressRoute 1 verbindt ook zowel de hub VNet als het on-premises Locatie 1-netwerk met een extern VNet in Azure Region 2.

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

Meer informatie over [configuratiedetails][Configuration] voor de testtopologie.

Meer informatie over de analyse van het [controlevlak][Control-Analysis] van de testopstelling en de weergaven van verschillende VNets of VLAN's in de topologie.

Meer informatie over de analyse van het [gegevensvlak][Data-Analysis] van de testinstallatie en azure-netwerkbewakingsfunctieweergaven.

Zie de [veelgestelde vragen over ExpressRoute][ExR-FAQ] voor:
-   Ontdek hoeveel ExpressRoute-circuits u verbinden met een ExpressRoute-gateway.
-   Ontdek hoeveel ExpressRoute-gateways u verbinden met een ExpressRoute-circuit.
-   Meer informatie over andere schaallimieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagram van de testtopologie"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


