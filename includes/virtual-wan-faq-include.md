---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 28ea1e68441a57d67fef1e78153e00eb1bd09211
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143902"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Moet de gebruiker hub en spoke met SD-WAN/VPN-apparaten hebben om virtueel WAN van Azure te gebruiken?

Virtual WAN biedt tal van functies die zijn ingebouwd in één deel venster, zoals site/site-naar-site VPN-connectiviteit, gebruikers-P2S connectiviteit, ExpressRoute connectiviteit, Virtual Network connectiviteit, VPN ExpressRoute interconnectiviteit, VNet-naar-VNet transitieve connectiviteit, gecentraliseerde route ring, Azure Firewall en firewall Manager-beveiliging, bewaking, ExpressRoute-versleuteling en vele andere mogelijkheden. U hoeft niet al deze gebruiks kwesties te hebben om te beginnen met het gebruik van Virtual WAN. U kunt aan de slag met slechts één use-case. De virtuele WAN-architectuur is een hub-en spoke-architectuur met schaal-en prestatie mogelijkheden die zijn ingebouwd in het geval van branches (VPN/SD-WAN-apparaten), gebruikers (Azure VPN-clients, openVPN of IKEv2-clients), ExpressRoute-circuits, virtuele netwerken fungeren als spokes voor virtuele hub (s). Alle hubs zijn verbonden in full mesh in een standaard virtuele WAN, waardoor de gebruiker de Microsoft-backbone eenvoudig kan gebruiken voor any-to-any-connectiviteit (elke willekeurige spoke). Voor hub en spoke met SD-WAN/VPN-apparaten kunnen gebruikers deze hand matig instellen in de Azure Virtual WAN-portal of gebruikmaken van de virtuele WAN-partner CPE (SD-WAN/VPN) om verbinding met Azure in te stellen. Virtuele WAN-partners bieden automatisering voor connectiviteit. Dit is de mogelijkheid om de apparaatgegevens te exporteren naar Azure, de Azure-configuratie te downloaden en verbinding te maken met de virtuele WAN-hub van Azure. Voor punt-naar-site/gebruiker-VPN-verbindingen ondersteunen we [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554), openvpn of IKEv2-client. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Kunt u volledig gemeshe hubs in een virtueel WAN uitschakelen?

Virtuele WAN is beschikbaar in twee soorten: Basic en Standard. In het virtuele standaard WAN worden hubs niet gemesh. In een standaard virtueel WAN worden hubs genetten en automatisch verbonden wanneer het virtuele WAN voor het eerst wordt ingesteld. De gebruiker hoeft niets te doen. De gebruiker hoeft ook de functionaliteit niet in of uit te scha kelen om hubs te verkrijgen. Virtuele WAN biedt u een groot aantal routerings opties om verkeer tussen een spoke (VNet, VPN of ExpressRoute) te sturen. Het biedt het gemak van volledig Genet gewerkte hubs en ook de flexibiliteit van routerings verkeer volgens uw behoeften. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hoe worden Beschikbaarheidszones en tolerantie verwerkt in virtueel WAN?

Virtual WAN is een verzameling hubs en services die beschikbaar zijn in de hub. De gebruiker kan net zoveel virtueel WAN hebben als nodig is. In een virtuele WAN-hub zijn er meerdere services, zoals VPN, ExpressRoute, enzovoort. Elk van deze services (met uitzonde ring van de Azure Firewall) wordt geïmplementeerd in een Beschikbaarheidszones regio, dat wil zeggen dat de regio Beschikbaarheidszones ondersteunt. Als een regio een beschikbaarheids zone wordt na de eerste implementatie in de hub, kan de gebruiker de gateways opnieuw maken, waardoor de implementatie van een beschikbaarheids zone wordt geactiveerd. Alle gateways worden ingericht in een hub als actief-actief, wat inhoudt dat er toleranties zijn ingebouwd in een hub. Gebruikers kunnen verbinding maken met meerdere hubs als ze toleranties voor verschillende regio's willen. Hoewel het concept van virtueel WAN globaal is, is de daad werkelijke virtuele WAN-resource op basis van Resource Manager en gedistribueerd. Als de virtuele WAN-regio zelf een probleem had, blijven alle hubs in dat virtuele WAN functioneren, maar de gebruiker kan pas nieuwe hubs maken als de virtuele WAN-regio beschikbaar is.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Welke client ondersteunt de Azure Virtual WAN-gebruikers VPN (punt-naar-site)?

Virtual WAN ondersteunt [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554), openvpn-client of een IKEv2-client. Azure AD-verificatie wordt ondersteund met Azure VPN-client. mini maal Windows 10 client OS versie 17763,0 of hoger is vereist.  OpenVPN-Client (s) kunnen verificatie op basis van certificaten ondersteunen. Zodra op certificaten gebaseerde verificatie is geselecteerd op de gateway, wordt het *ovpn* -bestand weer geven om het te downloaden naar uw apparaat. IKEv2 ondersteunt zowel certificaat-als RADIUS-authenticatie. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Voor gebruikers VPN (punt-naar-site): Waarom is de P2S-client groep in twee routes verdeeld?

Elke gateway heeft twee instanties, de splitsing gaat zo dat elke gateway-instantie onafhankelijk client Ip's kan toewijzen voor verbonden clients en dat verkeer van het virtuele netwerk wordt teruggestuurd naar het juiste gateway-exemplaar om te voor komen dat de hop tussen gateway-exemplaren verloopt.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hoe kan ik DNS-servers toevoegen voor P2S-clients?

Er zijn twee opties voor het toevoegen van DNS-servers voor de P2S-clients. De eerste methode verdient de voor keur omdat hiermee de aangepaste DNS-servers worden toegevoegd aan de gateway in plaats van de client.

1. Gebruik het volgende Power shell-script om de aangepaste DNS-servers toe te voegen. Vervang de waarden voor uw omgeving.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Als u de Azure VPN-client voor Windows 10 gebruikt, kunt u het gedownloade XML-profiel bestand wijzigen en de ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** Tags toevoegen voordat u het importeert.

   ```powershell
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

Elke P2S-gateway van een gebruiker heeft twee instanties en elk exemplaar biedt ondersteuning voor bepaalde gebruikers als de schaal eenheid verandert. Schaal eenheid 1-3 ondersteunt 500 verbindingen, schaal eenheid 4-6 ondersteunt 1000 verbindingen, schaal eenheid 7-12 ondersteunt 5000 verbindingen en schaal eenheid 13-20 ondersteunt Maxi maal 10.000 verbindingen. 

Als voor beeld kan de gebruiker bijvoorbeeld 1 schaal eenheid kiezen. Elke schaal eenheid impliceert een actief-actief gateway die is geïmplementeerd en elk van de instanties (in dit geval 2) zou Maxi maal 500 verbindingen ondersteunen. Omdat u 500-verbindingen * 2 per gateway kunt verkrijgen, betekent dit niet dat u in plaats van de 500 voor deze schaal eenheid voor 1000 plant. Het kan zijn dat instanties moeten worden verwerkt waarvoor connectiviteit voor de extra 500 kan worden onderbroken als u het aantal aanbevolen verbindingen overschrijdt. Zorg er ook voor dat u de uitval tijd plant voor het geval dat u omhoog of omlaag wilt schalen op de schaal eenheid of wijzig de punt-naar-site-configuratie op de VPN-gateway.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Wat is het verschil tussen een virtuele Azure-netwerk gateway (VPN Gateway) en een virtueel WAN VPN-gateway van Azure?

Virtual WAN biedt grootschalige site-naar-site-connectiviteit en is ontworpen met het oog op doorvoer, schaalbaarheid en gebruiksgemak. Wanneer u een-site verbindt met een virtuele WAN-VPN-gateway, verschilt deze van een normale virtuele netwerk gateway die gebruikmaakt van het gateway type ' VPN '. Wanneer u een ExpressRoute-circuit verbindt met een virtuele WAN-hub, wordt er ook een andere resource gebruikt voor de ExpressRoute-gateway dan de reguliere virtuele netwerk gateway die gebruikmaakt van het gateway type ' ExpressRoute '. 

Virtual WAN ondersteunt Maxi maal 20 Gbps geaggregeerde door Voer voor VPN-en ExpressRoute. Virtual WAN heeft ook automatisering voor connectiviteit met een ecosysteem van CPE-partner partners. CPE-vertakkings apparaten hebben ingebouwde Automation die automatische voorzieningen en verbinding maakt met Azure Virtual WAN. Deze apparaten zijn beschikbaar binnen een groeiend ecosysteem van SD-WAN- en VPN-partners. Zie de [lijst met partners van voorkeur](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hoe verschilt virtuele WAN van een gateway van een virtueel netwerk in azure?

Een virtuele netwerk gateway VPN is beperkt tot 30 tunnels. U moet Virtual WAN gebruiken voor grootschalige VPN-verbindingen. U kunt tot 1.000 vertakkings verbindingen per regio (virtuele hub) verbinden met een samen stelling van 20 Gbps per hub. Een verbinding is een actief-actief-tunnel van het on-premises VPN-apparaat naar de virtuele hub. U kunt één hub per regio hebben, wat betekent dat u meer dan 1.000 vertakkingen in hubs kunt verbinden.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Wat is een virtuele WAN-gateway-schaal eenheid

Een schaal eenheid is een eenheid die is gedefinieerd voor het kiezen van een geaggregeerde door Voer van een gateway in een virtuele hub. 1 schaal eenheid VPN = 500 Mbps. 1 schaal eenheid van ExpressRoute = 2 Gbps. Voor beeld: 10 schaal eenheid van VPN betekent 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Welke providers van apparaten (virtuele WAN-partners) worden ondersteund?

Veel partners bieden momenteel ondersteuning voor de volledig geautomatiseerde Virtual WAN-ervaring. Zie [Virtual WAN-partners](../articles/virtual-wan/virtual-wan-locations-partners.md) voor meer informatie.

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Wat zijn de stappen voor automatisering voor Virtual WAN-partners?

Zie [Automatisering voor Virtual WAN-partners](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) voor meer informatie.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Moet ik een apparaat van een voorkeurspartner gebruiken?

Nee. U kunt ieder VPN-apparaat gebruiken dat voldoet aan de Azure-vereisten voor IKEv2/IKEv1 IPSec-ondersteuning. Virtual WAN heeft ook CPE-partner oplossingen waarmee de connectiviteit met Azure Virtual WAN wordt geautomatiseerd, waardoor het eenvoudiger wordt om IPsec VPN-verbindingen op schaal in te stellen.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hoe automatiseren Virtual WAN-partners connectiviteit met Azure Virtual WAN?

Softwarematige oplossingen voor netwerkconnectiviteit beheren hun vertakkingsapparaten doorgaans met behulp van een controller of een knooppunt voor apparaatinrichting. De controller kan Azure API’s gebruiken om de connectiviteit met Azure Virtual WAN te automatiseren. De automatisering omvat het uploaden van vertakkings gegevens, het downloaden van de Azure-configuratie, het instellen van IPSec-tunnels in virtuele Azure-hubs en het automatisch instellen van connectiviteit via het vertakkings apparaat in azure Virtual WAN. Wanneer u honderden filialen hebt, is het eenvoudig om verbinding te maken met behulp van virtuele WAN CPE-partners, omdat de voorbereidings ervaring de nood zaak heeft om grootschalige IPsec-connectiviteit in te stellen, te configureren en te beheren. Zie voor meer informatie [automatisering van virtuele WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Wat gebeurt er als een apparaat dat ik gebruik niet voor komt in de lijst met virtuele WAN-partners? Kan ik deze nog steeds gebruiken om verbinding te maken met Azure Virtual WAN VPN?

Ja als het apparaat IPsec IKEv1 of IKEv2 ondersteunt. Virtuele WAN-partners automatiseren de connectiviteit van het apparaat tot Azure VPN-eind punten. Dit impliceert de automatiserings stappen zoals ' vertakkings gegevens uploaden ', ' IPsec en configuratie ' en ' connectiviteit '. Omdat uw apparaat niet afkomstig is van een virtueel WAN-partner ecosysteem, moet u het zware werk van de Azure-configuratie hand matig opheffen en uw apparaat bijwerken om IPsec-verbindingen in te stellen.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hoe worden nieuwe partners die nog niet zijn vermeld in de startlijst van partners, opgenomen?

Alle virtuele WAN-Api's zijn open API. U kunt de documentatie over de [virtuele WAN-partner automatisering](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) door lopen om de technische haal baarheid te beoordelen. Een ideale partner is iemand die een apparaat heeft dat kan worden ingericht voor een IKEv1 of IKEv2 IPSec-verbinding. Zodra het bedrijf de automatiserings werkzaamheden voor het CPE-apparaat heeft voltooid op basis van de hierboven beschreven automatiserings richtlijnen, kunt u contact opnemen met de azurevirtualwan@microsoft.com hier aangegeven [connectiviteit via partners]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Als u een klant bent die een bepaalde bedrijfs oplossing moet worden vermeld als een virtuele WAN-partner, moet het bedrijf contact opnemen met het virtuele WAN door een e-mail te verzenden naar azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hoe wordt virtuele WAN ondersteunt SD-WAN-apparaten?

Virtuele WAN-partners automatiseren IPsec-verbinding met Azure VPN-eind punten. Als de virtuele WAN-partner een SD-WAN-provider is, is het geïmpliceerd dat de SD-WAN-controller de automatiserings-en IPsec-verbinding beheert met Azure VPN-eind punten. Als het SD-WAN-apparaat een eigen eind punt in plaats van Azure VPN nodig heeft voor een eigen SD-WAN-functionaliteit, kunt u het SD-WAN-eind punt in een Azure-VNet implementeren en samen met Azure Virtual WAN bestaan.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hoeveel VPN-apparaten kunnen verbinding maken met één hub?

Maxi maal 1.000 verbindingen worden ondersteund per virtuele hub. Elke verbinding bestaat uit vier koppelingen en elke koppelings verbinding ondersteunt twee tunnels die zich in een actief-actief configuratie bevinden. De tunnels worden beëindigd in een Azure Virtual hub VPN-gateway. Koppelingen vertegenwoordigen de fysieke ISP-koppeling op het filiaal/VPN-apparaat.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Wat is een vertakkings verbinding met Azure Virtual WAN?

Een verbinding van een vertakking of VPN-apparaat in azure Virtual WAN is niets maar een VPN-verbinding die vrijwel de VPN-site en de Azure-VPN Gateway in een virtuele hub verbindt.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan het on-premises VPN-apparaat verbinding maken met meerdere hubs?

Ja. De verkeers stroom is, wanneer deze begint, van het on-premises apparaat naar de dichtstbijzijnde micro soft-netwerk rand en vervolgens naar de virtuele hub.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Zijn er nieuwe Resource Manager-resources beschikbaar voor Virtual WAN?
  
Ja, virtuele WAN heeft nieuwe resource manager-resources. Zie voor meer informatie het [Overzicht](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan ik mijn favoriete virtuele netwerkapparaat implementeren en gebruiken (in een NVA-VNet) met Azure Virtual WAN?

Ja, u kunt uw favoriete virtuele netwerkapparaat (NVA)-VNet verbinden met de Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan ik een virtueel netwerk apparaat in de virtuele hub maken?

Een virtueel netwerk apparaat (NVA) kan niet binnen een virtuele hub worden geïmplementeerd. U kunt het echter maken in een spoke VNet dat is verbonden met de virtuele hub en de juiste route ring inschakelen zodat het verkeer wordt doorgestuurd volgens uw behoeften.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan een spoke VNet een virtuele netwerk gateway hebben?

Nee. De spoke VNet kan geen virtuele netwerk gateway hebben als deze is verbonden met de virtuele hub.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Is er ondersteuning voor BGP in VPN-connectiviteit?

Ja, BGP wordt ondersteund. Wanneer u een VPN-site maakt, kunt u hier de BGP-para meters opgeven. Dit betekent dat alle verbindingen die zijn gemaakt in azure voor die site worden ingeschakeld voor BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Is er informatie over licentieverlening of prijzen beschikbaar voor Virtual WAN?

Ja. Zie de [prijzenpagina](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Is het mogelijk om een Azure Virtual WAN te maken met een Resource Manager-sjabloon?

Een eenvoudige configuratie van één virtueel WAN met één hub en één vpnsite kan worden gemaakt met behulp van een Quick Start- [sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN is voornamelijk een REST-of portal-service.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan spoke VNets die is verbonden met een virtuele hub communiceren met elkaar (V2V Transit)?

Ja. Standaard virtuele WAN ondersteunt transitieve VNet-to-VNet-connectiviteit via de virtuele WAN-hub waarmee de VNets zijn verbonden. In de terminologie van het virtuele WAN verwijzen we naar deze paden als ' lokaal door sturen van virtueel WAN VNet ' voor VNets die zijn verbonden met een virtuele WAN-hub in één regio, en ' globaal virtueel WAN-VNet door Voer ' voor VNets die zijn verbonden via meerdere virtuele WAN-hubs in twee of meer regio's. Voor sommige scenario's kan spoke VNets ook rechtstreeks worden gekoppeld met behulp van [Virtual Network peering](../articles/virtual-network/virtual-network-peering-overview.md) naast lokale of globale virtuele WAN-trans missie. In dit geval heeft VNet-peering voor rang op de transitieve verbinding via de virtuele WAN-hub.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Is een vertakking-naar-vertakking-verbinding toegestaan in Virtual WAN?

Ja, een vertakking-naar-vertakking-verbinding is beschikbaar in Virtual WAN. Vertakking is conceptueel toepasbaar op VPN-site, ExpressRoute-circuits of Point-to-site/gebruikers-VPN-gebruikers. Het inschakelen van vertakking in vertakking is standaard ingeschakeld en kan worden gevonden in de WAN-configuratie-instellingen. Hierdoor kunnen VPN-filialen/-gebruikers verbinding maken met andere VPN-vertakkingen en wordt de doorvoer verbinding ingeschakeld tussen VPN-en ExpressRoute-gebruikers.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Gaat vertakkings-naar-vertakking verkeer via Azure Virtual WAN?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vereist Virtual WAN ExpressRoute van elke site?

Nee. Voor virtuele WAN is geen ExpressRoute van elke site vereist. Uw sites kunnen worden verbonden met een providernetwerk met behulp van een ExpressRoute-circuit. Voor sites die zijn verbonden met ExpressRoute naar een virtuele hub en IPsec VPN in dezelfde hub, biedt virtuele hub Transit connectiviteit tussen de VPN-en ExpressRoute-gebruiker.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Is er een netwerk doorvoer of verbindings limiet bij het gebruik van virtuele WAN-netwerken?

Netwerk doorvoer is per service in een virtuele WAN-hub. Hoewel u net zoveel virtuele Wan's kunt als u wilt, staat elk virtueel WAN 1 hub per regio toe. In elke hub is de cumulatieve VPN-door Voer Maxi maal 20 Gbps. de geaggregeerde door Voer van de ExpressRoute is Maxi maal 20 Gbps en de gezamenlijke door Voer van VPN/punt-naar-site VPN is Maxi maal 20 Gbps. De router in Virtual hub ondersteunt Maxi maal 50 Gbps voor het VNet-naar-VNet-verkeer en gaat uit van een totale belasting van 2000 VM voor alle VNets in virtuele WAN-hubs.

Wanneer VPN-sites verbinding maken met een hub, doen ze dit met verbindingen. Virtual WAN ondersteunt Maxi maal 1000 verbindingen of 2000 IPsec-tunnels per virtuele hub. Wanneer externe gebruikers verbinding maken met de virtuele hub, maken ze verbinding met de P2S VPN-gateway, die ondersteuning biedt voor Maxi maal 10.000 gebruikers, afhankelijk van de schaal eenheid (band breedte) die is gekozen voor de P2S VPN-gateway in de virtuele hub.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wat is de totale VPN-door Voer van een VPN-tunnel en een verbinding?

De totale VPN-door Voer van een hub is Maxi maal 20 Gbps op basis van de gekozen schaal eenheid van de VPN-gateway. De door Voer wordt gedeeld door alle bestaande verbindingen. Elke tunnel in een verbinding kan Maxi maal 1 Gbps ondersteunen.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>De instelling van 20 Gbps wordt niet weer geven voor de virtuele hub in de portal. Dat Hoe kan ik configureren?

Ga naar de VPN-gateway in een hub op de portal en klik op de schaal eenheid om deze naar de juiste instelling te wijzigen.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Staat Virtual WAN toe dat het on-premises apparaat meerdere Isp's parallel kan gebruiken of dat het altijd één VPN-tunnel is?

On-premises apparaat-oplossingen kunnen Traffic-beleid Toep assen om verkeer over meerdere tunnels te sturen naar de virtuele WAN-hub van Azure (VPN-gateway in de virtuele hub).

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

1. Langste voorvoegsel overeenkomst.
2. Lokale routes via interhub.
3. Statische routes via BGP: dit is in context voor de beslissing van de virtuele hub-router. Als de beslissings Maker echter de VPN-gateway is waarbij een site routes adverteert via BGP of statische adres voorvoegsels biedt, kunnen statische routes de voor keur hebben boven BGP-routes.
4. ExpressRoute (er) via VPN: er is een voor keur boven VPN wanneer de context een lokale hub is. Transit connectiviteit tussen ExpressRoute-circuits is alleen beschikbaar via Global Reach. In scenario's waarin ExpressRoute-circuit is verbonden met één hub en er een ander ExpressRoute-circuit is verbonden met een andere hub met een VPN-verbinding, kan VPN mogelijk de voor keur hebben voor scenario's tussen de verschillende hub.
5. Als padlengte.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Staat Virtual WAN hub connectiviteit tussen ExpressRoute-circuits toe.

Door Voer van een-naar-er is altijd via wereld wijd bereik. Virtuele-hub gateways worden geïmplementeerd in DC-of Azure-regio's. Wanneer twee ExpressRoute-circuits verbinding maken via een wereld wijd bereik, is het niet nood zakelijk dat het verkeer helemaal afkomstig is van de Edge-routers naar de virtuele hub-DC.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-circuits-or-vpn-connections"></a>Is er een begrip van het gewicht in virtuele WAN-circuits of VPN-verbindingen van Azure

Wanneer meerdere ExpressRoute-circuits zijn verbonden met een virtuele hub, biedt routerings gewicht op de verbinding een mechanisme voor de ExpressRoute in de virtuele hub om de voor keur te geven aan een circuit van de andere. Er is geen mechanisme om een gewicht in te stellen voor een VPN-verbinding. Azure krijgt altijd de voor keur aan een ExpressRoute-verbinding via een VPN-verbinding binnen één hub.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Wanneer twee hubs (hub 1 en 2) zijn verbonden en er een ExpressRoute-circuit is verbonden als een Strike-to-the-hubs, wat is het pad voor een VNet dat is verbonden met hub 1 om een VNet te bereiken dat is aangesloten op hub 2?

Het huidige gedrag is om de voor keur te geven aan het ExpressRoute-circuit over de hub-naar-hub voor VNet-naar-VNet-connectiviteit. Dit wordt echter niet aanbevolen in een virtuele WAN-installatie. Het virtuele WAN-team werkt aan een oplossing om de voor keur voor hub-naar-hub via het ExpressRoute-pad in te scha kelen. De aanbeveling is voor meerdere ExpressRoute-circuits (verschillende providers) om verbinding te maken met één hub en de hub-naar-hub-connectiviteit van virtuele WAN te gebruiken voor verkeer tussen regio's.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Is er ondersteuning voor IPv6 in virtuele WAN?

IPv6 wordt niet ondersteund in virtuele WAN-hub en gateways. Dit scenario wordt momenteel niet ondersteund als u een VNet hebt met IPv6-ondersteuning en u het VNet wilt verbinden met een virtueel WAN.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Wat zijn de verschillen tussen de virtuele WAN-typen (Basic en Standard)?

Zie [Basic en Standard Virtual wan's](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/virtual-wan/) voor prijzen.
