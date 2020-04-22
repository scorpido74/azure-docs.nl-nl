---
title: S2S VPN gebruiken als back-up voor Azure ExpressRoute Private Peering | Microsoft Documenten
description: Deze pagina bevat architecturale aanbevelingen voor het maken van back-ups van Azure ExpressRoute private peering met S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687846"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>S2S VPN gebruiken als back-up voor Privé-peering via ExpressRoute

In het artikel getiteld [Designing for disaster recovery with ExpressRoute private peering][DR-PP], bespraken we de noodzaak van back-up connectiviteit oplossing voor een ExpressRoute private peering connectiviteit en hoe geo-redundantexpressRoute circuits te gebruiken voor het doel. In dit artikel bekijken we hoe we site-to-site (S2S) VPN kunnen gebruiken en onderhouden als een back voor Privé-peering van ExpressRoute. 

In tegenstelling tot georedundante ExpressRoute-circuits u expressroute-VPN-noodherstelcombinatie alleen in actief-passieve modus gebruiken. Een grote uitdaging van het gebruik van een back-up netwerkconnectiviteit in de passieve modus is dat de passieve verbinding vaak zou mislukken naast de primaire verbinding. De gemeenschappelijke reden voor de mislukkingen van de passieve verbinding is gebrek aan actief onderhoud. Daarom, in dit artikel laten we ons richten op hoe te controleren en actief onderhouden S2S VPN-connectiviteit die is een back-up van een ExpressRoute prive-peering.

>[!NOTE] 
>Wanneer een bepaalde route wordt geadverteerd via zowel ExpressRoute als VPN, geeft Azure de voorkeur aan routering boven ExpressRoute.  
>

Laten we in dit artikel bekijken hoe u de connectiviteit verifiëren, zowel vanuit Azure-perspectief als vanuit het perspectief van de netwerkrand aan de klantzijde. De mogelijkheid om te valideren vanaf beide einden zal helpen, ongeacht of u de netwerkapparaten aan de klantzijde beheert die met de Microsoft-netwerkentiteiten samenwerken. 

## <a name="example-topology"></a>Voorbeeldtopologie

In onze installatie hebben we een on-premises netwerk verbonden met een Azure hub VNet via zowel een ExpressRoute-circuit als een S2S VPN-verbinding. De Azure-hub VNet is op zijn beurt ingekeken naar een spaakVNet, zoals in het onderstaande diagram wordt weergegeven:

![1][1]

In de setup wordt het ExpressRoute-circuit beëindigd op een paar "Customer Edge" (CE) routers op de on-premises. Het on-premises LAN is verbonden met de CE-routers via een paar firewalls die in de leader-followermodus werken. De S2S VPN wordt direct beëindigd op de firewalls.

In de volgende tabel worden de belangrijkste IP-voorvoegsels van de topologie weergegeven:

| **Entiteit** | **Voorvoegsel** |
| --- | --- |
| On-premises LAN | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure sprak VNet | 10.17.11.128/26 |
| On-premises testserver | 10.1.11.10 |
| Spaak VNet-test VM | 10.17.11.132 |
| ExpressRoute primaire verbinding p2p subnet | 192.168.11.16/30 |
| ExpressRoute secundaire verbinding p2p subnet | 192.168.11.20/30 |
| VPN-gateway primaire BGP-peer IP | 10.17.11.76 |
| VPN-gateway secundairE BGP-peer IP | 10.17.11.77 |
| On-premises firewall VPN BGP peer IP | 192.168.11.88 |
| Primaire CE-router i/f naar firewall-IP | 192.168.11.0/31 |
| Firewall i/f naar primaire CE-router IP | 192.168.11.1/31 |
| Secundaire CE router i/f naar firewall IP | 192.168.11.2/31 |
| Firewall i/f naar secundair CE-router-IP | 192.168.11.3/31 |


In de volgende tabel worden de ASN's van de topologie weergegeven:

| **Autonoom systeem** | **Asn** |
| --- | --- |
| On-premises | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtueel netwerk GW (ExR) | 65515 |
| Virtueel netwerk GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Hoge beschikbaarheid zonder asymmetrie

### <a name="configuring-for-high-availability"></a>Configureren voor hoge beschikbaarheid

[ExpressRoute en site-to-site coëxistentieverbindingen configureren,][Conf-CoExist] bespreekt hoe u het naast elkaar bestaande ExpressRoute-circuit en S2S VPN-verbindingen configureert. Zoals we besproken in [Designing voor hoge beschikbaarheid met ExpressRoute][HA], om ExpressRoute hoge beschikbaarheid te verbeteren, onderhoudt onze setup de redundantie van het netwerk (voorkomt single point of failure) tot aan de eindpunten. Ook de primaire en secundaire verbindingen van de ExpressRoute-circuits zijn geconfigureerd om actief te werken door de on-premises voorvoegsels op dezelfde manier via beide verbindingen te adverteren. 

De on-premises routeadvertentie van de primaire CE-router via de primaire verbinding van het ExpressRoute-circuit wordt hieronder weergegeven (Opdrachten van Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

De on-premises routeadvertentie van de secundaire CE-router via de secundaire verbinding van het ExpressRoute-circuit wordt hieronder weergegeven (Junos-opdrachten):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Om de hoge beschikbaarheid van de back-upverbinding te verbeteren, is de S2S VPN ook geconfigureerd in de actief-actieve modus. De Azure VPN-gatewayconfiguratie wordt hieronder weergegeven. Opmerking als onderdeel van de VPN-configuratie VPN de BGP peer IP-adressen van de gateway--10.17.11.76 en 10.17.11.77 - worden ook vermeld.

![2][2]

De on-premises route wordt geadverteerd door de firewalls aan de primaire en secundaire BGP-peers van de VPN-gateway. De route advertenties worden hieronder weergegeven (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Het configureren van de S2S VPN in actieve actieve modus biedt niet alleen een hoge beschikbaarheid voor uw disaster recovery back-upnetwerkconnectiviteit, maar biedt ook een hogere doorvoer voor de back-upconnectiviteit. Met andere woorden, het configureren van S2S VPN in actief-actieve modus wordt aanbevolen omdat het meerdere onderliggende tunnels maakt.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configureren voor symmetrische verkeersstroom

We merkten op dat wanneer een bepaalde on-premises route wordt geadverteerd via zowel ExpressRoute als S2S VPN, Azure de voorkeur zou geven aan het ExpressRoute-pad. Om Azure te dwingen de voorkeur te geven aan S2S VPN-pad boven de naast elkaar bestaande ExpressRoute, moet u meer specifieke routes adverteren (langer voorvoegsel met groter subnetmasker) via de VPN-verbinding. Ons doel hier is om de VPN-verbindingen alleen als back te gebruiken. Het standaardpadselectiegedrag van Azure is dus in lijn met ons doel. 

Het is onze verantwoordelijkheid om ervoor te zorgen dat het verkeer dat vanuit on-premises naar Azure is bestemd, ook de voorkeur geeft aan ExpressRoute-pad boven S2S VPN. De standaard lokale voorkeur van de CE-routers en firewalls in onze on-premises installatie is 100. Dus, door het configureren van de lokale voorkeur van de routes ontvangen via de ExpressRoute prive-peerings groter dan 100 (zeg 150), kunnen we het verkeer bestemd voor Azure voorkeur ExpressRoute circuit in de stabiele staat.

De BGP-configuratie van de primaire CE-router die de primaire verbinding van het ExpressRoute-circuit beëindigt, wordt hieronder weergegeven. Let op de waarde van de lokale voorkeur van de routes geadverteerd over de iBGP sessie is geconfigureerd op 150. Ook moeten we ervoor zorgen dat de lokale voorkeur van de secundaire CE-router die de secundaire verbinding van het ExpressRoute-circuit beëindigt, ook is geconfigureerd als 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

De routeringstabel van de on-premises firewalls bevestigt (zie hieronder) dat voor het on-premises verkeer dat is bestemd voor Azure het voorkeurspad boven ExpressRoute in de stabiele toestand is.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

In de bovenstaande routetabel, voor de hub en spaak VNet routes--10.17.11.0/25 en 10.17.11.128/26--zien we ExpressRoute circuit is de voorkeur boven VPN-verbindingen. De 192.168.11.0 en 192.168.11.2 zijn IP's op firewall-interface richting CE-routers.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validatie van route-uitwisseling via S2S VPN

Eerder in dit artikel hebben we on-premises routeadvertenties van de firewalls geverifieerd naar de primaire en secundaire BGP-peers van de VPN-gateway. Bovendien, laten we bevestigen Azure routes ontvangen door de firewalls van de primaire en secundaire BGP peers van de VPN-gateway.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Laten we ook controleren of on-premises netwerkroutevoorvoegsels die zijn ontvangen door de Azure VPN-gateway. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Zoals hierboven te zien is, heeft de VPN-gateway routes die zowel door de primaire als secundaire BGP-peers van de VPN-gateway zijn ontvangen. Het heeft ook zicht op de routes ontvangen via primaire en secundaire ExpressRoute verbindingen (die met AS-pad prepended met 12076). Om de routes te bevestigen die via VPN-verbindingen worden ontvangen, moeten we de on-premises BGP-peer IP van de verbindingen kennen. In onze setup in kwestie, het is 192.168.11.88 en we zien de routes ontvangen van het.

Laten we vervolgens de routes verifiëren die worden geadverteerd door de Azure VPN-gateway naar de on-premises firewall BGP-peer (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


Als u route-uitwisselingen niet ziet, wordt de verbinding mislukt. Zie [Probleemoplossing: een AZURE-VPN-verbinding van site tot site kan geen verbinding maken en werkt niet meer][VPN Troubleshoot] voor hulp bij het oplossen van problemen met de VPN-verbinding.

## <a name="testing-failover"></a>Failover testen

Nu we succesvolle route-uitwisselingen via de VPN-verbinding (besturingsvlak) hebben bevestigd, zijn we ingesteld om verkeer (dataplane) over te schakelen van de ExpressRoute-verbinding naar de VPN-connectiviteit. 

>[!NOTE] 
>In productieomgevingen moet failover-tests worden uitgevoerd tijdens het geplande werkvenster voor netwerkonderhoud, omdat deze serviceverstorend kan zijn.
>

Voordat u de verkeersschakelaar doet, moeten we het huidige pad in onze installatie traceren van de on-premises testserver naar de testVM in het spaakvnet.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

De primaire en secundaire ExpressRoute point-to-point verbindingssubnetten van onze setup zijn respectievelijk 192.168.11.16/30 en 192.168.11.20/30. In de bovenstaande traceroute zien we in stap 3 dat we 192.168.11.18 raken, de interface-IP van de primaire MSEE. De aanwezigheid van de MSEE-interface bevestigt dat zoals verwacht ons huidige pad over de ExpressRoute ligt.

Zoals gemeld in de [Reset ExpressRoute circuit peerings][RST], laten we gebruik maken van de volgende powershell commando's om zowel de primaire en secundaire peering van de ExpressRoute circuit uit te schakelen.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

De failoverschakeltijd is afhankelijk van de BGP-convergentietijd. In onze setup duurt de failoverschakelaar enkele seconden (minder dan 10). Na de switch geeft de herhaling van de traceroute het volgende pad weer:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

Het resultaat van de traceroute bevestigt dat de back-upverbinding via S2S VPN actief is en servicecontinuïteit kan bieden als zowel de primaire als secundaire ExpressRoute-verbindingen uitvallen. Om de failovertests te voltooien, schakelen we de ExpressRoute-verbindingen terug in en normaliseren we de verkeersstroom, met behulp van de volgende set opdrachten.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Om te bevestigen dat het verkeer wordt teruggezet naar ExpressRoute, herhaalt u de traceroute en zorgt u ervoor dat deze via de ExpressRoute privé-peering gaat.

## <a name="next-steps"></a>Volgende stappen

ExpressRoute is ontworpen voor hoge beschikbaarheid zonder single point of failure binnen het Microsoft-netwerk. Nog steeds is een ExpressRoute circuit beperkt tot één geografische regio en tot een dienstverlener. S2S VPN kan een goede oplossing zijn voor passieve back-ups voor noodherstel op een ExpressRoute-circuit. Voor een betrouwbare passieve back-upverbindingsoplossing zijn regelmatig onderhoud van de passieve configuratie en periodieke validatie van de verbinding belangrijk. Het is essentieel om de VPN-configuratie niet muf te laten worden en om periodiek (bijvoorbeeld elk kwartaal) de validatie- en failoverteststappen te herhalen die in dit artikel worden beschreven tijdens het onderhoudsvenster.

Zie [Waarschuwingen instellen op VPN Gateway metrics][VPN-alerts]om monitoring en waarschuwingen op basis van VPN-gatewaymetrics in te schakelen.

Als u de Convergentie van BGP wilt versnellen na een expressroute-fout, [configureert u BFD over ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologie in overweging"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW-configuratie"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



