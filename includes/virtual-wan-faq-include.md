---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cb2302637efb16fc31bd420bf8c4ead19d7f598d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384958"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Moet de gebruiker hub hebben en met SD-WAN/VPN-apparaten gesproken om Azure Virtual WAN te gebruiken?

Virtual WAN biedt veel functionaliteiten die zijn ingebouwd in één ruit, zoals Site/Site-to-site VPN-connectiviteit, User/P2S-connectiviteit, ExpressRoute-connectiviteit, Virtual Network-connectiviteit, VPN ExpressRoute Interconnectivity, VNET naar VNET-transitieve connectiviteit, Gecentraliseerde routing, Azure firewall- en firewallmanagerbeveiliging, Monitoring, ExpressRoute-versleuteling en vele andere mogelijkheden. U hoeft niet al deze use cases te hebben om Virtual WAN te gebruiken. U gewoon aan de slag met slechts één use case. De Virtual WAN-architectuur is een hub en spoke-architectuur met schaal en prestaties ingebouwd waar branches (VPN/SD-WAN-apparaten), gebruikers (Azure VPN Clients, openVPN of IKEv2 Clients), ExpressRoute-circuits, Virtuele netwerken als spaken dienen voor Virtual Hub(s). Alle hubs zijn volledig mesh aangesloten in een Standard Virtual WAN, waardoor het voor de gebruiker gemakkelijk is om de Microsoft-backbone te gebruiken voor elke (spaak)connectiviteit. Voor hub en spoke met SD-WAN/VPN-apparaten kunnen gebruikers deze handmatig instellen in de Azure Virtual WAN-portal of de Virtual WAN Partner CPE (SD-WAN/VPN) gebruiken om connectiviteit met Azure in te stellen. Virtuele WAN-partners bieden automatisering voor connectiviteit, wat de mogelijkheid is om de apparaatgegevens naar Azure te exporteren, de Azure-configuratie te downloaden en connectiviteit met de Azure Virtual WAN-hub tot stand te brengen. Voor Point-to-site/User VPN-connectiviteit ondersteunen we [Azure VPN-client,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN of IKEv2-client. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Welke client ondersteunt de Azure Virtual WAN User VPN (Point-to-site) ?

Virtual WAN ondersteunt [Azure VPN-client,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN-client of een IKEv2-client. Azure AD-verificatie wordt ondersteund met Azure VPN Client.Een minimum van Windows 10 client OS versie 17763.0 of hoger is vereist.  OpenVPN-client(s) kunnen certificaatgebaseerde verificatie ondersteunen. Zodra cert-based auth is geselecteerd op de gateway, ziet u het .ovpn-bestand om te downloaden naar uw apparaat. Zowel certificaat als RADIUS auth wordt ondersteund met IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Voor User VPN (Point-to-site)- Waarom is de P2S-clientpool opgesplitst in twee routes?

Elke gateway heeft twee exemplaren, de splitsing gebeurt, zodat elke gateway-instantie onafhankelijk client-IP's kan toewijzen voor verbonden clients en verkeer van het virtuele netwerk wordt teruggeleid naar de juiste gateway-instantie om inter-gateway instance hop te voorkomen.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hoe voeg ik DNS-servers toe voor P2S-clients?

Er zijn twee opties om DNS-servers toe te voegen voor de P2S-clients.

1. Open een ondersteuningsticket met Microsoft en laat ze uw DNS-servers toevoegen aan de hub
2. Of als u de Azure VPN-client voor Windows 10 gebruikt, u het gedownloade profiel XML-bestand wijzigen en de ** \<dnsservers toevoegen>\<dnsserver> \</dnsserver>\</dnsservers>-tags** voordat u het importeert.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Voor User VPN (Point-to-site)- hoeveel clients worden ondersteund?

Elke User VPN P2S-gateway heeft twee exemplaren en elke instantie ondersteunt maximaal bepaalde gebruikers wanneer de schaaleenheid verandert. Schaaleenheid 1-3 ondersteunt 500 verbindingen, Schaaleenheid 4-6 ondersteunt 1000-aansluitingen, Schaaleenheid 7-10 ondersteunt 5000-aansluitingen en Schaaleenheid 11+ ondersteunt tot 10.000 aansluitingen. Als voorbeeld, laten we zeggen dat de gebruiker kiest 1 schaal eenheid. Elke schaaleenheid impliceert een actief actieve gateway die wordt geïmplementeerd en elk van de instanties (in dit geval 2) zou maximaal 500 verbindingen ondersteunen. Aangezien u 500 verbindingen * 2 per gateway krijgen, betekent dit niet dat u van plan bent voor 1000 in plaats van de 500 voor deze schaaleenheid, omdat instanties mogelijk moeten worden onderhouden waarbij de connectiviteit voor de extra 500 kan worden onderbroken als u het aanbevolen aantal verbindingen overschrijdt.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Wat is het verschil tussen een Vpn-gateway (Virtual Network Gateway) van Azure en een Azure Virtual WAN VPN-gateway?

Virtual WAN biedt grootschalige site-naar-site-connectiviteit en is ontworpen met het oog op doorvoer, schaalbaarheid en gebruiksgemak. Wanneer u een site verbindt met een Virtual WAN VPN-gateway, is deze anders dan een gewone virtuele netwerkgateway die gebruikmaakt van een gatewaytype 'VPN'. Wanneer u een ExpressRoute-circuit aansluit op een Virtuele WAN-hub, gebruikt deze ook een andere bron voor de ExpressRoute-gateway dan de reguliere virtuele netwerkgateway die gatewaytype 'ExpressRoute' gebruikt. Virtual WAN ondersteunt tot 20 Gbps geaggregeerde doorvoer voor zowel VPN als ExpressRoute. Virtual WAN heeft ook automatisering voor connectiviteit met een ecosysteem van CPE-brancheapparaatpartners. CPE-vertakkingen hebben ingebouwde automatisering die automatisch in- en aansluit op Azure Virtual WAN. Deze apparaten zijn beschikbaar binnen een groeiend ecosysteem van SD-WAN- en VPN-partners. Zie de [lijst met partners van voorkeur](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hoe verschilt Virtual WAN van een Azure virtual network gateway?

Een virtual network gateway VPN is beperkt tot 30 tunnels. U moet Virtual WAN gebruiken voor grootschalige VPN-verbindingen. U tot 1.000 branchverbindingen per regio (virtuele hub) aansluiten met een totaal van 20 Gbps per hub. Een verbinding is een actief-actief-tunnel van het on-premises VPN-apparaat naar de virtuele hub. U één hub per regio hebben, wat betekent dat u meer dan 1.000 branches aansluiten op hubs.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Wat is een Virtual WAN Gateway Scale Unit
Een schaaleenheid is een eenheid die is gedefinieerd om een geaggregeerde doorvoer van een gateway in virtuele hub te kiezen. 1 schaaleenheid vpn = 500 Mbps . 1 schaaleenheid van ExpressRoute = 2 Gbps. Voorbeeld : 10 schaal eenheid van VPN zou impliceren 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Welke apparaatproviders (Virtual WAN-partners) worden ondersteund?

Veel partners bieden momenteel ondersteuning voor de volledig geautomatiseerde Virtual WAN-ervaring. Zie [Virtual WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md) voor meer informatie. 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Wat zijn de stappen voor automatisering voor Virtual WAN-partners?

Zie [Automatisering voor Virtual WAN-partners](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) voor meer informatie.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Moet ik een apparaat van een voorkeurspartner gebruiken?

Nee. U kunt ieder VPN-apparaat gebruiken dat voldoet aan de Azure-vereisten voor IKEv2/IKEv1 IPSec-ondersteuning.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hoe automatiseren Virtual WAN-partners connectiviteit met Azure Virtual WAN?

Softwarematige oplossingen voor netwerkconnectiviteit beheren hun vertakkingsapparaten doorgaans met behulp van een controller of een knooppunt voor apparaatinrichting. De controller kan Azure API’s gebruiken om de connectiviteit met Azure Virtual WAN te automatiseren. De automatisering omvat het uploaden van branch-informatie, het downloaden van de Azure-configuratie, het instellen van IPSec-tunnels in Azure Virtual-hubs en het automatisch instellen van connectiviteit vormen het branch-apparaat naar Azure Virtual WAN. Wanneer u honderden branches hebt, is het eenvoudig om verbinding te maken met Virtual WAN CPE Partners, omdat de onboarding-ervaring de noodzaak wegneemt om grootschalige IPsec-connectiviteit in te stellen, te configureren en te beheren. Zie [Virtual WAN-partnerautomatisering](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)voor meer informatie.


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hoe ondersteunt Virtual WAN SD-WAN-apparaten?

Virtuele WAN-partners automatiseren IPsec-connectiviteit met Azure VPN-eindpunten. Als de Virtual WAN-partner een SD-WAN-provider is, wordt geïmpliceerd dat de SD-WAN-controller automatisering en IPsec-connectiviteit met Azure VPN-eindpunten beheert. Als het SD-WAN-apparaat een eigen eindpunt vereist in plaats van Azure VPN voor een eigen SD-WAN-functionaliteit, u het SD-WAN-eindpunt implementeren in een Azure VNet en naast Azure Virtual WAN bestaan.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Veranderen er bij het gebruik van Virtual WAN bestaande functies voor connectiviteit?

Er zijn geen wijzigingen in bestaande connectiviteitsfuncties van Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Zijn er nieuwe Resource Manager-resources beschikbaar voor Virtual WAN?
  
Ja, met Virtual WAN worden nieuwe Resource Manager-resources geïntroduceert. Zie voor meer informatie het [Overzicht](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hoeveel VPN-apparaten kunnen verbinding maken met één hub?

Tot 1.000 verbindingen worden ondersteund per virtuele hub. Elke verbinding bestaat uit vier koppelingen en elke verbindingsverbinding ondersteunt twee tunnels die zich in een actief actieve configuratie bevinden. De tunnels eindigen in een Azure virtual hub vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan het on-premises VPN-apparaat verbinding maken met meerdere hubs?

Ja. De verkeersstroom, wanneer het begint, is van het on-premises apparaat aan de dichtste microsoft netwerkrand, en dan aan de virtuele hub.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan ik mijn favoriete virtuele netwerkapparaat implementeren en gebruiken (in een NVA-VNet) met Azure Virtual WAN?

Ja, u kunt uw favoriete virtuele netwerkapparaat (NVA)-VNet verbinden met de Azure Virtual WAN. Verbind eerst het NVA-VNet naar de hub met een Hub Virtual Network-verbinding. Maak vervolgens een virtuele hubroute met een volgende hop die naar het virtuele toestel wijst. U meerdere routes toepassen op de virtuele hub routetabel. Eventuele spaken die verbonden zijn met het NVA-VNet moeten aanvullend worden verbonden met de virtuele hub om ervoor te zorgen dat de VNet-spaakroutes naar on-premises systemen worden doorgevoerd.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan ik een network virtual appliance maken in de virtuele hub?

Een Network Virtual Appliance (NVA) kan niet worden geïmplementeerd in een virtuele hub. U het echter maken in een spaak VNet dat is aangesloten op de virtuele hub en een route in de hub in staat stellen om verkeer naar bestemming VNet te leiden via het NVA IP-adres (van de NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan een spaak VNet hebben een virtueel netwerk gateway?

Nee. De spaak VNet kan geen virtuele netwerkgateway hebben als deze is aangesloten op de virtuele hub.

### <a name="is-there-support-for-bgp"></a>Is er ondersteuning voor BGP?

Ja, BGP wordt ondersteund. Wanneer u een VPN-site maakt, u de BGP-parameters erin opgeven. Dit betekent dat alle verbindingen die in Azure voor die site zijn gemaakt, zijn ingeschakeld voor BGP. Bovendien, als u een VNet had met een NVA, en als deze NVA VNet was gekoppeld aan een Virtual WAN-hub, om ervoor te zorgen dat routes van een NVA VNet op de juiste manier worden geadverteerd, moeten spaken die zijn gekoppeld aan NVA VNet BGP uitschakelen. Sluit deze spaakvNets aan op de virtuele hub VNet om ervoor te zorgen dat spaakvnet-routes worden gepropageerd naar on-premises systemen.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan ik verkeer beheren met behulp van de UDR in de virtuele hub?

Ja, u verkeer naar een VNet leiden met behulp van een virtuele hubroutetabel. Hiermee u routes instellen voor bestemmingsVNets in Azure via een specifiek IP-adres (meestal van de NVA NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Is er informatie over licentieverlening of prijzen beschikbaar voor Virtual WAN?

Ja. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hoe bereken ik de prijs van een hub?

* U zou betalen voor de diensten in de hub. Laten we bijvoorbeeld zeggen dat u 10 branches of on-premises apparaten hebt die verbinding moeten maken met Azure Virtual WAN, wat zou betekenen dat u verbinding maakt met VPN-eindpunten in de hub. Laten we zeggen dat dit VPN van 1 schaal eenheid = 500 Mbps, dit is in rekening gebracht op $ 0.361/hr. Elke verbinding wordt in rekening gebracht voor $ 0,05 per uur. Voor 10 verbindingen, zou de totale kosten van de dienst / hr is $ 0,361 + $.5 /hr. Gegevenskosten voor verkeer dat Azure verlaat, zijn van toepassing.

* Er is extra hub-kosten. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Als u ExpressRoute gateway als gevolg van ExpressRoute circuits verbinding met een virtuele hub, dan zou je betalen voor de schaal eenheid prijs. Elke schaaleenheid in ER is 2 Gbps en elke verbindingseenheid wordt in rekening gebracht tegen hetzelfde tarief als de VPN Connection-eenheid.

* Als u Spoke VNET's had aangesloten op de hub, zijn peering kosten bij de Spoke VNETs nog steeds van toepassing. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hoe worden nieuwe partners die nog niet zijn vermeld in de startlijst van partners, opgenomen?

Alle virtuele WAN API's zijn open API. U de documentatie doornemen om de technische haalbaarheid te beoordelen. Als je een vraag hebt, azurevirtualwan@microsoft.comstuur dan een e-mail naar . Een ideale partner is iemand die een apparaat heeft dat kan worden ingericht voor een IKEv1 of IKEv2 IPSec-verbinding.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Wat gebeurt er als een apparaat dat ik gebruik niet in de lijst met virtuele WAN-partners staat? Kan ik het nog steeds gebruiken om verbinding te maken met Azure Virtual WAN VPN?

Ja, zolang het apparaat IPsec IKEv1 of IKEv2 ondersteunt. Virtuele WAN-partners automatiseren de connectiviteit van het apparaat naar Azure VPN-eindpunten. Dit impliceert het automatiseren van stappen zoals 'branch information upload', 'IPsec and configuration' en 'connectivity'. Aangezien uw apparaat niet afkomstig is van een virtueel WAN-partnerecosysteem, moet u het zware werk doen om handmatig de Azure-configuratie te nemen en uw apparaat bij te werken om IPsec-connectiviteit in te stellen.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Is het mogelijk om een Azure Virtual WAN te maken met een Resource Manager-sjabloon?

Een eenvoudige configuratie van een Virtual WAN met één hub en één vpnsite kan worden gemaakt met behulp van een [quickstartsjabloon.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) Virtual WAN is in de eerste plaats een REST- of portal-gestuurde service.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Wordt wereldwijde VNET-peering met Azure Virtual WAN ondersteund? 

U een VNet aansluiten in een andere regio dan uw virtuele WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kunnen spaakvnets die zijn aangesloten op een virtuele hub met elkaar communiceren (V2V Transit)?

Ja. Standard Virtual WAN ondersteunt Vnet naar Vnet transitive connectiviteit via de Virtual WAN hub waarmee de Vnets zijn aangesloten. In de virtuele WAN-terminologie verwijzen we naar deze paden als "lokale Virtual WAN VNet-doorvoer" voor VNets die zijn aangesloten op een Virtuele Wan-hub binnen één regio en "global Virtual WAN VNet-transit" voor VNets die zijn verbonden via meerdere Virtuele WAN-hubs in twee of meer regio's. VNet-transit ondersteunt tot 3 Gbps doorvoer tijdens openbare preview. De doorvoer zal worden uitgebreid wanneer de wereldwijde doorvoer ga gaat.

OPMERKING: Momenteel vereist V2V transit preview een VPN GW worden geïmplementeerd in een Virtual Hub om de routing-elementen te activeren die moeten worden gestart. Deze VPN GW wordt niet gebruikt voor het V2V-transitpad. Dit is een bekende beperking en zal worden verwijderd op het moment van V2V GA. U de VPN-gateway in de hub(s) verwijderen nadat deze volledig is gestart, omdat deze niet nodig is voor v2V-transitfunctionaliteit. 

Voor sommige scenario's kunnen spaakvnets ook rechtstreeks met elkaar worden gekeken met behulp van [Virtual Network Peering](../articles/virtual-network/virtual-network-peering-overview.md) naast lokale of wereldwijde Virtual WAN VNet-doorvoer. In dit geval heeft Vnet Peering voorrang op de transitieve verbinding via de Virtual WAN-hub. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Wat is een branch-verbinding met Azure Virtual WAN?

Een verbinding van een branch-apparaat met Azure Virtual WAN ondersteunt maximaal vier koppelingen. Een koppeling is de fysieke connectiviteitskoppeling op de vestigingsplaats (bijvoorbeeld: ATT, Verizon etc.). Elke verbindingsverbinding bestaat uit twee actieve/actieve IPsec-tunnels.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Is een vertakking-naar-vertakking-verbinding toegestaan in Virtual WAN?

Ja, een vertakking-naar-vertakking-verbinding is in Virtual WAN beschikbaar voor VPN en VPN naar ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Loopt het branch-to-branch-verkeer door het Azure Virtual WAN?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vereist Virtual WAN ExpressRoute van elke site?

Nee, Virtual WAN heeft niet vanaf elke locatie ExpressRoute nodig. Het maakt gebruik van standaard IPsec site-to-site connectiviteit via internetlinks van het apparaat naar een Azure Virtual WAN-hub. Uw sites kunnen worden verbonden met een providernetwerk met behulp van een ExpressRoute-circuit. Voor sites die zijn verbonden met ExpressRoute in een virtuele hub, kunnen sites een tak hebben om de verkeersstroom tussen VPN en ExpressRoute te vertakken.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Is er een doorvoerlimiet in het netwerk bij gebruik van Azure Virtual WAN?

Het aantal branches is beperkt tot 1000 verbindingen per hub/regio en een totaal van 20 Gbps in de hub. U 1 hub per regio hebben.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hoeveel VPN-verbindingen ondersteunt een Virtual WAN-hub?

Een Azure Virtual WAN-hub kan maximaal 1.000 S2S-verbindingen, 10.000 P2S-verbindingen en 4 ExpressRoute-verbindingen tegelijk ondersteunen.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wat is de totale VPN-doorvoer van een VPN-tunnel en een verbinding?

De totale VPN-doorvoer van een hub is maximaal 20 Gbps op basis van de gekozen schaaleenheid. Doorvoer wordt gedeeld door alle bestaande verbindingen. Elke tunnel in een verbinding kan tot 1 Gbps ondersteunen.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Ik zie niet de 20 Gbps instelling voor de virtuele hub in de portal. Hoe configureer ik dat?

Navigeer naar de VPN-gateway in een hub op de portal en klik op de schaaleenheid om deze te wijzigen in de juiste instelling.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Staat Virtual WAN toe dat het on-premises apparaat meerdere ISP's parallel gebruikt, of is het altijd een enkele VPN-tunnel?

Een verbinding die in een virtuele WAN VPN komt, is altijd een actief actieve tunnel (voor tolerantie binnen dezelfde hub/regio) via een koppeling die beschikbaar is bij de branch. Deze koppeling kan een ISP-koppeling zijn in het on-premises filiaal. Virtual WAN 'VPNSite' biedt de mogelijkheid om linkinformatie toe te voegen aan de site. Als u meerdere ISP's in de branch hebt en elk van de ISP's een koppeling heeft, kan die informatie worden ingesteld in de VPN-sitegegevens in Azure. Het beheren van failover over ISP's in de branch is echter volledig een branch-centric routing-bewerking.

### <a name="what-is-global-transit-architecture"></a>Wat is wereldwijde transitarchitectuur?

Zie Global transit network architecture en Virtual WAN voor informatie over de wereldwijde architectuur van het [transitnetwerk.](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hoe wordt het verkeer naar de Azure-backbone geleid?

Het verkeer volgt het patroon: branch device ->ISP->Microsoft network edge->Microsoft DC (hub VNet)->Microsoft network edge->ISP->branch device

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Wat is er met dit model op elke locatie nodig? Alleen een internetverbinding?

Ja. Een internetverbinding en een fysiek apparaat dat IPsec ondersteunt, bij voorkeur van onze geïntegreerde [Virtual WAN-partners.](../articles/virtual-wan/virtual-wan-locations-partners.md) Optioneel u de configuratie en connectiviteit met Azure handmatig beheren vanaf uw voorkeursapparaat.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hoe schakel ik standaardroute (0.0.0.0/0) in een verbinding in (VPN, ExpressRoute of Virtueel netwerk):

Een virtuele hub kan een geleerde standaardroute naar een vpn/ExpressRoute-verbinding van virtueel netwerk/site-naar-site verspreiden als de vlag 'Ingeschakeld' is op de verbinding. Deze vlag is zichtbaar wanneer de gebruiker een virtuele netwerkverbinding, een VPN-verbinding of een ExpressRoute-verbinding bewerkt. Standaard wordt deze vlag uitgeschakeld wanneer een site of een ExpressRoute-circuit is verbonden met een hub. Het is standaard ingeschakeld wanneer een virtuele netwerkverbinding wordt toegevoegd om een VNet aan te sluiten op een virtuele hub. De standaardroute is niet afkomstig van de Virtuele WAN-hub. de standaardroute wordt gepropageerd als deze al is geleerd door de Virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub of als een andere verbonden site gedwongen tunneling heeft ingeschakeld.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Wat zijn de verschillen tussen de Virtuele WAN-typen (Basic en Standard)?

Met het WAN-type 'Basic' u een basishub (SKU = Basic) maken. Met een 'Standaard' WAN-type u standaardhub (SKU = Standard) maken. Basishubs zijn beperkt tot site-to-site VPN-functionaliteit. Met standaardhubs u ExpressRoute, User VPN (P2S), volledige mesh-hub en VNet-naar-VNet-doorvoer door de hubs hebben. U betaalt een basisvergoeding van $ 0,25 per uur voor standaardhubs en een kosten voor gegevensverwerking voor het doorreizen via de hubs tijdens VNet-naar-VNet-connectiviteit, evenals gegevensverwerking voor hub- naar hubverkeer. Zie [Virtuele WAN's voor basis- en standaardvoorzieningen voor](../articles/virtual-wan/virtual-wan-about.md#basicstandard)meer informatie. Zie de [prijspagina voor](https://azure.microsoft.com/pricing/details/virtual-wan/) prijzen.
