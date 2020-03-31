---
title: 'Interoperabiliteit in azure back-end connectiviteitsfuncties: analyse van gegevensvlak | Microsoft Documenten'
description: Dit artikel biedt de analyse van het gegevensvlak van de testopstelling die u gebruiken om de interoperabiliteit tussen ExpressRoute, een site-to-site VPN en virtueel netwerkpeeren in Azure te analyseren.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 815976c672272270e465610e17fef3aea79387f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526634"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Interoperabiliteit in Azure back-end connectiviteitsfuncties: analyse van gegevensvlak

In dit artikel wordt de analyse van het gegevensvlak van de [testopstelling][Setup]beschreven. U ook de configuratie van de [testinstallatie][Configuration] en de analyse van het [controlevlak][Control-Analysis] van de testopstelling bekijken.

Gegevensvlakanalyse onderzoekt het pad dat wordt afgelegd door pakketten die van het ene lokale netwerk (LAN of virtueel netwerk) naar het andere binnen een topologie worden doorgestoond. Het gegevenspad tussen twee lokale netwerken is niet noodzakelijkerwijs symmetrisch. Daarom analyseren we in dit artikel een doorstuurpad van een lokaal netwerk naar een ander netwerk dat gescheiden is van het omgekeerde pad.

## <a name="data-path-from-the-hub-vnet"></a>Gegevenspad van de hub VNet

### <a name="path-to-the-spoke-vnet"></a>Pad naar het spaakvnet

Virtual network (VNet) peering emuleert netwerkbrugfunctionaliteit tussen de twee VNets die worden peered. Traceroute output van een hub VNet naar een VM in de spaak VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

De volgende afbeelding toont de grafische verbindingsweergave van de hub VNet en het spaakVNet vanuit het perspectief van Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Pad naar de vertakking VNet

Traceroute-uitvoer van een hub VNet naar een VM in de branch VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In deze traceroute is de eerste hop de VPN-gateway in Azure VPN Gateway van de hub VNet. De tweede hop is de VPN-gateway van de tak VNet. Het IP-adres van de VPN-gateway van de tak VNet wordt niet geadverteerd in de hub VNet. De derde hop is de VM op de tak VNet.

De volgende figuur toont de grafische verbindingsweergave van de hub VNet en de tak VNet vanuit het perspectief van Network Watcher:

![2][2]

Voor dezelfde verbinding wordt in de volgende afbeelding de rasterweergave weergegeven in Network Watcher:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Pad naar on-premises locatie 1

Traceroute-uitvoer van een hub VNet naar een VM in on-premises locatie 1 wordt hier weergegeven:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In deze traceroute is de eerste hop het Azure ExpressRoute-gatewaytunneleindpunt voor een Microsoft Enterprise Edge Router (MSEE). De tweede en derde hop zijn de customer edge (CE) router en de on-premises Locatie 1 LAN IP's. Deze IP-adressen worden niet geadverteerd in de hub VNet. De vierde hop is de VM in de on-premises locatie 1.


### <a name="path-to-on-premises-location-2"></a>Pad naar on-premises locatie 2

Traceroute-uitvoer van een hub VNet naar een VM in on-premises locatie 2 wordt hier weergegeven:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel eindpunt van een MSEE. De tweede en derde hop zijn de CE router en de on-premises Locatie 2 LAN IP's. Deze IP-adressen worden niet geadverteerd in de hub VNet. De vierde hop is de VM op de on-premises locatie 2.

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute-uitvoer van een hub VNet naar een VM in het externe VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel eindpunt van een MSEE. De tweede hop is de externe VNet gateway IP. De tweede hop IP-bereik wordt niet geadverteerd in de hub VNet. De derde hop is de VM op het externe VNet.

## <a name="data-path-from-the-spoke-vnet"></a>Gegevenspad van het spaakvnet

De spaak VNet deelt de netwerkvisie van de hub VNet. Via VNet-peering maakt de spaak VNet gebruik van de externe gateway-connectiviteit van de hub VNet alsof het rechtstreeks is verbonden met het spaakvnet.

### <a name="path-to-the-hub-vnet"></a>Pad naar de hub VNet

Traceroute output van de spaak VNet naar een VM in de hub VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Pad naar de vertakking VNet

Traceroute output van de spaak VNet naar een VM in de tak VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In deze traceroute is de eerste hop de VPN-gateway van de hub VNet. De tweede hop is de VPN-gateway van de tak VNet. Het IP-adres van de VPN-gateway van de tak VNet wordt niet geadverteerd binnen de hub/spoke VNet. De derde hop is de VM op de tak VNet.

### <a name="path-to-on-premises-location-1"></a>Pad naar on-premises locatie 1

Traceroute-uitvoer van het spaakvnet naar een VM in on-premises locatie 1 wordt hier weergegeven:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel eindpunt van de hub VNet naar een MSEE. De tweede en derde hop zijn de CE-router en de on-premises locatie 1 LAN IP's. Deze IP-adressen worden niet geadverteerd in de hub / spaak VNet. De vierde hop is de VM in de on-premises locatie 1.

### <a name="path-to-on-premises-location-2"></a>Pad naar on-premises locatie 2

Traceroute-uitvoer van het spaakvnet naar een VM in on-premises locatie 2 wordt hier weergegeven:


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel eindpunt van de hub VNet naar een MSEE. De tweede en derde hop zijn de CE router en de on-premises Locatie 2 LAN IP's. Deze IP-adressen worden niet geadverteerd in de hub / spaak VNets. De vierde hop is de VM in de on-premises locatie 2.

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute output van de spaak VNet naar een VM in de externe VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel eindpunt van de hub VNet naar een MSEE. De tweede hop is de externe VNet gateway IP. De tweede hop IP-bereik wordt niet geadverteerd in de hub / spaak VNet. De derde hop is de VM op het externe VNet.

## <a name="data-path-from-the-branch-vnet"></a>Gegevenspad van de branch VNet

### <a name="path-to-the-hub-vnet"></a>Pad naar de hub VNet

Traceroute-uitvoer van de branch VNet naar een VM in de hub VNet wordt hier weergegeven:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

In deze traceroute is de eerste hop de VPN-gateway van de tak VNet. De tweede hop is de VPN-gateway van de hub VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is de VM op de hub VNet.

### <a name="path-to-the-spoke-vnet"></a>Pad naar het spaakvnet

Traceroute output van de tak VNet naar een VM in de spaak VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

In deze traceroute is de eerste hop de VPN-gateway van de tak VNet. De tweede hop is de VPN-gateway van de hub VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is de VM op de spaak VNet.

### <a name="path-to-on-premises-location-1"></a>Pad naar on-premises locatie 1

Traceroute-uitvoer van de tak VNet naar een VM in on-premises locatie 1 wordt hier weergegeven:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

In deze traceroute is de eerste hop de VPN-gateway van de tak VNet. De tweede hop is de VPN-gateway van de hub VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is het VPN-tunnelaansluitpunt op de primaire CE-router. De vierde hop is een intern IP-adres van on-premises locatie 1. Dit LAN IP-adres wordt niet geadverteerd buiten de CE-router. De vijfde hop is de bestemming VM in de on-premises locatie 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Pad naar on-premises locatie 2 en het externe VNet

Zoals we besproken in de controle vliegtuig analyse, de tak VNet heeft geen zicht op on-premises locatie 2 of op de externe VNet per het netwerk configuratie. De volgende pingresultaten bevestigen: 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>Gegevenspad van on-premises locatie 1

### <a name="path-to-the-hub-vnet"></a>Pad naar de hub VNet

Traceroute-uitvoer van on-premises locatie 1 naar een VM in de hub VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

In deze traceroute maken de eerste twee hops deel uit van het on-premises netwerk. De derde hop is de primaire MSEE-interface die de CE-router onder ogen ziet. De vierde hop is de ExpressRoute gateway van de hub VNet. Het IP-bereik van de ExpressRoute-gateway van de hub VNet wordt niet geadverteerd op het on-premises netwerk. De vijfde hop is de bestemming VM.

Network Watcher biedt alleen een Azure-centric view. Voor een on-premises perspectief gebruiken we Azure Network Performance Monitor. Network Performance Monitor biedt agents die u installeren op servers in netwerken buiten Azure voor gegevenspadanalyse.

De volgende afbeelding toont de topologieweergave van de on-premises Locatie 1 VM-connectiviteit met de VM op de hub VNet via ExpressRoute:

![4][4]

Zoals eerder besproken, maakt de testopstelling gebruik van een site-to-site VPN als back-upverbinding voor ExpressRoute tussen de on-premises locatie 1 en de hub VNet. Als u het back-upgegevenspad wilt testen, leiden we een ExpressRoute-koppelingsfout tussen de primaire CE-router van locatie 1 en de bijbehorende MSEE. Als u een ExpressRoute-koppelingsfout wilt veroorzaken, sluit u de CE-interface af die tegenover de MSEE staat:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

De volgende afbeelding toont de topologieweergave van de on-premises Locatie 1 VM-connectiviteit met de VM op de hub VNet via site-to-site VPN-connectiviteit wanneer de ExpressRoute-connectiviteit is uitgeschakeld:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Pad naar het spaakvnet

Traceroute-uitvoer van on-premises locatie 1 naar een VM in het spaakvnet wordt hier weergegeven:

Laten we de primaire verbinding met ExpressRoute terugbrengen om de analyse van het gegevenspad naar het gesproken VNet uit te voeren:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Breng de primaire ExpressRoute 1-connectiviteit weer voor de rest van de gegevenspadanalyse.

### <a name="path-to-the-branch-vnet"></a>Pad naar de vertakking VNet

Traceroute-uitvoer van on-premises locatie 1 naar een VM in de branche VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Pad naar on-premises locatie 2

Zoals we bespreken in de analyse van het [controlevlak,][Control-Analysis]heeft de on-premises locatie 1 geen zicht op on-premises locatie 2 per netwerkconfiguratie. De volgende pingresultaten bevestigen: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute-uitvoer van on-premises locatie 1 naar een VM in het externe VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Gegevenspad van on-premises locatie 2

### <a name="path-to-the-hub-vnet"></a>Pad naar de hub VNet

Traceroute-uitvoer van on-premises locatie 2 naar een VM in de hub VNet wordt hier weergegeven:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Pad naar het spaakvnet

Traceroute-uitvoer van on-premises locatie 2 naar een VM in het spaakvnet wordt hier weergegeven:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Pad naar de branch VNet, on-premises locatie 1 en het externe VNet

Zoals we bespreken in de analyse van het [besturingsvlak,][Control-Analysis]heeft de on-premises locatie 1 geen zicht op de branch VNet, naar on-premises locatie 1 of naar het externe VNet per netwerkconfiguratie. 

## <a name="data-path-from-the-remote-vnet"></a>Gegevenspad van het externe VNet

### <a name="path-to-the-hub-vnet"></a>Pad naar de hub VNet

Traceroute-uitvoer van het externe VNet naar een VM in de hub VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Pad naar het spaakvnet

Traceroute output van de externe VNet naar een VM in de spaak VNet wordt hier weergegeven:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Pad naar de tak VNet en on-premises locatie 2

Zoals we bespreken in de [controle vlak analyse][Control-Analysis], de externe VNet heeft geen zicht op de tak VNet of on-premises Locatie 2 per de netwerkconfiguratie. 

### <a name="path-to-on-premises-location-1"></a>Pad naar on-premises locatie 1

Traceroute-uitvoer van het externe VNet naar een VM in on-premises locatie 1 wordt hier weergegeven:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


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

Zie de [veelgestelde vragen over ExpressRoute][ExR-FAQ] voor:
-   Ontdek hoeveel ExpressRoute-circuits u verbinden met een ExpressRoute-gateway.
-   Ontdek hoeveel ExpressRoute-gateways u verbinden met een ExpressRoute-circuit.
-   Meer informatie over andere schaallimieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher weergave van connectiviteit van een hub VNet naar een spaak VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher weergave van connectiviteit van een hub VNet naar een branch VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Network Watcher-rasterweergave van connectiviteit van een hub VNet naar een branch VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Netwerkprestatiemonitorweergave van de connectiviteit van de VM locatie 1 naar de hub VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Network Performance Monitor weergave van connectiviteit van de Locatie 1 VM naar de hub VNet via een site-to-site VPN"

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


