---
title: 'Interoperabiliteit in Azure-back-endconnectiviteitsfuncties: configuratiedetails | Microsoft Documenten'
description: In dit artikel worden configuratiegegevens beschreven voor de testconfiguratie die u gebruiken om de interoperabiliteit tussen ExpressRoute, een site-to-site VPN en virtueel netwerkpeeren in Azure te analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335943"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabiliteit in Azure back-end connectiviteitsfuncties: configuratiedetails testen

In dit artikel worden de configuratiedetails van de [testopstelling][Setup]beschreven. Met de testopstelling u analyseren hoe Azure-netwerkservices samenwerken op het niveau van het besturingsvlak en het niveau van het gegevensvlak.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke VNet-connectiviteit met VNet-peering

De volgende afbeelding toont de azure virtual network peering details van een gesproken virtueel netwerk (VNet). Zie [VNet-peering][VNet-Config]beheren voor meer informatie over het instellen van peering tussen twee VNets. Als u wilt dat de spaak VNet de gateways gebruikt die zijn aangesloten op de hub VNet, selecteert u **Externe gateways gebruiken**.

[![1]][1]

De volgende figuur toont de VNet peering details van de hub VNet. Als u wilt dat de hub VNet de spaak VNet toestaat om de gateways van de hub te gebruiken, selecteert u **Gateway transit toestaan**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Branch VNet-connectiviteit met behulp van een site-to-site VPN

Stel site-to-site VPN-connectiviteit tussen de hub en branch VNets in met VPN-gateways in Azure VPN Gateway. Vpn-gateways en Azure ExpressRoute-gateways gebruiken standaard een ASN-waarde (private autonomous system number) van **65515.** U de ASN-waarde wijzigen in VPN Gateway. In de testopstelling wordt de ASN-waarde van de Branch VNet VPN-gateway gewijzigd in **65516** om eBGP-routering tussen de hub en branch VNets te ondersteunen.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>On-premises Locatie 1-connectiviteit met ExpressRoute en een site-to-site VPN

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1-configuratiegegevens

De volgende afbeelding toont de azure region 1 ExpressRoute-circuitconfiguratie naar on-premises CE-routers (Location 1 customer edge):

[![4]][4]

De volgende afbeelding toont de verbindingsconfiguratie tussen het ExpressRoute 1-circuit en de hub VNet:

[![5]][5]

In de volgende lijst wordt de primaire CE-routerconfiguratie voor ExpressRoute-connectiviteit voor privépeering weergegeven. (Cisco ASR1000-routers worden gebruikt als CE-routers in de testopstelling.) Wanneer vpn- en ExpressRoute-circuits van site-to-site parallel zijn geconfigureerd om een on-premises netwerk met Azure te verbinden, geeft Azure standaard prioriteit aan het ExpressRoute-circuit. Om asymmetrische routering te voorkomen, moet het on-premises netwerk ook prioriteit geven aan ExpressRoute-connectiviteit boven site-to-site VPN-connectiviteit. Met de volgende configuratie wordt prioritering bepaald met behulp van het **kenmerk Lokaal-voorkeurskenmerk** BGP:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Site-to-site VPN-configuratiegegevens

In de volgende lijst wordt de primaire CE-routerconfiguratie voor site-to-site VPN-connectiviteit weergegeven:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>On-premises locatie 2-connectiviteit met ExpressRoute

Een tweede ExpressRoute-circuit, in de nabijheid van on-premises locatie 2, verbindt on-premises locatie 2 met de hub VNet. De volgende afbeelding toont de tweede ExpressRoute-configuratie:

[![6]][6]

De volgende afbeelding toont de verbindingsconfiguratie tussen het tweede ExpressRoute-circuit en de hub VNet:

[![7]][7]

ExpressRoute 1 verbindt zowel de hub VNet als on-premises locatie 1 met een extern VNet in een andere Azure-regio:

[![8]][8]

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

Meer informatie over de analyse van het [controlevlak][Control-Analysis] van de testopstelling en de weergaven van verschillende VNets of VLAN's in de topologie.

Meer informatie over [gegevensvlakanalyse][Data-Analysis] van de testinstallatie en azure-netwerkbewakingsfunctieweergaven.

Zie de [veelgestelde vragen over ExpressRoute][ExR-FAQ] voor:
-   Ontdek hoeveel ExpressRoute-circuits u verbinden met een ExpressRoute-gateway.
-   Ontdek hoeveel ExpressRoute-gateways u verbinden met een ExpressRoute-circuit.
-   Meer informatie over andere schaallimieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Spoke VNet's VNet peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Hub VNet's VNet-peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway configuratie van een branch VNet"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 configuratie"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Verbindingsconfiguratie van ExpressRoute 1 naar een hub VNet ExR-gateway"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2-configuratie"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Verbindingsconfiguratie van ExpressRoute 2 naar een hub VNet ExR-gateway"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Verbindingsconfiguratie van ExpressRoute 2 naar een externe VNet ExR-gateway"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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


