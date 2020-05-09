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
ms.openlocfilehash: cde644b821b8c9233c9a79c2c5e9fe8478850a65
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82997718"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Moet de gebruiker hub en spoke met SD-WAN/VPN-apparaten hebben om virtueel WAN van Azure te gebruiken?

Virtual WAN biedt tal van functies die zijn ingebouwd in één enkel deel venster, zoals site/site-naar-site VPN-connectiviteit, gebruikers-P2S connectiviteit, ExpressRoute-connectiviteit, Virtual Network connectiviteit, VPN ExpressRoute interconnectiviteit, VNET naar transitieve VNET-connectiviteit, gecentraliseerde route ring, Azure firewall en firewall Manager beveiliging, bewaking, ExpressRoute versleuteling en vele andere mogelijkheden. U hoeft niet al deze gebruiks voorbeelden te hebben om virtueel WAN te gaan gebruiken. U kunt gewoon aan de slag met slechts één use-case. De virtuele WAN-architectuur is een hub-en spoke-architectuur met ingebouwde schaal-en prestatie mogelijkheden waarbij vertakkingen (VPN/SD-WAN-apparaten), gebruikers (Azure VPN-clients, openVPN of IKEv2-clients), ExpressRoute-circuits, virtuele netwerken fungeren als spokes voor virtuele hub (s). Alle hubs zijn verbonden met een volledig net in een standaard virtuele WAN, waardoor de gebruiker de micro soft-backbone eenvoudig kan gebruiken voor een wille keurige verbinding (wille keurige spoke). Voor hub en spoke met SD-WAN/VPN-apparaten kunnen gebruikers deze hand matig instellen in de Azure Virtual WAN-portal of gebruikmaken van de virtuele WAN-partner CPE (SD-WAN/VPN) om verbinding met Azure in te stellen. Virtuele WAN-partners bieden automatisering voor de connectiviteit. Dit is de mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en verbinding te maken met de virtuele WAN-hub van Azure. Voor punt-naar-site/gebruiker-VPN-verbindingen ondersteunen we [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554), openvpn of IKEv2-client. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Welke client ondersteunt de Azure Virtual WAN-gebruikers VPN (punt-naar-site)?

Virtual WAN ondersteunt [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554), openvpn-client of een IKEv2-client. Azure AD-verificatie wordt ondersteund met Azure VPN-client. mini maal Windows 10 client OS versie 17763,0 of hoger is vereist.  OpenVPN-Client (s) kunnen verificatie op basis van certificaten ondersteunen. Zodra op certificaten gebaseerde verificatie is geselecteerd op de gateway, wordt het ovpn-bestand weer geven om het te downloaden naar uw apparaat. Zowel certificaat-als RADIUS-verificatie wordt ondersteund met IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Voor gebruikers VPN (punt-naar-site): Waarom is de P2S-client groep in twee routes verdeeld?

Elke gateway heeft twee instanties, de splitsing gaat zo dat elke gateway-instantie onafhankelijk client Ip's kan toewijzen voor verbonden clients en dat verkeer van het virtuele netwerk wordt teruggestuurd naar het juiste gateway-exemplaar om te voor komen dat de hop tussen gateway-exemplaren verloopt.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hoe kan ik DNS-servers toevoegen voor P2S-clients?

Er zijn twee opties voor het toevoegen van DNS-servers voor de P2S-clients.

1. Een ondersteunings ticket openen met micro soft en uw DNS-servers toevoegen aan de hub
2. Als u de Azure VPN-client voor Windows 10 gebruikt, kunt u het gedownloade XML-profiel bestand wijzigen en de ** \<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** -labels toevoegen voordat u het importeert.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Voor gebruikers VPN (punt-naar-site): hoeveel clients worden ondersteund?

Elke P2S-gateway van een gebruiker heeft twee instanties en elk exemplaar biedt ondersteuning voor bepaalde gebruikers als de schaal eenheid verandert. Schaal eenheid 1-3 ondersteunt 500 verbindingen, schaal eenheid 4-6 ondersteunt 1000 verbindingen, schaal eenheid 7-12 ondersteunt 5000 verbindingen en schaal eenheid 13-20 ondersteunt Maxi maal 10.000 verbindingen. Als voor beeld kan de gebruiker bijvoorbeeld 1 schaal eenheid kiezen. Elke schaal eenheid impliceert een actief-actief gateway die is geïmplementeerd en elk van de instanties (in dit geval 2) zou Maxi maal 500 verbindingen ondersteunen. Omdat u 500-verbindingen * 2 per gateway kunt verkrijgen, betekent dit niet dat u in plaats van de 500 voor deze schaal eenheid op 1000 hoeft te plannen, omdat er mogelijk exemplaren moeten worden verwerkt waarin connectiviteit voor de extra 500 mogelijk wordt verbroken als u het aantal aanbevolen verbindingen overschrijdt. Zorg er ook voor dat u de uitval tijd plant voor het geval dat u omhoog of omlaag wilt schalen op de schaal eenheid of wijzig de punt-naar-site-configuratie op de VPN-gateway.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Wat is het verschil tussen een virtuele Azure-netwerk gateway (VPN Gateway) en een virtueel WAN VPN-gateway van Azure?

Virtual WAN biedt grootschalige site-naar-site-connectiviteit en is ontworpen met het oog op doorvoer, schaalbaarheid en gebruiksgemak. Wanneer u een-site verbindt met een virtuele WAN-VPN-gateway, verschilt deze van een normale virtuele netwerk gateway die gebruikmaakt van het gateway type ' VPN '. Wanneer u een ExpressRoute-circuit verbindt met een virtuele WAN-hub, wordt er ook een andere resource gebruikt voor de ExpressRoute-gateway dan de reguliere virtuele netwerk gateway die gebruikmaakt van het gateway type ' ExpressRoute '. Virtual WAN ondersteunt Maxi maal 20 Gbps geaggregeerde door Voer voor VPN-en ExpressRoute. Virtual WAN heeft ook automatisering voor connectiviteit met een ecosysteem van CPE-partner partners. CPE-vertakkings apparaten hebben ingebouwde Automation die automatische voorzieningen en verbinding maakt met Azure Virtual WAN. Deze apparaten zijn beschikbaar binnen een groeiend ecosysteem van SD-WAN- en VPN-partners. Zie de [lijst met partners van voorkeur](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hoe verschilt virtuele WAN van een gateway van een virtueel netwerk in azure?

Een virtuele netwerk gateway VPN is beperkt tot 30 tunnels. U moet Virtual WAN gebruiken voor grootschalige VPN-verbindingen. U kunt tot 1.000 vertakkings verbindingen per regio (virtuele hub) verbinden met een samen stelling van 20 Gbps per hub. Een verbinding is een actief-actief-tunnel van het on-premises VPN-apparaat naar de virtuele hub. U kunt één hub per regio hebben, wat betekent dat u meer dan 1.000 vertakkingen in hubs kunt verbinden.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Wat is een virtuele WAN-gateway-schaal eenheid
Een schaal eenheid is een eenheid die is gedefinieerd voor het kiezen van een geaggregeerde door Voer van een gateway in een virtuele hub. 1 schaal eenheid VPN = 500 Mbps. 1 schaal eenheid van ExpressRoute = 2 Gbps. Voor beeld: 10 schaal eenheid van VPN betekent 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Welke providers van apparaten (virtuele WAN-partners) worden ondersteund?

Veel partners bieden momenteel ondersteuning voor de volledig geautomatiseerde Virtual WAN-ervaring. Zie [Virtual WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md) voor meer informatie. 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Wat zijn de stappen voor automatisering voor Virtual WAN-partners?

Zie [Automatisering voor Virtual WAN-partners](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) voor meer informatie.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Moet ik een apparaat van een voorkeurspartner gebruiken?

Nee. U kunt ieder VPN-apparaat gebruiken dat voldoet aan de Azure-vereisten voor IKEv2/IKEv1 IPSec-ondersteuning.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hoe automatiseren Virtual WAN-partners connectiviteit met Azure Virtual WAN?

Softwarematige oplossingen voor netwerkconnectiviteit beheren hun vertakkingsapparaten doorgaans met behulp van een controller of een knooppunt voor apparaatinrichting. De controller kan Azure API’s gebruiken om de connectiviteit met Azure Virtual WAN te automatiseren. De automatisering omvat het uploaden van vertakkings gegevens, het downloaden van de Azure-configuratie, het instellen van IPSec-tunnels in virtuele Azure-hubs en het automatisch instellen van connectiviteit via het vertakkings apparaat in azure Virtual WAN. Wanneer u honderden filialen hebt, is het eenvoudig om verbinding te maken met behulp van virtuele WAN CPE-partners, omdat de voorbereidings ervaring de nood zaak heeft om grootschalige IPsec-connectiviteit in te stellen, te configureren en te beheren. Zie voor meer informatie [automatisering van virtuele WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hoe wordt virtuele WAN ondersteunt SD-WAN-apparaten?

Virtuele WAN-partners automatiseren IPsec-verbinding met Azure VPN-eind punten. Als de virtuele WAN-partner een SD-WAN-provider is, is het geïmpliceerd dat de SD-WAN-controller de automatiserings-en IPsec-verbinding beheert met Azure VPN-eind punten. Als het SD-WAN-apparaat een eigen eind punt in plaats van Azure VPN nodig heeft voor een eigen SD-WAN-functionaliteit, kunt u het SD-WAN-eind punt in een Azure-VNet implementeren en samen met Azure Virtual WAN bestaan.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Veranderen er bij het gebruik van Virtual WAN bestaande functies voor connectiviteit?

Er zijn geen wijzigingen in bestaande connectiviteitsfuncties van Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Zijn er nieuwe Resource Manager-resources beschikbaar voor Virtual WAN?
  
Ja, met Virtual WAN worden nieuwe Resource Manager-resources geïntroduceert. Zie voor meer informatie het [Overzicht](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hoeveel VPN-apparaten kunnen verbinding maken met één hub?

Maxi maal 1.000 verbindingen worden ondersteund per virtuele hub. Elke verbinding bestaat uit vier koppelingen en elke koppelings verbinding ondersteunt twee tunnels die zich in een actief-actief configuratie bevinden. De tunnels worden beëindigd in een virtuele Azure-hub-vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan het on-premises VPN-apparaat verbinding maken met meerdere hubs?

Ja. De verkeers stroom is, wanneer deze begint, van het on-premises apparaat naar de dichtstbijzijnde micro soft-netwerk rand en vervolgens naar de virtuele hub.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan ik mijn favoriete virtuele netwerkapparaat implementeren en gebruiken (in een NVA-VNet) met Azure Virtual WAN?

Ja, u kunt uw favoriete virtuele netwerkapparaat (NVA)-VNet verbinden met de Azure Virtual WAN. Verbind eerst het NVA-VNet naar de hub met een Hub Virtual Network-verbinding. Maak vervolgens een virtuele hub-route met een volgende hop die naar het virtuele apparaat wijst. U kunt meerdere routes Toep assen op de route tabel van de virtuele hub. Eventuele spaken die verbonden zijn met het NVA-VNet moeten aanvullend worden verbonden met de virtuele hub om ervoor te zorgen dat de VNet-spaakroutes naar on-premises systemen worden doorgevoerd.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan ik een virtueel netwerk apparaat in de virtuele hub maken?

Een virtueel netwerk apparaat (NVA) kan niet binnen een virtuele hub worden geïmplementeerd. U kunt het echter maken in een spoke VNet dat is verbonden met de virtuele hub en een route in de hub inschakelen om verkeer voor het doel-VNet te omleiden via het NVA IP-adres (van de NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan een spoke VNet een virtuele netwerk gateway hebben?

Nee. De spoke VNet kan geen virtuele netwerk gateway hebben als deze is verbonden met de virtuele hub.

### <a name="is-there-support-for-bgp"></a>Is er ondersteuning voor BGP?

Ja, BGP wordt ondersteund. Wanneer u een VPN-site maakt, kunt u hier de BGP-para meters opgeven. Dit betekent dat alle verbindingen die zijn gemaakt in azure voor die site worden ingeschakeld voor BGP. Bovendien, als u een VNet met een NVA had en dit NVA VNet is gekoppeld aan een virtuele WAN-hub, om ervoor te zorgen dat routes van een NVA VNet op de juiste wijze worden geadverteerd, moeten spokes die zijn gekoppeld aan NVA VNet het BGP uitschakelen. Verbind deze spoke-VNets ook met het virtuele hub VNet om ervoor te zorgen dat spoke VNet-routes worden door gegeven aan on-premises systemen.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan ik verkeer beheren met behulp van de UDR in de virtuele hub?

Ja, u kunt verkeer door sturen naar een VNet met behulp van een route tabel van de virtuele hub. Zo kunt u routes instellen voor de doel-VNets in azure via een specifiek IP-adres (doorgaans van de NVA-NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Is er informatie over licentieverlening of prijzen beschikbaar voor Virtual WAN?

Ja. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>De prijs van een hub Hoe kan ik berekenen?

* U betaalt voor de services in de hub. Stel bijvoorbeeld dat u 10 vertakkingen of on-premises apparaten hebt die verbinding moeten maken met Azure Virtual WAN om verbinding te maken met VPN-eind punten in de hub. Dit is een VPN van 1 schaal eenheid = 500 Mbps. dit wordt in rekening gebracht op $0.361/uur. Elke verbinding wordt in rekening gebracht op $0,05/uur. Voor 10 verbindingen is de totale kosten van de service/HR $0,361 + $. 5/uur. Gegevens kosten voor verkeer waardoor Azure van toepassing is.

* Er zijn extra hub-kosten. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Als u ExpressRoute-gateway had vanwege ExpressRoute-circuits die verbinding maken met een virtuele hub, betaalt u voor de schaal eenheids prijs. Elke schaal eenheid is 2 Gbps en elke verbindings eenheid wordt in rekening gebracht tegen hetzelfde tarief als de VPN-verbindings eenheid.

* Als u spoke VNETs hebt verbonden met de hub, zijn de kosten voor peering op de spoke VNETs nog steeds van toepassing. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hoe worden nieuwe partners die nog niet zijn vermeld in de startlijst van partners, opgenomen?

Alle virtuele WAN-Api's zijn open API. U kunt de documentatie raadplegen voor het beoordelen van de technische haal baarheid. Als u een vraag hebt, stuurt u een e-mail azurevirtualwan@microsoft.comnaar. Een ideale partner is iemand die een apparaat heeft dat kan worden ingericht voor een IKEv1 of IKEv2 IPSec-verbinding.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Wat gebeurt er als een apparaat dat ik gebruik niet voor komt in de lijst met virtuele WAN-partners? Kan ik deze nog steeds gebruiken om verbinding te maken met Azure Virtual WAN VPN?

Ja als het apparaat IPsec IKEv1 of IKEv2 ondersteunt. Virtuele WAN-partners automatiseren de connectiviteit van het apparaat tot Azure VPN-eind punten. Dit impliceert de automatiserings stappen zoals ' vertakkings gegevens uploaden ', ' IPsec en configuratie ' en ' connectiviteit '. Omdat uw apparaat niet afkomstig is van een virtueel WAN-partner ecosysteem, moet u het zware werk van de Azure-configuratie hand matig opheffen en uw apparaat bijwerken om IPsec-verbindingen in te stellen.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Is het mogelijk om een Azure Virtual WAN te maken met een Resource Manager-sjabloon?

Een eenvoudige configuratie van één virtueel WAN met één hub en één vpnsite kan worden gemaakt met behulp van een Quick Start- [sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN is voornamelijk een REST-of portal-service.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Wordt wereldwijde VNET-peering met Azure Virtual WAN ondersteund? 

U kunt een VNet verbinden in een andere regio dan uw virtuele WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan spoke VNets die is verbonden met een virtuele hub communiceren met elkaar (V2V Transit)?

Ja. Standaard virtuele WAN ondersteunt Vnet naar transitieve Vnet-connectiviteit via de virtuele WAN-hub waarmee de Vnets zijn verbonden. In de terminologie van het virtuele WAN verwijzen we naar deze paden als ' lokaal door sturen van virtueel WAN VNet ' voor VNets die zijn verbonden met een virtuele WAN-hub in één regio, en ' globaal virtueel WAN-VNet door Voer ' voor VNets die zijn verbonden via meerdere virtuele WAN-hubs in twee of meer regio's. VNet-Transit ondersteunt Maxi maal 3 Gbps aan door Voer tijdens open bare preview. De door Voer wordt uitgebreid wanneer wereld wijd door Voer gaat.

Opmerking: voor V2V Transit Preview moet een VPN-GW in een virtuele hub worden geïmplementeerd om de routerings elementen te activeren die moeten worden gestart. Dit VPN-GW wordt niet gebruikt voor het V2V-Transit traject. Dit is een bekende beperking en wordt verwijderd op het moment van V2V GA. U kunt de VPN Gateway in de hub (s) verwijderen nadat deze volledig is gestart, omdat deze niet nodig is voor de V2V door voer. 

Voor sommige scenario's kan spoke Vnets ook rechtstreeks worden gekoppeld met behulp van [Virtual Network peering](../articles/virtual-network/virtual-network-peering-overview.md) naast lokale of globale virtuele WAN-trans missie. In dit geval heeft Vnet-peering voor rang op de transitieve verbinding via de virtuele WAN-hub. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Wat is een vertakkings verbinding met Azure Virtual WAN?

Een verbinding van een vertakkings apparaat in azure Virtual WAN ondersteunt Maxi maal vier koppelingen. Een koppeling is de fysieke connectiviteits koppeling op de filiaal locatie (bijvoorbeeld: ATT, Verizon enz.). Elke koppelings verbinding bestaat uit twee actieve/actieve IPsec-tunnels.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Is een vertakking-naar-vertakking-verbinding toegestaan in Virtual WAN?

Ja, een vertakking-naar-vertakking-verbinding is in Virtual WAN beschikbaar voor VPN en VPN naar ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Gaat vertakkings-naar-vertakking verkeer via Azure Virtual WAN?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vereist Virtual WAN ExpressRoute van elke site?

Nee, Virtual WAN heeft niet vanaf elke locatie ExpressRoute nodig. Het maakt gebruik van standaard-IPsec-site-to-site-connectiviteit via Internet koppelingen van het apparaat naar een virtuele WAN-hub van Azure. Uw sites kunnen worden verbonden met een providernetwerk met behulp van een ExpressRoute-circuit. Voor sites die zijn verbonden met ExpressRoute in een virtuele hub, kunnen sites vertakkings verkeer tussen VPN en ExpressRoute hebben.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Is er een doorvoerlimiet in het netwerk bij gebruik van Azure Virtual WAN?

Het aantal vertakkingen is beperkt tot 1000 verbindingen per hub/regio en in totaal 20 Gbps in de hub. U kunt één hub per regio hebben.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hoeveel VPN-verbindingen ondersteunt een virtuele WAN-hub?

Een virtuele WAN-hub van Azure kan Maxi maal 1.000 S2S-verbindingen, 10.000 P2S-verbindingen en vier ExpressRoute-verbindingen tegelijk ondersteunen.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wat is de totale VPN-door Voer van een VPN-tunnel en een verbinding?

De totale VPN-door Voer van een hub is Maxi maal 20 Gbps op basis van de gekozen schaal eenheid. De door Voer wordt gedeeld door alle bestaande verbindingen. Elke tunnel in een verbinding kan Maxi maal 1 Gbps ondersteunen.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>De instelling van 20 Gbps wordt niet weer geven voor de virtuele hub in de portal. Dat Hoe kan ik configureren?

Ga naar de VPN-gateway in een hub op de portal en klik op de schaal eenheid om deze naar de juiste instelling te wijzigen.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Staat Virtual WAN toe dat het on-premises apparaat meerdere Isp's parallel kan gebruiken of dat het altijd één VPN-tunnel is?

Een verbinding die zich in een virtueel WAN VPN bevindt, is altijd een actief/actief-tunnel (voor tolerantie binnen dezelfde hub/regio) met behulp van een koppeling die beschikbaar is op de vertakking. Deze koppeling kan een ISP-koppeling zijn op de on-premises vertakking. Virtuele WAN ' VPNSite ' biedt de mogelijkheid om koppelings gegevens toe te voegen aan de site. Als u meerdere Isp's in het filiaal hebt en alle Isp's een koppeling hebben opgegeven, kan deze informatie worden ingesteld in de gegevens van de VPN-site in Azure. Het beheren van failover over Isp's in de vertakking is echter volledig een vertakkings gerichte routerings bewerking.

### <a name="what-is-global-transit-architecture"></a>Wat is een wereld wijde doorvoer architectuur?

Zie [wereld wijde doorvoer netwerk architectuur en virtueel WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)voor meer informatie over de wereld wijde doorvoer architectuur.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hoe wordt het verkeer naar de Azure-backbone geleid?

Het verkeer volgt het patroon: vertakkings apparaat->ISP->micro soft-netwerk Edge->micro soft DC (hub VNet)->micro soft-netwerk Edge->ISP->-vertakkings apparaat

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Wat is er met dit model op elke locatie nodig? Alleen een internetverbinding?

Ja. Een Internet verbinding en een fysiek apparaat dat IPsec ondersteunt, bij voor keur van onze geïntegreerde [virtuele WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md). U kunt de configuratie en connectiviteit met Azure eventueel hand matig beheren vanaf uw voorkeurs apparaat.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hoe kan ik standaard route (0.0.0.0/0) inschakelen in een verbinding (VPN, ExpressRoute of Virtual Network):

Een virtuele hub kan een geleerde standaard route door geven naar een virtueel netwerk/site-naar-site VPN-ExpressRoute als de vlag is ingeschakeld op de verbinding. Deze markering wordt weer gegeven wanneer de gebruiker een verbinding met een virtueel netwerk, een VPN-verbinding of een ExpressRoute wijzigt. Deze vlag wordt standaard uitgeschakeld wanneer een site of een ExpressRoute-circuit is verbonden met een hub. Het is standaard ingeschakeld wanneer een virtuele netwerk verbinding wordt toegevoegd om een VNet te verbinden met een virtuele hub. De standaard route is niet afkomstig van de virtuele WAN-hub. de standaard route wordt door gegeven als deze al is geleerd door de virtuele WAN-hub als gevolg van het implementeren van een firewall in de hub, of als een andere verbonden site geforceerde tunneling heeft ingeschakeld.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hoe selecteer de virtuele hub in een virtueel WAN het beste pad voor een route van meerdere hubs

Als een virtuele hub dezelfde route van meerdere externe hubs bewaart, is de volg orde waarin deze wordt beslist als volgt:
1) Route oorsprong a) netwerk routes: VNET-voor voegsels worden rechtstreeks door de Virtual hub gateway b-hub geRouteTableeerd (statisch geconfigureerde routes) c) BGP d) InterHub-routes
2)  Route-metric: Virtual WAN geeft de voor keur aan ExpressRoute via VPN. ExpressRoute-peer heeft een hogere gewichts verhouding vergeleken met de VPN-peer
3)  Als padlengte

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Is er ondersteuning voor IPv6 in virtuele WAN?

IPv6 wordt niet ondersteund in virtuele WAN-hub en gateways. Dit scenario wordt ook niet ondersteund als u een VNET hebt met IPv6-ondersteuning en u het VNET wilt verbinden met een virtueel WAN. 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Wat zijn de verschillen tussen de virtuele WAN-typen (Basic en Standard)?

Met het ' Basic ' WAN-type kunt u een Basic-hub maken (SKU = basis). Met een ' standaard ' WAN-type kunt u standaard hub (SKU = Standard) maken. Basis hubs zijn beperkt tot de site-naar-site VPN-functionaliteit. Met standaard hubs kunt u ExpressRoute, User VPN (P2S), Full mesh hub en VNet-naar-VNet-transit via de hubs hebben. U betaalt een basis bedrag van $0,25 per uur voor standaard hubs en een gegevens verwerkings vergoeding voor door Voer via de hubs tijdens VNet-naar-VNet-connectiviteit, evenals gegevens verwerking voor hub naar hub-verkeer. Zie voor meer informatie [Basic en Standard Virtual wan's](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/virtual-wan/) voor prijzen.
