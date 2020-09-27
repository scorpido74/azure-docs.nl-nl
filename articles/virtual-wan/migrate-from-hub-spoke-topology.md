---
title: 'Architectuur: migreren naar Azure Virtual WAN'
description: Meer informatie over het migreren naar Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b07ed4589a54948ef87f516ac4bb97ef8492283e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398832"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migreren naar Azure Virtual WAN

Met Azure Virtual WAN kunnen bedrijven hun wereld wijde connectiviteit vereenvoudigen om te profiteren van de schaal van het wereld wijde micro soft-netwerk. Dit artikel bevat technische Details voor bedrijven die willen migreren van een bestaande hub-en-spoke-topologie van een klant, naar een ontwerp dat gebruikmaakt van door micro soft beheerde virtuele WAN-hubs.

Voor meer informatie over de voor delen die Azure Virtual WAN maakt voor ondernemingen die een in de Cloud gerichte moderne wereld wijde Enter prise-netwerk gebruiken, Zie [wereld wijde doorvoer netwerk architectuur en virtueel WAN](virtual-wan-global-transit-network-architecture.md).

![hub-en spoke- ](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
 **afbeelding: Azure Virtual WAN**

Het Azure hub-en-spoke-connectiviteits model is door duizenden klanten aangenomen om gebruik te maken van de standaard transitieve route ring van Azure-netwerken om eenvoudige en schaal bare Cloud netwerken te kunnen bouwen. Azure Virtual WAN bouwt voort op deze concepten en introduceert nieuwe mogelijkheden waarmee wereld wijde connectiviteits topologieën worden toegestaan, niet alleen tussen on-premises locaties en Azure, maar biedt klanten ook de mogelijkheid om de schaal van het micro soft-netwerk te benutten om hun bestaande mondiale netwerken te verbeteren.

In dit artikel wordt beschreven hoe u een bestaande hybride omgeving naar een virtueel WAN migreert.

## <a name="scenario"></a>Scenario

Contoso is een wereld wijde financiële organisatie met kant oren in Europa en Azië. Ze zijn van plan om hun bestaande toepassingen te verplaatsen van een on-premises Data Center naar Azure en een basis ontwerp hebben gebouwd op basis van de hand matige hub-en-spoke-architectuur, met inbegrip van regionale virtuele netwerken met door klanten beheerde hub voor hybride connectiviteit. Als onderdeel van de overstap naar Cloud technologieën is het netwerk team gewerkt om ervoor te zorgen dat de connectiviteit is geoptimaliseerd voor het bedrijf dat verdergaat.

De volgende afbeelding toont een weer gave op hoog niveau van het bestaande wereld wijde netwerk, met inbegrip van connectiviteit met meerdere Azure-regio's.

![Contoso bestaande netwerk topologie ](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
 **afbeelding: contoso bestaande netwerk topologie**

De volgende punten kunnen worden begrepen van de bestaande netwerk topologie:

- Een hub en spoke-topologie wordt in meerdere regio's gebruikt, inclusief ExpressRoute Premium-circuits voor de connectiviteit van een gemeen schappelijk particulier WAN.

- Sommige van deze sites hebben ook rechtstreeks VPN-tunnels in azure om toepassingen te bereiken die in de micro soft-Cloud worden gehost.

## <a name="requirements"></a>Vereisten

Het netwerk team is gewerkt met het leveren van een globaal netwerk model dat de contoso-migratie naar de cloud kan ondersteunen en moet worden geoptimaliseerd op het gebied van kosten, schaal en prestaties. In samen vatting moeten aan de volgende vereisten worden voldaan:

- Geef zowel hoofd kantoor (Head Quarter) als filialen op met het geoptimaliseerde pad naar in de Cloud gehoste toepassingen.
- De afhankelijkheid van bestaande on-premises Data Centers (DC) voor het beëindigen van VPN verwijderen terwijl de volgende verbindings paden behouden blijven:
  - **Vertakking-naar-VNet**: VPN verbonden kant oren moeten toegang kunnen krijgen tot toepassingen die zijn gemigreerd naar de cloud in de lokale Azure-regio.
  - **Vertakking-naar**-hub-naar-VNet: VPN verbonden kant oren moeten toegang kunnen krijgen tot toepassingen die zijn gemigreerd naar de cloud in de externe Azure-regio.
  - **Vertakking-naar-vertakking**: regionale VPN verbonden kant oren moeten kunnen communiceren met elkaar en EXPRESSROUTE verbonden hoofd kantoor/DC-sites.
  - **Vertakking-naar**-hub-naar-vertakking: wereld wijd gescheiden VPN verbonden kant oren moeten kunnen communiceren met elkaar en alle EXPRESSROUTE verbonden hoofd kantoor/DC-sites.
  - **Vertakking-naar-Internet**: verbonden sites moeten kunnen communiceren met internet. Dit verkeer moet worden gefilterd en geregistreerd.
  - **Vnet-naar-VNet**: de virtuele spoke-netwerken in dezelfde regio moeten kunnen communiceren met elkaar.
  - **Vnet-naar**-hub-naar-hub-naar-VNet: spoke virtuele netwerken in de verschillende regio's moeten kunnen communiceren met elkaar.
- Bieden de mogelijkheid voor zwervende gebruikers van Contoso (laptop en telefoon) om toegang te krijgen tot bedrijfs bronnen terwijl ze zich niet in het bedrijfs netwerk besturen.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN-architectuur

In de volgende afbeelding ziet u een overzicht op hoog niveau van de bijgewerkte doel topologie met behulp van Azure Virtual WAN om te voldoen aan de vereisten die in de vorige sectie zijn beschreven.

![Afbeelding van de virtuele WAN-architectuur van Contoso ](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
 **: Azure Virtual WAN-architectuur**

Samenvatting:

- HOOFD kantoor in Europa blijft ExpressRoute verbonden, Europa on-premises DC is volledig gemigreerd naar Azure en nu buiten gebruik gesteld.
- De Azië-DC en hoofd kantoor blijven verbonden met het particuliere WAN. Azure Virtual WAN wordt nu gebruikt voor het verbeteren van het lokale vervoerder netwerk en het bieden van globale connectiviteit.
- Virtuele WAN-hubs van Azure worden geïmplementeerd in zowel Europa-west als Zuid Azië-oost Azure-regio's om connectiviteits hub te bieden voor ExpressRoute en VPN-verbonden apparaten.
- Hubs bieden ook VPN-beëindiging voor zwervende gebruikers via meerdere client typen met behulp van OpenVPN-connectiviteit met het wereld wijde mesh-netwerk, zodat toegang wordt geboden tot niet alleen toepassingen die naar Azure zijn gemigreerd, maar ook op alle resources die on-premises resteren.
- Internet verbinding voor bronnen binnen een virtueel netwerk dat door Azure Virtual WAN wordt verschaft.

Internet connectiviteit voor externe sites wordt ook door Azure Virtual WAN verschaft. Lokale Internet-groepen worden ondersteund via partner integratie voor geoptimaliseerde toegang tot SaaS-services zoals Microsoft 365.

## <a name="migrate-to-virtual-wan"></a>Migreren naar Virtual WAN

In deze sectie worden de verschillende stappen voor het migreren naar Azure Virtual WAN weer gegeven.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Stap 1: door de klant beheerde hub en spoke van één regio

In de volgende afbeelding ziet u een topologie met één regio voor contoso vóór de implementatie van Azure Virtual WAN:

![Topologie van één regio ](./media/migrate-from-hub-spoke-topology/figure1.png)
 **afbeelding 1: hand matige hub en spoke met één regio**

In het kader van de hub-en-spoke-benadering bevat het virtuele netwerk dat door de klant wordt beheerd, meerdere functie blokken:

- Gedeelde services (een algemene functie vereist door meerdere spokes). Voor beeld: contoso maakt gebruik van Windows Server-domein controllers op virtuele machines met IaaS (Infrastructure-as-a-Service).
- IP/route ring firewall services worden door een virtueel netwerk apparaat van derden verschaft, waardoor een spoke-to-spoke Layer-3 IP-route ring mogelijk wordt.
- Services voor binnenkomend/uitgaand verkeer, waaronder Azure-toepassing gateway voor inkomende HTTPS-aanvragen en proxy services van derden die op virtuele machines worden uitgevoerd voor gefilterde uitgaande toegang tot Internet bronnen.
- ExpressRoute en VPN Virtual Network gateway voor connectiviteit met on-premises netwerken.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Stap 2: virtuele WAN-hubs implementeren

Implementeer in elke regio een virtuele WAN-hub. Stel de virtuele WAN-hub in met VPN Gateway-en ExpressRoute-gateway, zoals beschreven in de volgende artikelen:

- [Zelfstudie: Een site-naar-site-verbinding maken met Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Zelfstudie: Een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN moet de standaard-SKU gebruiken om enkele van de verkeers paden die in dit artikel worden weer gegeven, in te scha kelen.

![Virtuele WAN-hubs implementeren ](./media/migrate-from-hub-spoke-topology/figure2.png)
 **afbeelding 2: door de klant beheerde hub-en-spoke-migratie naar virtuele WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Stap 3: externe sites (ExpressRoute en VPN) verbinden met virtuele WAN

Verbind de virtuele WAN-hub met de bestaande ExpressRoute-circuits en stel site-naar-site-Vpn's via internet in op externe filialen.

> [!NOTE]
> Express route-circuits moeten worden bijgewerkt naar het Premium-SKU-type om verbinding te maken met een virtuele WAN-hub.

![Externe sites verbinden met virtuele WAN ](./media/migrate-from-hub-spoke-topology/figure3.png)
 **-afbeelding 3: door de klant beheerde hub en spoke-naar-site-migratie van virtueel WAN**

Op dit moment zullen on-premises netwerk apparatuur routes ontvangen die overeenkomen met de IP-adres ruimte die is toegewezen aan de virtuele door WAN beheerde hub VNet. Met externe VPN-verbindingen in deze fase worden twee paden naar bestaande toepassingen in de spoke Virtual Networks weer geven. Deze apparaten moeten zo worden geconfigureerd dat ze de tunnel blijven gebruiken voor de door de klant beheerde hub om te zorgen voor symmetrische route ring tijdens de overgangs fase.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Stap 4: hybride connectiviteit testen via Virtual WAN

Voordat u de beheerde virtuele WAN-hub voor productie connectiviteit gebruikt, raden we u aan om een virtueel netwerk met test Spaak en een Virtual WAN VNet-verbinding in te stellen. Controleer of de verbindingen met deze test omgeving via ExpressRoute en site-naar-site VPN werken voordat u verdergaat met de volgende stappen.

![Hybride connectiviteit testen via Virtual WAN- ](./media/migrate-from-hub-spoke-topology/figure4.png)
 **afbeelding 4: door de klant beheerde hub en Spaak naar virtuele WAN-migratie**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Stap 5: overgangs verbinding naar virtuele WAN-hub

![Overgangs verbinding met virtuele WAN ](./media/migrate-from-hub-spoke-topology/figure5.png)
 **-hub afbeelding 5: door de klant beheerde hub en Spaak naar Virtual WAN-migratie**

**a**. Verwijder de bestaande peering-verbindingen van spoke Virtual Networks naar de oude door de klant beheerde hub. Toegang tot toepassingen in spoke Virtual Networks is niet beschikbaar totdat de stappen a-c zijn voltooid.

**b**. Verbind de spoke-virtuele netwerken met de virtuele WAN-hub via VNet-verbindingen.

**c**. Verwijder alle door de gebruiker gedefinieerde routes (UDR) die eerder in spoke Virtual Networks zijn gebruikt voor een spoke-to-spoke-communicatie. Dit pad is nu ingeschakeld door de dynamische route ring die beschikbaar is binnen de virtuele WAN-hub.

**d**. Bestaande ExpressRoute-en VPN-gateways in de door de klant beheerde hub worden nu buiten gebruik gesteld om de volgende stap (e) toe te staan.

**e**. Verbind de oude door de klant beheerde hub (virtueel netwerk van de hub) met de virtuele WAN-hub via een nieuwe VNet-verbinding.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Stap 6: de oude hub wordt gedeeld Services-spoke

We hebben ons Azure-netwerk nu opnieuw ontworpen om de virtuele WAN-hub het centrale punt in onze nieuwe topologie te maken.

![De oude hub wordt gedeeld Services ](./media/migrate-from-hub-spoke-topology/figure6.png)
 **-spoke-afbeelding 6: door de klant beheerde hub en Spaak naar Virtual WAN-migratie**

Omdat de virtuele WAN-hub een beheerde entiteit is en de implementatie van aangepaste resources, zoals virtuele machines, niet toestaat, bestaat het deel blok Shared Services nu als een spoke-virtueel netwerk en fungeert hosts als host voor functies zoals Internet binnenkomend via Azure-toepassing gateway of gevirtualiseerde netwerk apparaat. Verkeer tussen de Shared Services-omgeving en de back-end virtuele machines doorvoert nu de virtuele door WAN beheerde hub.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Stap 7: on-premises connectiviteit optimaliseren om virtueel WAN volledig te gebruiken

In deze fase heeft Contoso meestal hun migraties van bedrijfs toepassingen in de Microsoft Cloud voltooid, met slechts enkele oudere toepassingen die resteren binnen de on-premises domein controller.

![On-premises connectiviteit optimaliseren voor het volledig gebruik van virtuele WAN ](./media/migrate-from-hub-spoke-topology/figure7.png)
 **-afbeelding 7: door de klant beheerde hub-en-spoke-verbinding naar virtuele WAN-migratie**

Om gebruik te maken van de volledige functionaliteit van Azure Virtual WAN, besluit contoso om hun oude on-premises VPN-verbindingen buiten gebruik te stellen. Alle vertakkingen die worden gebruikt voor toegang tot hoofd kantoor of DC-netwerken, kunnen het micro soft Global Network door geven met behulp van de ingebouwde Transit routering van Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach is een alternatieve keuze voor klanten die de micro soft-backbone willen gebruiken om hun bestaande persoonlijke Wan's aan te vullen.

## <a name="end-state-architecture-and-traffic-paths"></a>Architectuur van de eind status en verkeers paden

![Architectuur van de eind status en verkeers paden ](./media/migrate-from-hub-spoke-topology/figure8.png)
 **afbeelding: dubbele regio virtueel WAN**

In deze sectie vindt u een overzicht van hoe deze topologie voldoet aan de oorspronkelijke vereisten door te kijken naar een aantal voor beelden van verkeers stromen.

### <a name="path-1"></a>Pad 1

Pad 1 toont de verkeers stroom van een met S2S VPN verbonden vertakking in Azië naar een Azure VNet in de regio Zuid-Azië-oost.

Het verkeer wordt als volgt gerouteerd:

- Azië-vertakking is verbonden via robuuste, beschikbaar S2S-tunnels in Zuid-Azië-oost virtuele WAN-hub.

- Virtuele WAN-hub verkeer routeert lokaal naar verbonden VNet.

![Stroom 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Pad 2

Pad 2 toont de verkeers stroom van de ExpressRoute verbonden Europese hoofd kantoor naar een Azure VNet in de regio Zuid-Azië-oost.

Het verkeer wordt als volgt gerouteerd:

- Euro pees hoofd kantoor is verbonden via een Premium ExpressRoute-circuit in Europa-west virtuele WAN-hub.

- Met Virtual WAN hub-to-hub globale connectiviteit kan verkeer worden overgedragen naar VNet dat is verbonden in de externe regio.

![Stroom 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Pad 3

Pad 3 toont de verkeers stroom van de in Azië aangesloten lokale domein controller die is verbonden met particulier WAN naar een met Europese S2S verbonden Branch.

Het verkeer wordt als volgt gerouteerd:

- De Azië-DC is verbonden met een lokale privé WAN-provider.

- ExpressRoute-circuit lokaal eindigt op particuliere WAN-verbindingen met de Zuid-Azië-oost virtuele WAN-hub.

- Met Virtual WAN hub-to-hub globale connectiviteit kan verkeer worden overgedragen.

![Stroom 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Pad 4

Pad 4 toont de verkeers stroom van een Azure VNet in de Zuid-Azië-oost regio naar een Azure VNet in Europa-west regio.

Het verkeer wordt als volgt gerouteerd:

- Virtual WAN hub-to-hub wereld wijde connectiviteit maakt systeem eigen door Voer van alle verbonden Azure VNets zonder verdere gebruikers configuratie.

![Stroom 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Pad 5

Pad 5 toont de verkeers stroom van zwervende VPN-gebruikers (P2S) naar een Azure-VNet in de Europa-west regio.

Het verkeer wordt als volgt gerouteerd:

- Gebruikers van laptops en mobiele apparaten gebruiken de OpenVPN-Client voor transparante connectiviteit in de P2S VPN-gateway in Europa-west.

- Europa-west virtuele WAN-hub stuurt verkeer lokaal naar het verbonden VNet.

![Stroom 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Beveiliging en beleids beheer via Azure Firewall

Contoso heeft nu de connectiviteit voor alle branches en VNets in overeenstemming met de vereisten die eerder in dit artikel zijn besproken. Om te voldoen aan de vereisten voor beveiligings beheer en netwerk isolatie, moeten ze het verkeer blijven scheiden en registreren via het hub-netwerk. Voorheen werd deze functie uitgevoerd door een virtueel netwerk apparaat (NVA). Contoso wil ook hun bestaande proxy services buiten gebruik stellen en systeem eigen Azure-Services gebruiken voor uitgaande internet filters.

![Beveiligings-en beleids beheer via Azure Firewall ](./media/migrate-from-hub-spoke-topology/security-policy.png)
 **afbeelding: Azure firewall in virtueel WAN (beveiligde virtuele hub)**

De volgende stappen op hoog niveau zijn vereist voor het introduceren van Azure Firewall in de virtuele WAN-hubs om een uniform punt van beleids beheer mogelijk te maken. Zie [Azure firewall Manager](../firewall-manager/index.yml)voor meer informatie over dit proces en het concept van beveiligde virtuele hubs.

1. Azure Firewall beleid maken.
2. Firewall beleid koppelen aan een virtuele WAN-hub in Azure. Met deze stap kan de bestaande virtuele WAN-hub functioneren als een beveiligde virtuele hub en de vereiste Azure Firewall resources worden geïmplementeerd.

> [!NOTE]
> Als de Azure Firewall is geïmplementeerd in een standaard virtuele WAN-hub (SKU: Standard): V2V, B2V, V2I en B2I FW-beleid worden alleen afgedwongen op het verkeer dat afkomstig is van de VNets en de vertakkingen die zijn verbonden met de specifieke hub waarop de Azure FW is geïmplementeerd (beveiligde hub). Verkeer dat afkomstig is van externe VNets en vertakkingen die zijn gekoppeld aan andere virtuele WAN-hubs in hetzelfde virtuele WAN, wordt niet ' firewalled ', ook al zijn de externe vertakkingen en VNet onderling verbonden via Virtual WAN hub naar hub-koppelingen. Ondersteuning voor cross-hub-firewalls bevindt zich op het schema voor virtuele WAN-en firewall beheer van Azure.

De volgende paden tonen de connectiviteits paden die zijn ingeschakeld met behulp van beveiligde virtuele hubs van Azure:

### <a name="path-6"></a>Pad 6

In pad 6 wordt de stroom van beveiligde verkeer tussen VNets binnen dezelfde regio weer gegeven.

Het verkeer wordt als volgt gerouteerd:

- Virtuele netwerken die zijn verbonden met dezelfde beveiligde virtuele hub, sturen nu verkeer naar via de Azure Firewall.

- Azure Firewall kunt beleid Toep assen op deze stromen.

![Stroom 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Pad 7

Path 7 toont de verkeers stroom van een Azure VNet naar het internet of de beveiligings service van derden.

Het verkeer wordt als volgt gerouteerd:

- Virtuele netwerken die zijn verbonden met de beveiligde virtuele hub, kunnen verkeer verzenden naar openbaar, bestemmingen op internet met behulp van de beveiligde hub als centraal punt van Internet toegang.

- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligings service van derden voor inspectie.

![Stroom 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Pad 8

In pad 8 wordt de verkeers stroom van de beveiligings service van de vertakking naar Internet of van derden weer gegeven.

Het verkeer wordt als volgt gerouteerd:

- Vertakkingen die zijn verbonden met de beveiligde virtuele hub kunnen verkeer verzenden naar open bare bestemmingen op internet met behulp van de beveiligde hub als centraal punt van Internet toegang.

- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligings service van derden voor inspectie.

![Stroom 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Virtual WAN](virtual-wan-about.md)
