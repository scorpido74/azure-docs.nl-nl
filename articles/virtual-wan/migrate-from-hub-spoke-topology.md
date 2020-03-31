---
title: 'Architectuur: migreren naar Azure Virtual WAN'
description: Meer informatie over het migreren naar Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062946"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migreren naar Azure Virtual WAN

Azure Virtual WAN stelt bedrijven in staat hun wereldwijde connectiviteit te vereenvoudigen om te profiteren van de schaal van het wereldwijde netwerk van Microsoft. Dit artikel bevat technische details voor bedrijven die willen migreren van een bestaande door klanten beheerde hub-en-spoke topologie, naar een ontwerp dat gebruikmaakt van door Microsoft beheerde Virtuele WAN-hubs.

For information about the benefits that Azure Virtual WAN enables for enterprises adopting a cloud-centric modern enterprise global network, see [Global transit network architecture and Virtual WAN](virtual-wan-global-transit-network-architecture.md).

![hub en](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
spaak**Figuur: Azure Virtual WAN**

Het Hub-and-spoke-connectiviteitsmodel (Azure Virtual Datacenter) is door duizenden van onze klanten overgenomen om gebruik te maken van het standaard transitive routing-gedrag van Azure Networking om eenvoudige en schaalbare cloudnetwerken te bouwen. Azure Virtual WAN bouwt voort op deze concepten en introduceert nieuwe mogelijkheden die wereldwijde connectiviteitstopologieën mogelijk maken, niet alleen tussen on-premises locaties en Azure, maar ook klanten in staat stellen de schaal van het Microsoft-netwerk te benutten om hun bestaande wereldwijde netwerken.

In dit artikel ziet u hoe u een bestaande hybride omgeving migreert naar Virtual WAN.

## <a name="scenario"></a>Scenario

Contoso is een wereldwijde financiële organisatie met kantoren in zowel Europa als Azië. Ze zijn van plan om hun bestaande toepassingen te verplaatsen van een on-premises datacenter naar Azure en hebben een basisontwerp gebouwd op basis van de VDC-architectuur, inclusief regionale door de klant beheerde virtuele hubnetwerken voor hybride connectiviteit. Als onderdeel van de overstap naar cloudgebaseerde technologieën heeft het netwerkteam de opdracht gekregen om ervoor te zorgen dat hun connectiviteit is geoptimaliseerd voor het bedrijf dat vooruit gaat.

De volgende afbeelding toont een weergave op hoog niveau van het bestaande wereldwijde netwerk, inclusief connectiviteit met meerdere Azure-regio's.

![Contoso bestaande netwerktopologie](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**Figuur: Contoso bestaande netwerktopologie**

De volgende punten zijn te begrijpen uit de bestaande netwerktopologie:

- Een hub-and-spoke topologie wordt gebruikt in meerdere regio's, waaronder ExpressRoute Premium circuits voor connectiviteit terug naar een gemeenschappelijke particuliere WAN.

- Sommige van deze sites hebben ook VPN-tunnels rechtstreeks naar Azure om toepassingen te bereiken die worden gehost in de Microsoft-cloud.

## <a name="requirements"></a>Vereisten

Het netwerkteam is belast met het leveren van een wereldwijd netwerkmodel dat de Contoso-migratie naar de cloud kan ondersteunen en moet optimaliseren op het gebied van kosten, schaal en prestaties. Samengevat moet aan de volgende eisen worden voldaan:

- Bied zowel hoofdkwartaal (HQ) als filialen een geoptimaliseerd pad naar cloudgehoste applicaties.
- Verwijder de afhankelijkheid van bestaande on-premises datacenters (DC) voor VPN-beëindiging met behoud van de volgende connectiviteitspaden:
  - **Branch -to- VNet**: VPN connected offices moeten toegang hebben tot applicaties die zijn gemigreerd naar de cloud in de lokale Azure-regio.
  - **Branch -to- Hub -to- Hub -to- VNet:** VPN connected offices must be able to applications migrated to the cloud in the remote Azure region.
  - **Branch -to- branch**: Regionale VPN verbonden kantoren moeten met elkaar kunnen communiceren en ExpressRoute connected HQ/DC sites.
  - **Branch -to- Hub -to- Hub-to-- branch:** Wereldwijd gescheiden VPN verbonden kantoren moeten met elkaar kunnen communiceren en alle expressroute aangesloten HQ/DC-sites.
  - **Branch -to- Internet**: Verbonden sites moeten kunnen communiceren met het internet. Dit verkeer moet worden gefilterd en geregistreerd.
  - **VNet -to- VNet**: Spaak virtuele netwerken in dezelfde regio moeten met elkaar kunnen communiceren.
  - **VNet -to- Hub -to- Hub -to- VNet**: Spoke virtuele netwerken in de verschillende regio's moeten met elkaar kunnen communiceren.
- Bieden de mogelijkheid voor Contoso roaming gebruikers (laptop en telefoon) om toegang te krijgen tot bedrijfsbronnen, terwijl niet op het bedrijfsnetwerk.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN-architectuur

De volgende afbeelding toont een weergave op hoog niveau van de bijgewerkte doeltopologie met Azure Virtual WAN om te voldoen aan de vereisten die in de vorige sectie zijn beschreven.

![Contoso virtuele](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
WAN-architectuur**Figuur: Azure Virtual WAN-architectuur**

Samenvatting:

- HQ in Europa blijft ExpressRoute aangesloten, Europe on-premises DC zijn volledig gemigreerd naar Azure en nu buiten bedrijf gesteld.
- Asia DC en HQ blijven verbonden met Private WAN. Azure Virtual WAN wordt nu gebruikt om het lokale netwerk van vervoerders uit te breiden en wereldwijde connectiviteit te bieden.
- Azure Virtual WAN-hubs die zijn geïmplementeerd in zowel West-Europa als Azure-regio's in Zuidoost-Azië om connectiviteitshub te bieden voor ExpressRoute- en VPN-verbonden apparaten.
- Hubs bieden ook VPN-beëindiging voor zwervende gebruikers in meerdere clienttypen met OpenVPN-connectiviteit met het wereldwijde mesh-netwerk, waardoor toegang wordt tot niet alleen toepassingen die zijn gemigreerd naar Azure, maar ook alle bronnen die on-premises blijven.
- Internetverbinding voor bronnen binnen een virtueel netwerk van Azure Virtual WAN.

Internetverbinding voor externe sites die ook worden geleverd door Azure Virtual WAN. Lokale internetbreakout ondersteund via partnerintegratie voor geoptimaliseerde toegang tot SaaS-services zoals Office 365.

## <a name="migrate-to-virtual-wan"></a>Migreren naar Virtual WAN

In deze sectie worden de verschillende stappen weergegeven voor het migreren naar Azure Virtual WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Stap 1: VDC hub-and-spoke single region

Bekijk de architectuur. De volgende afbeelding toont één regiotopologie voor Contoso voorafgaand aan de implementatie van Azure Virtual WAN:

![Single region](./media/migrate-from-hub-spoke-topology/figure1.png)
topologie**Figuur 1: VDC hub-and-spoke single region**

In overeenstemming met de Virtual Data Center (VDC) aanpak, de klant beheerde hub virtuele netwerk bevat verschillende functie blokken:

- Gedeelde services (elke gemeenschappelijke functie vereist door meerdere spaken). Voorbeeld: Contoso gebruikt Windows Server-domeincontrollers op virtuele machines (Infrastructure-as-a-Service).
- IP/Routing firewall services worden geleverd door een virtueel toestel van derden, waardoor spoke-to-spoke layer-3 IP routing mogelijk wordt.
- Internet-in-gress/uitgangsservices, waaronder Azure Application Gateway voor binnenkomende HTTPS-aanvragen en proxyservices van derden die op virtuele machines worden uitgevoerd voor gefilterde uitgaande toegang tot internetbronnen.
- ExpressRoute en VPN virtuele netwerk gateway voor connectiviteit met on-premises netwerken.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Stap 2: Virtuele WAN-hubs implementeren

Implementeer een virtuele WAN-hub in elke regio. Stel de Virtual WAN-hub in met VPN Gateway en ExpressRoute Gateway zoals beschreven in de volgende artikelen:

- [Zelfstudie: Een site-naar-site-verbinding maken met Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Zelfstudie: Een ExpressRoute-koppeling maken met Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN moet de Standaard SKU gebruiken om een aantal verkeerspaden in dit artikel in te schakelen.

![Virtual](./media/migrate-from-hub-spoke-topology/figure2.png)
**WAN-hubs figuur 2: VDC-hub-and-spoke implementeren met virtuele WAN-migratie**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Stap 3: Externe sites (ExpressRoute en VPN) verbinden met Virtual WAN

Sluit de Virtual WAN-hub aan op de bestaande ExpressRoute-circuits en stel Site-to-site VPN's via internet in op externe branches.

> [!NOTE]
> Express Routes Circuits moeten worden geüpgraded naar Premium SKU-type om verbinding te maken met de Virtuele WAN-hub.

![Externe sites verbinden](./media/migrate-from-hub-spoke-topology/figure3.png)
met Virtual WAN**Figuur 3: VDC-hub-and-spoke met Virtuele WAN-migratie**

Op dit moment zal on-premises netwerkapparatuur routes beginnen te ontvangen die de IP-adresruimte weerspiegelen die is toegewezen aan de virtuele WAN-beheerde hub VNet. Remote VPN-connected branches in dit stadium ziet twee paden naar bestaande toepassingen in de spaak virtuele netwerken. Deze apparaten moeten zijn geconfigureerd om de tunnel naar de VDC-hub te blijven gebruiken om symmetrische routering tijdens de overgangsfase te garanderen.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Stap 4: Test hybride connectiviteit via Virtual WAN

Voordat u de beheerde Virtual WAN-hub gebruikt voor productieconnectiviteit, raden we u aan een testspaakvirtueel netwerk en Virtual WAN VNet-verbinding in te stellen. Valideer dat verbindingen met deze testomgeving werken via ExpressRoute en Site to Site VPN voordat u verdergaat met de volgende stappen.

![Test hybride connectiviteit via](./media/migrate-from-hub-spoke-topology/figure4.png)
Virtual WAN**Figuur 4: VDC hub-and-spoke met Virtual WAN-migratie**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Stap 5: Overgang connectiviteit naar virtuele WAN hub

![Overgangsconnectiviteit naar Virtuele](./media/migrate-from-hub-spoke-topology/figure5.png)
**WAN-hub Figuur 5: VDC-hub-and-spoke met virtuele WAN-migratie**

**a**. Verwijder de bestaande peeringverbindingen van Spoke virtuele netwerken naar de oude VDC-hub. Toegang tot toepassingen in gesproken virtuele netwerken is niet beschikbaar totdat de stappen a-c zijn voltooid.

**b**. Sluit de gesproken virtuele netwerken aan op de Virtual WAN-hub via VNet-verbindingen.

**c**. Verwijder alle door de gebruiker gedefinieerde routes (UDR) die eerder binnen gesproken virtuele netwerken werden gebruikt voor spraak-tot-spaakcommunicatie. Dit pad is nu ingeschakeld door dynamische routering beschikbaar binnen de Virtual WAN-hub.

**d**. Bestaande ExpressRoute- en VPN-gateways in de VDC-hub worden nu buiten gebruik gesteld om de volgende stap (e) mogelijk te maken.

**e**. Sluit de oude VDC-hub (hub virtueel netwerk) aan op de Virtual WAN-hub via een nieuwe VNet-verbinding.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Stap 6: Oude hub wordt gedeelde services

We hebben ons Azure-netwerk nu opnieuw ontworpen om van de Virtual WAN-hub het centrale punt in onze nieuwe topologie te maken.

![Oude hub wordt](./media/migrate-from-hub-spoke-topology/figure6.png)
Shared Services sprak**Figuur 6: VDC hub-and-spoke to Virtual WAN migration**

Omdat de Virtuele WAN-hub een beheerde entiteit is en geen implementatie van aangepaste resources zoals virtuele machines toestaat, bestaat het blok voor gedeelde services nu als een gesproken virtueel netwerk en host functies zoals internetbinnendringen via Azure Application Gateway of netwerk gevirtualiseerd toestel. Verkeer tussen de gedeelde services-omgeving en backend virtuele machines rijdt nu door de Virtual WAN-managed hub.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Stap 7: On-premises connectiviteit optimaliseren om Virtual WAN volledig te benutten

In dit stadium heeft Contoso meestal hun migraties van zakelijke toepassingen voltooid in de Microsoft Cloud, met slechts een paar oudere toepassingen die nog binnen de on-premises DC blijven.

![On-premises connectiviteit optimaliseren om Virtual](./media/migrate-from-hub-spoke-topology/figure7.png)
WAN Figure 7 volledig te**gebruiken: VDC hub-and-spoke met Virtual WAN-migratie**

Om gebruik te maken van de volledige functionaliteit van Azure Virtual WAN, besluit Contoso om hun verouderde on-premises VPN-verbindingen te ontmantelen. Alle branches die toegang blijven krijgen tot HQ- of DC-netwerken kunnen het wereldwijde Microsoft-netwerk doorsturen met behulp van de ingebouwde transitroutering van Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach is een alternatieve keuze voor klanten die gebruik willen maken van de Microsoft-backbone als aanvulling op hun bestaande privé-WAN's.

## <a name="end-state-architecture-and-traffic-paths"></a>Architectuur en verkeerspaden in de eindtoestand

![Einde-state architectuur en](./media/migrate-from-hub-spoke-topology/figure8.png)
verkeerspaden**Figuur: Dual regio Virtual WAN**

In dit gedeelte vindt u een overzicht van hoe deze topologie aan de oorspronkelijke vereisten voldoet door te kijken naar enkele voorbeeldverkeersstromen.

### <a name="path-1"></a>Pad 1

Pad 1 toont de verkeersstroom van een S2S VPN-verbinding in Azië naar een Azure VNet in de regio Zuidoost-Azië.

Het verkeer wordt als volgt omgeleid:

- De Aziatische tak is verbonden via veerkrachtige S2S BGP-tunnels in de Virtual WAN-hub in Zuidoost-Azië.

- Asia Virtual WAN-hub leidt het verkeer lokaal naar verbonden VNet.

![Stroom 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Pad 2

Pad 2 toont de verkeersstroom van de ExpressRoute verbonden Europese HQ naar een Azure VNet in de regio Zuidoost-Azië.

Het verkeer wordt als volgt omgeleid:

- Europese HQ is verbonden via premium ExpressRoute circuit in West-Europa Virtual WAN hub.

- Virtuele WAN hub-to-hub wereldwijde connectiviteit maakt doorvoer van verkeer naar VNet aangesloten in afgelegen regio.

![Stroom 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Pad 3

Pad 3 toont de verkeersstroom vanuit het a-premises DC in Azië dat is aangesloten op Private WAN met een Europese S2S-verbonden branch.

Het verkeer wordt als volgt omgeleid:

- Asia DC is aangesloten op de lokale Private WAN-carrier.

- ExpressRoute-circuit wordt lokaal beëindigd in Private WAN maakt verbinding met de Virtual WAN-hub in Zuidoost-Azië.

- Virtuele WAN hub-to-hub wereldwijde connectiviteit maakt doorvoer van het verkeer mogelijk.

![Stroom 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Pad 4

Pad 4 toont de verkeersstroom van een Azure VNet in zuidoost-Azië naar een Azure VNet in West-Europa.

Het verkeer wordt als volgt omgeleid:

- Virtuele WAN hub-to-hub wereldwijde connectiviteit maakt native transit van alle aangesloten Azure VNets zonder verdere gebruiker config.

![Stroom 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Pad 5

Pad 5 toont de verkeersstroom van zwervende VPN-gebruikers (P2S) naar een Azure VNet in de regio West-Europa.

Het verkeer wordt als volgt omgeleid:

- Gebruikers van laptops en mobiele apparaten gebruiken de OpenVPN-client voor transparante connectiviteit met de P2S VPN-gateway in West-Europa.

- West Europe Virtual WAN hub leidt het verkeer lokaal naar aangesloten VNet.

![Stroom 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Beveiliging en beleidsbeheer via Azure Firewall

Contoso heeft nu de connectiviteit tussen alle branches en VNets gevalideerd in overeenstemming met de eerder in dit artikel besproken eisen. Om te voldoen aan hun vereisten voor beveiligingscontrole en netwerkisolatie, moeten ze doorgaan met het scheiden en registreren van verkeer via het hubnetwerk. Voorheen werd deze functie uitgevoerd door een network virtual appliance (NVA). Contoso wil ook hun bestaande proxyservices ontmantelen en native Azure-services gebruiken voor uitgaande internetfiltering.

![Beveiliging en beleidsbeheer](./media/migrate-from-hub-spoke-topology/security-policy.png)
via Azure Firewall**Figure: Azure Firewall in Virtual WAN (Secured Virtual hub)**

De volgende stappen op hoog niveau zijn vereist om Azure Firewall in de Virtuele WAN-hubs te introduceren om een uniform punt van beleidsbeheer mogelijk te maken. Zie [Azure Firewall Manager](../firewall-manager/index.yml)voor meer informatie over dit proces en het concept van Secure Virtual Hubs.

1. Azure Firewall-beleid maken.
2. Firewallbeleid koppelen aan azure virtual wan-hub. Met deze stap kan de bestaande Virtuele WAN-hub functioneren als een beveiligde virtuele hub en worden de vereiste Azure Firewall-resources geïmplementeerd.

> [!NOTE]
> Als de Azure Firewall is geïmplementeerd in een Standard Virtual WAN-hub (SKU : Standard): V2V-, B2V-, V2I- en B2I FW-beleid worden alleen afgedwongen op het verkeer dat afkomstig is van de VNets en Branches die zijn aangesloten op de specifieke hub waar de Azure FW is geïmplementeerd (Secured Hub). Verkeer afkomstig van externe VNets en Branches die zijn gekoppeld aan andere Virtuele WAN-hubs in dezelfde Virtuele WAN-hubs, wordt niet "firewalled", ook al zijn de externe branches en VNet met elkaar verbonden via virtuele WAN-hub naar hubkoppelingen. Ondersteuning voor cross-hub firewalling bevindt zich op de roadmap Azure Virtual WAN en Firewall Manager.

De volgende paden tonen de verbindingspaden die zijn ingeschakeld met behulp van door Azure beveiligde virtuele hubs:

### <a name="path-6"></a>Pad 6

Pad 6 toont een veilige verkeersstroom tussen VNets binnen dezelfde regio.

Het verkeer wordt als volgt omgeleid:

- Virtuele netwerken die zijn verbonden met dezelfde Beveiligde virtuele hub, leiden het verkeer nu via de Azure Firewall.

- Azure Firewall kan beleid toepassen op deze stromen.

![Stroom 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Pad 7

Pad 7 toont de verkeersstroom van een Azure VNet naar het internet of de beveiligingsservice van derden.

Het verkeer wordt als volgt omgeleid:

- Virtuele netwerken die zijn verbonden met de Secure Virtual Hub kunnen verkeer naar openbare, bestemmingen op het internet verzenden, met behulp van de Secure Hub als centraal punt van internettoegang.

- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligingsservice van derden voor inspectie.

![Stroom 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Pad 8

Pad 8 toont de verkeersstroom van branch-to-Internet of beveiligingsservice van derden.

Het verkeer wordt als volgt omgeleid:

- Branches die zijn aangesloten op de Secure Virtual Hub kunnen verkeer naar openbare bestemmingen op internet verzenden met behulp van de Secure Hub als centraal toegangspunt voor internet.

- Dit verkeer kan lokaal worden gefilterd met Azure Firewall FQDN-regels of worden verzonden naar een beveiligingsservice van derden voor inspectie.

![Stroom 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Virtual WAN](virtual-wan-about.md)
