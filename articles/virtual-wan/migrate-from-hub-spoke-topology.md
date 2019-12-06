---
title: Migreren naar Azure Virtual WAN
description: Meer informatie over hoe u migreert naar Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: c50f2ad0a15e9793fd5a799d44bf34fc2b48e14b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851261"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migreren naar Azure Virtual WAN
Met Azure Virtual WAN kunnen bedrijven hun wereld wijde connectiviteit vereenvoudigen en profiteren van de schaal van het wereld wijde micro soft-netwerk. In dit technisch document vindt u technische gegevens voor bedrijven die willen migreren van een bestaande hub-en-spoke-topologie van een klant, naar een ontwerp dat gebruikmaakt van door micro soft beheerde virtuele WAN-hubs.

In de [wereld wijde doorvoer netwerk architectuur en het virtuele WAN](virtual-wan-global-transit-network-architecture.md) -artikel worden de voor delen van Azure Virtual WAN ingeschakeld voor ondernemingen die een in de Cloud gericht modern mondiaal netwerk gebruiken.

![hub en spoke](./media/migrate-from-hub-spoke-topology/figure1.png)
**afbeelding 1: virtueel WAN van Azure**

Het hub-en-spoke-connectiviteits model van Azure Virtual Data Center (VDC) is door duizenden klanten aangenomen om gebruik te maken van de standaard transitieve route ring van Azure-netwerken om eenvoudige en schaal bare Cloud netwerken te bouwen. Azure Virtual WAN bouwt voort op deze concepten en introduceert nieuwe mogelijkheden waarmee wereld wijde connectiviteits topologieën worden toegestaan, niet alleen tussen on-premises locaties en Azure, maar biedt klanten ook de mogelijkheid om de schaal van het micro soft-netwerk te gebruiken om hun bestaande globale netwerken.

In dit artikel wordt beschreven hoe u een bestaande hybride omgeving naar een virtueel WAN migreert.

## <a name="scenario"></a>Scenario

Contoso is een wereld wijde financiële organisatie met kant oren in Europa en Azië. Ze zijn van plan om hun bestaande toepassingen te verplaatsen van een on-premises Data Center naar Azure en een funderings ontwerp hebben gebouwd op basis van de VDC-architectuur, met inbegrip van regionale virtuele netwerken met door klanten beheerde hub voor hybride connectiviteit. Als onderdeel van de overstap naar Cloud technologieën is het netwerk team gewerkt, zodat de connectiviteit is geoptimaliseerd voor het bedrijf dat verdergaat.

Afbeelding 2 toont een weer gave op hoog niveau van het bestaande wereld wijde netwerk, met inbegrip van connectiviteit met meerdere Azure-regio's.

![contoso bestaande netwerk topologie](./media/migrate-from-hub-spoke-topology/figure2.png)
**afbeelding 2: contoso bestaande netwerk topologie**

De volgende punten kunnen worden begrepen van de bestaande netwerk topologie:
 
- Een hub en spoke-topologie wordt in meerdere regio's gebruikt, inclusief ExpressRoute Premium-circuits voor de connectiviteit van een gemeen schappelijk particulier WAN.
- Sommige van deze sites hebben ook rechtstreeks VPN-tunnels in azure om toepassingen te bereiken die in de micro soft-Cloud worden gehost.

## <a name="requirements"></a>Vereisten
Het netwerk team is gewerkt met het leveren van een globaal netwerk model dat de contoso-migratie naar de cloud kan ondersteunen en moet worden geoptimaliseerd op het gebied van kosten, schaal en prestaties. In samen vatting moeten aan de volgende vereisten worden voldaan:
- Geef zowel hoofd kantoor (Head Quarter) als filialen op met het geoptimaliseerde pad naar in de Cloud gehoste toepassingen. 
- De afhankelijkheid van bestaande on-premises Data Centers (DC) voor het beëindigen van VPN verwijderen, met behoud van de volgende connectiviteits paden:
    - **Vertakking-naar-VNet**: VPN verbonden kant oren moeten toegang kunnen krijgen tot toepassingen die zijn gemigreerd naar de cloud in de lokale Azure-regio.
    - **Vertakking-naar**-hub-naar-VNet: VPN verbonden kant oren moeten toegang kunnen krijgen tot toepassingen die zijn gemigreerd naar de cloud in de externe Azure-regio. 
    - **Vertakking-naar-vertakking**: regionale VPN verbonden kant oren moeten kunnen communiceren met elkaar en EXPRESSROUTE verbonden hoofd kantoor/DC-sites. 
    - **Vertakking-naar**-hub-naar-vertakking: wereld wijd gescheiden VPN verbonden kant oren moeten kunnen communiceren met elkaar en alle EXPRESSROUTE verbonden hoofd kantoor/DC-sites.
    - **Vertakking-naar-Internet**: verbonden sites moeten kunnen communiceren met internet en dit verkeer moet worden gefilterd en geregistreerd.
    - **Vnet-naar-VNet**: de virtuele spoke-netwerken in dezelfde regio moeten kunnen communiceren met elkaar.
    - **Vnet-naar**-hub-naar-hub-naar-VNet: spoke virtuele netwerken in de verschillende regio's moeten kunnen communiceren met elkaar.
- Bieden de mogelijkheid voor zwervende gebruikers van Contoso (laptop en telefoon) om toegang te krijgen tot bedrijfs bronnen, maar niet op het bedrijfs netwerk.

## <a name="azure-virtual-wan-architecture"></a>Azure Virtual WAN-architectuur

Afbeelding 3 toont een weer gave op hoog niveau van de bijgewerkte doel topologie met behulp van Azure Virtual WAN om te voldoen aan de vereisten die in de vorige sectie zijn beschreven.

![contoso virtuele WAN-architectuur](./media/migrate-from-hub-spoke-topology/figure3.png)
**afbeelding 3: virtuele WAN-architectuur van Azure**

Samenvatting: 
- HOOFD kantoor in Europa blijft ExpressRoute verbonden, Europa on-premises DC is volledig gemigreerd naar Azure en nu buiten gebruik gesteld.
- De Azië-DC en hoofd kantoor blijven verbonden met het particuliere WAN. Azure Virtual WAN wordt nu gebruikt voor het verbeteren van het lokale vervoerder netwerk en het bieden van globale connectiviteit 
- Virtuele WAN-hubs van Azure worden geïmplementeerd in zowel Europa-west als Zuid Azië-oost Azure-regio's om connectiviteits hub te bieden voor ExpressRoute en VPN-verbonden apparaten. 
- Hubs bieden ook VPN-beëindiging voor zwervende gebruikers via meerdere client typen met behulp van OpenVPN-connectiviteit met het wereld wijde mesh-netwerk, zodat toegang wordt geboden tot niet alleen toepassingen die naar Azure zijn gemigreerd, maar ook op alle resources die on-premises resteren. 
- Internet verbinding voor bronnen binnen een virtueel netwerk dat door Azure Virtual WAN wordt verschaft. Internet connectiviteit voor externe sites wordt ook door Azure Virtual WAN verschaft. Lokale Internet-groepen worden ondersteund via partner integratie voor geoptimaliseerde toegang tot SaaS-services zoals Office 365.

## <a name="migrate-to-azure-virtual-wan"></a>Migreren naar Azure Virtual WAN

In deze sectie worden de verschillende stappen voor de migratie naar Azure Virtual WAN beschreven.
 
### <a name="vdc-hub-and-spoke-single-region"></a>VDC hub-en-spoke-ene regio

De volgende afbeelding toont een topologie van één regio voor contoso vóór de implementatie van het virtuele WAN van Azure.

![Virtuele WAN-hubs implementeren](./media/migrate-from-hub-spoke-topology/figure4.png)

 **Afbeelding 4: VDC hub en spoke-één regio – stap 1**

In overeenstemming met de VDC-benadering (Virtual Data Center) bevat het virtuele netwerk dat door de klant wordt beheerd, meerdere functie blokken:
- Gedeelde services (een algemene functie vereist door meerdere spokes) een voor beeld waarvan contoso gebruikmaakt van Windows Server-domein controllers op IaaS-virtuele machines (Infrastructure-as-a-Service).
- IP/route ring firewall services worden door een virtueel netwerk apparaat van derden verschaft, waardoor een spoke-to-spoke Layer-3 IP-route ring mogelijk wordt. 
- Services voor binnenkomend/uitgaand verkeer, waaronder Azure-toepassing gateway voor inkomende HTTPS-aanvragen en proxy services van derden die op virtuele machines worden uitgevoerd voor gefilterde uitgaande toegang tot Internet bronnen.
- ExpressRoute en VPN-Virtual Network gateway voor connectiviteit met on-premises netwerken.

### <a name="deploy-virtual-wan-hubs"></a>Virtuele WAN-hubs implementeren

De eerste stap voor het implementeren van een virtuele WAN-hub in elke regio. Stel de virtuele WAN-hub in met VPN Gateway en Express route gateway, zoals beschreven in de volgende artikelen:  
- [Zelf studie: een site-naar-site-verbinding maken met behulp van Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Zelf studie: een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> Azure Virtual WAN moet gebruikmaken van de standaard-SKU om enkele van de verkeers paden die in dit artikel worden beschreven, in te scha kelen.


![virtuele WAN-hubs implementeren](./media/migrate-from-hub-spoke-topology/figure5.png)
**afbeelding 5: VDC hub en spoke to Virtual WAN Migration – stap 2**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Externe sites (ExpressRoute en VPN) verbinden met virtuele WAN

Nu gaan we de virtuele WAN-hub verbinden met de bestaande ExpressRoute-circuits en site-naar-site-Vpn's instellen via internet naar externe filialen.

> [!NOTE]
> Express route-circuits moeten worden bijgewerkt naar het Premium-SKU-type om verbinding te maken met een virtuele WAN-hub.


![externe sites verbinden met Virtual WAN](./media/migrate-from-hub-spoke-topology/figure6.png)
**afbeelding 6: VDC hub en spoke to Virtual WAN Migration-stap 3**

Op dit moment zullen de on-premises netwerk apparatuur routes ontvangen die overeenkomen met de IP-adres ruimte die is toegewezen aan de virtuele door WAN beheerde hub VNet. Met externe VPN-verbindingen in deze fase worden twee paden naar bestaande toepassingen in de spoke Virtual Networks weer geven. Deze apparaten moeten zo worden geconfigureerd dat ze de tunnel naar de VDC-hub blijven gebruiken om te zorgen voor symmetrische route ring tijdens de overgangs fase.

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Hybride connectiviteit testen via Virtual WAN

Voordat u de beheerde virtuele WAN-hub voor productie connectiviteit gebruikt, is het raadzaam om een virtueel netwerk met test Spaak en een virtuele WAN-VNet-verbinding in te stellen. Controleer of de verbindingen met deze test omgeving via ExpressRoute en site-naar-site VPN werken voordat u verdergaat met de volgende stappen.

![hybride connectiviteit testen via Virtual WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
**afbeelding 7: VDC hub en spoke to Virtual WAN Migration-stap 4**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>Overgangs verbinding met virtuele WAN-hub


![overgangs verbinding met virtuele WAN-hub](./media/migrate-from-hub-spoke-topology/figure8.png)
**afbeelding 8: VDC hub en spoke to Virtual WAN Migration – stap 5**

**a**. Verwijder de bestaande peering-verbindingen van spoke Virtual Networks naar de oude VDC-hub. Toegang tot toepassingen in spoke Virtual Networks is niet beschikbaar totdat de stappen a-c zijn voltooid.

**b**. Verbind de spoke-virtuele netwerken met de virtuele WAN-hub via VNet-verbindingen.

**c**. Verwijder alle door de gebruiker gedefinieerde routes (UDR) die eerder in spoke Virtual Networks zijn gebruikt voor een spoke-to-spoke-communicatie. Dit pad is nu ingeschakeld door de dynamische route ring die beschikbaar is binnen de virtuele WAN-hub.

**d**. Bestaande ExpressRoute-en VPN-gateways in de VDC-hub worden nu buiten gebruik gesteld om de volgende stap (e) toe te staan.

**e**. Verbind de oude VDC hub (virtueel netwerk van de hub) met de virtuele WAN-hub via een nieuwe VNet-verbinding.

### <a name="old-hub-becomes-shared-services-spoke"></a>De oude hub wordt gedeeld Services-spoke

We hebben ons Azure-netwerk nu opnieuw ontworpen om de virtuele WAN-hub het centrale punt in onze nieuwe topologie te maken.

![oude hub wordt gedeeld Services-spoke](./media/migrate-from-hub-spoke-topology/figure9.png)
**afbeelding 9: VDC hub en spoke to Virtual WAN Migration-stap 6**

Omdat de virtuele WAN-hub een beheerde entiteit is en de implementatie van aangepaste resources, zoals virtuele machines, niet toestaat, bestaat de Shared Services-blok kering nu als een spoke-virtueel netwerk, hosten functies zoals Internet binnenkomen via Azure-toepassing gateway of netwerk gevirtualiseerd apparaat. Verkeer tussen de Shared Services-omgeving en de back-end virtuele machines doorvoert nu de virtuele door WAN beheerde hub.

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>On-premises connectiviteit optimaliseren om virtueel WAN volledig te gebruiken

In deze fase heeft Contoso meestal hun migraties van bedrijfs toepassingen in de Microsoft Cloud voltooid, met slechts enkele oudere toepassingen die resteren binnen de on-premises domein controller.

![Optimaliseer de on-premises connectiviteit om virtuele WAN-](./media/migrate-from-hub-spoke-topology/figure10.png)
**afbeelding 10: VDC hub en spoke to Virtual WAN Migration te benutten – stap 7**

 Om gebruik te maken van de volledige functionaliteit van Azure Virtual WAN, besluit contoso om hun oude on-premises VPN-verbindingen buiten gebruik te stellen. Alle vertakkingen die worden gebruikt voor toegang tot hoofd kantoor of DC-netwerken, kunnen het micro soft Global Network door geven met behulp van de ingebouwde Transit routering van Azure Virtual WAN. 

> [!NOTE]
> ExpressRoute Global Reach is een alternatieve keuze voor klanten die de micro soft-backbone willen gebruiken om hun bestaande persoonlijke Wan's aan te vullen.

## <a name="end-state-architecture-and-traffic-paths"></a>End-status architectuur en verkeers paden


![End State-architectuur en verkeers paden](./media/migrate-from-hub-spoke-topology/figure11.png)
**afbeelding 11: virtueel WAN met dubbele regio**

In deze sectie vindt u een overzicht van hoe deze topologie voldoet aan de oorspronkelijke vereisten door te kijken naar een aantal voor beelden van verkeers stromen.

### <a name="path-1"></a>Pad 1

In pad 1 wordt de verkeers stroom van een met S2S VPN verbonden vertakking in Azië naar een Azure VNet in de regio Zuid-Azië-oost beschreven.

Het verkeer wordt als volgt gerouteerd:
- Azië-vertakking is verbonden via robuuste, beschikbaar S2S-tunnels in Zuid-Azië-oost virtuele WAN-hub.
- Virtuele WAN-hub verkeer routeert lokaal naar verbonden VNet.

![Stroom 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Pad 2
In pad 2 wordt de verkeers stroom van de ExpressRoute Connected European hoofd kantoor naar een Azure VNet in de regio Zuid-Azië-oost beschreven.

Het verkeer wordt als volgt gerouteerd:
- Euro pees hoofd kantoor is verbonden via een Premium ExpressRoute-circuit in Europa-west virtuele WAN-hub.
- Met Virtual WAN hub-to-hub globale connectiviteit kan verkeer worden overgedragen naar VNet dat is verbonden in de externe regio.

![Stroom 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Pad 3
In pad 3 wordt de verkeers stroom van de Azië op de lokale domein controller die is verbonden met particulier WAN, beschreven in een met Europese S2S verbonden vertakking.

Het verkeer wordt als volgt gerouteerd:
- De Azië-DC is verbonden met een lokale privé WAN-provider.
- ExpressRoute-circuit lokaal eindigt op particuliere WAN-verbindingen met de Zuid-Azië-oost virtuele WAN-hub.
- Met Virtual WAN hub-to-hub globale connectiviteit kan verkeer worden overgedragen.

![Stroom 3](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>Pad 4
In pad 4 wordt de verkeers stroom van een Azure VNet in Zuid-Azië-oost regio naar een Azure VNet in Europa-west regio beschreven.

Het verkeer wordt als volgt gerouteerd:
- Virtual WAN hub-to-hub wereld wijde connectiviteit maakt systeem eigen door Voer van alle verbonden Azure VNets zonder verdere gebruikers configuratie.

![Stroom 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Pad 5
Path 5 beschrijft de verkeers stroom van zwervende VPN-gebruikers (P2S) naar een Azure-VNet in de Europa-west regio.

Het verkeer wordt als volgt gerouteerd:
- Gebruikers van laptops en mobiele apparaten maken gebruik van de OpenVPN-Client voor transparante connectiviteit in de P2S VPN-gateway in Europa-west.
- Europa-west virtuele WAN-hub stuurt verkeer lokaal naar het verbonden VNet.

![Stroom 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Beveiliging en beleids beheer via Azure Firewall

Contoso heeft nu de connectiviteit voor alle branches en VNets in overeenstemming met de vereisten die eerder in dit document zijn besproken. Om te voldoen aan de vereisten voor beveiligings beheer en netwerk isolatie, moeten ze het verkeer blijven scheiden en registreren via het hub-netwerk, voorheen deze functie werd uitgevoerd door een virtueel netwerk apparaat (NVA). Contoso wil ook hun bestaande proxy services buiten gebruik stellen en systeem eigen Azure-Services voor uitgaande internet filters nemen. 

![beveiligings-en beleids beheer via Azure Firewall](./media/migrate-from-hub-spoke-topology/figure12.png)
**afbeelding 12: Azure firewall in virtuele WAN (beveiligde virtuele hub)**

De volgende stappen op hoog niveau zijn vereist voor het introduceren van Azure Firewall in de virtuele WAN-hubs om een uniform punt van beleids beheer mogelijk te maken. Dit proces en het concept van beveiligde virtuele hubs worden [hier](https://go.microsoft.com/fwlink/?linkid=2107683)volledig beschreven.
- Azure Firewall beleid maken.
- Firewall beleid koppelen aan een virtuele WAN-hub in Azure.
- Met de bovenstaande stap kan de bestaande virtuele WAN-hub functioneren als een beveiligde virtuele hub en de vereiste Azure Firewall resources implementeren.

> [!NOTE]
> Als de Azure Firewall is geïmplementeerd in een standaard virtuele WAN-hub (SKU: Standard): V2V, B2V, V2I en B2I FW-beleid worden alleen afgedwongen op het verkeer dat afkomstig is van de Vnets en de vertakkingen die zijn verbonden met de specifieke hub waarop de Azure FW is geïmplementeerd (beveiligde hub). Verkeer dat afkomstig is van externe Vnets en vertakkingen die zijn gekoppeld aan andere virtuele WAN-hubs in hetzelfde virtuele WAN, wordt niet ' firewalled ', ook al zijn deze externe vertakkingen en Vnet onderling verbonden via Virtual WAN hub naar hub-koppelingen. Ondersteuning voor cross-hub-firewalls bevindt zich op het schema voor virtuele WAN-en firewall beheer van Azure.

De volgende paden beschrijven de connectiviteits paden die zijn ingeschakeld door Azure beveiligde virtuele hubs te gebruiken.

### <a name="path-6"></a>Pad 6
Path 6 beschrijft de stroom van beveiligde verkeer tussen VNets binnen dezelfde regio.

Het verkeer wordt als volgt gerouteerd:
- Virtuele netwerken die zijn verbonden met dezelfde beveiligde virtuele hub, sturen nu verkeer naar via de Azure Firewall.
- Azure Firewall kunt beleid Toep assen op deze stromen.

![Stroom 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Pad 7
Path 7 beschrijft de verkeers stroom van een Azure-Vnet naar het internet of de beveiligings service van derden.

Het verkeer wordt als volgt gerouteerd:
- Virtuele netwerken die zijn verbonden met de beveiligde virtuele hub, kunnen verkeer verzenden naar openbaar, bestemmingen op internet met behulp van de beveiligde hub als centraal punt van Internet toegang.
- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligings service van derden voor inspectie.

![Stroom 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Pad 8
In pad 8 wordt de verkeers stroom van de beveiligings service van de vertakking naar Internet of van derden beschreven.

Het verkeer wordt als volgt gerouteerd:
- Vertakkingen die zijn verbonden met de beveiligde virtuele hub kunnen verkeer verzenden naar open bare bestemmingen op internet, met behulp van de beveiligde hub als centraal punt van Internet toegang.
- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligings service van derden voor inspectie.

![Stroom 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Virtual WAN](virtual-wan-about.md)
