---
title: Overwegingen voor netwerktopologie voor Azure AD-toepassingsproxy
description: Dekt overwegingen op het gebied van netwerktopologie bij het gebruik van Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295139"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Overwegingen voor netwerktopologie bij het gebruik van Azure Active Directory Application Proxy

In dit artikel worden overwegingen voor netwerktopologie uitgelegd bij het gebruik van Azure Active Directory (Azure AD) Application Proxy voor het publiceren en openen van uw toepassingen op afstand.

## <a name="traffic-flow"></a>Verkeersstroom

Wanneer een toepassing wordt gepubliceerd via Azure AD Application Proxy, stroomt het verkeer van de gebruikers naar de toepassingen via drie verbindingen:

1. De gebruiker maakt verbinding met het openbare eindpunt van de Azure AD Application Proxy service op Azure
1. De application proxy-service maakt verbinding met de toepassingsproxyconnector
1. De toepassingsproxyconnector maakt verbinding met de doeltoepassing

![Diagram met verkeersstroom van gebruiker naar doeltoepassing](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Tenantlocatie en Application Proxy-service

Wanneer u zich aanmeldt voor een Azure AD-tenant, wordt de regio van uw tenant bepaald door het land/de regio die u opgeeft. Wanneer u Application Proxy inschakelt, worden de instanties voor de toepassingsproxy voor uw tenant gekozen of gemaakt in dezelfde regio als uw Azure AD-tenant of het dichtstbijzijnde gebied.

Als het land of de regio van uw Azure AD-tenant bijvoorbeeld het Verenigd Koninkrijk is, gebruiken al uw Application Proxy-connectors service-exemplaren in Europese datacenters. Wanneer uw gebruikers toegang krijgen tot gepubliceerde toepassingen, gaat hun verkeer via de instanties van de Application Proxy-service op deze locatie.

## <a name="considerations-for-reducing-latency"></a>Overwegingen voor het verminderen van latentie

Alle proxy-oplossingen introduceren latentie in uw netwerkverbinding. Het maakt niet uit welke proxy of VPN-oplossing u kiest als uw oplossing voor externe toegang, het bevat altijd een set servers waarmee de verbinding met uw bedrijfsnetwerk mogelijk is.

Organisaties nemen doorgaans servereindpunten op in hun perimeternetwerk. Met Azure AD Application Proxy stroomt het verkeer echter door de proxyservice in de cloud terwijl de connectors zich op uw bedrijfsnetwerk bevinden. Er is geen perimeternetwerk vereist.

De volgende secties bevatten aanvullende suggesties om de latentie nog verder te verminderen. 

### <a name="connector-placement"></a>Plaatsing van connectoren

Application Proxy kiest de locatie van instanties voor u, op basis van uw tenantlocatie. U echter beslissen waar u de connector wilt installeren, zodat u de latentiekenmerken van uw netwerkverkeer definiëren.

Stel bij het instellen van de application proxy-service de volgende vragen:

- Waar bevindt de app zich?
- Waar bevinden de meeste gebruikers zich bij de app?
- Waar bevindt zich de instantie Application Proxy?
- Heeft u al een speciale netwerkverbinding met Azure-datacenters ingesteld, zoals Azure ExpressRoute of een vergelijkbare VPN?

De connector moet communiceren met zowel Azure als uw toepassingen (stap 2 en 3 in het diagram Verkeersstroom), zodat de plaatsing van de connector van invloed is op de latentie van deze twee verbindingen. Houd bij de evaluatie van de plaatsing van de connector rekening met de volgende punten:

- Als u Kerberos constrained delegation (KCD) wilt gebruiken voor eenmalige aanmelding, heeft de connector een zichtlijn naar een datacenter nodig. Bovendien moet de connectorserver worden samengevoegd.  
- Installeer bij twijfel de connector dichter bij de toepassing.

### <a name="general-approach-to-minimize-latency"></a>Algemene aanpak om latentie te minimaliseren

U de latentie van het end-to-end-verkeer minimaliseren door elke netwerkverbinding te optimaliseren. Elke verbinding kan worden geoptimaliseerd door:

- Het verminderen van de afstand tussen de twee uiteinden van de hop.
- Het kiezen van het juiste netwerk te doorkruisen. Het doorkruisen van een privénetwerk in plaats van het openbare internet kan bijvoorbeeld sneller zijn, als gevolg van speciale links.

Als u een speciale VPN- of ExpressRoute-koppeling hebt tussen Azure en uw bedrijfsnetwerk, u dat gebruiken.

## <a name="focus-your-optimization-strategy"></a>Richt uw optimalisatiestrategie

Er is weinig dat u doen om de verbinding tussen uw gebruikers en de Application Proxy-service te beheren. Gebruikers hebben toegang tot uw apps via een thuisnetwerk, een coffeeshop of een ander land/regio. In plaats daarvan u de verbindingen optimaliseren van de service Application Proxy naar de connectors voor toepassingsproxy's met de apps. Overweeg het opnemen van de volgende patronen in uw omgeving.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Patroon 1: Zet de connector dicht bij de toepassing

Plaats de connector dicht bij de doeltoepassing in het klantennetwerk. Deze configuratie minimaliseert stap 3 in het topografiediagram, omdat de connector en toepassing dicht zijn.

Als uw connector een zichtlijn naar de domeincontroller nodig heeft, is dit patroon voordelig. De meeste van onze klanten gebruiken dit patroon, omdat het goed werkt voor de meeste scenario's. Dit patroon kan ook worden gecombineerd met patroon 2 om het verkeer tussen de service en de connector te optimaliseren.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Patroon 2: Profiteer van ExpressRoute met Microsoft-peering

Als u ExpressRoute hebt ingesteld met Microsoft-peering, u de snellere ExpressRoute-verbinding gebruiken voor verkeer tussen Application Proxy en de connector. De connector bevindt zich nog steeds in uw netwerk, dicht bij de app.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Patroon 3: Profiteer van ExpressRoute met private peering

Als u een speciale VPN of ExpressRoute hebt ingesteld met privé-peering tussen Azure en uw bedrijfsnetwerk, hebt u een andere optie. In deze configuratie wordt het virtuele netwerk in Azure doorgaans beschouwd als een uitbreiding van het bedrijfsnetwerk. U de connector dus installeren in het Azure-datacenter en toch voldoen aan de vereisten voor lage latentie van de connector-naar-app-verbinding.

Latentie komt niet in het gedrang omdat er verkeer over een speciale verbinding stroomt. U krijgt ook een verbeterde latentie van Application Proxy service-to-connector omdat de connector is geïnstalleerd in een Azure-datacenter in de buurt van uw Azure AD-tenantlocatie.

![Diagram met connector die is geïnstalleerd in een Azure-datacenter](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andere benaderingen

Hoewel de focus van dit artikel is connector plaatsing, u ook de plaatsing van de toepassing te krijgen betere latentie kenmerken.

Steeds vaker verplaatsen organisaties hun netwerken naar gehoste omgevingen. Dit stelt hen in staat om hun apps te plaatsen in een gehoste omgeving die ook deel uitmaakt van hun bedrijfsnetwerk en nog steeds deel uitmaakt van het domein. In dit geval kunnen de patronen die in de voorgaande secties worden besproken, worden toegepast op de nieuwe toepassingslocatie. Zie [Azure AD Domain Services](../../active-directory-domain-services/overview.md)als u deze optie overweegt.

Overweeg bovendien uw connectors te organiseren met behulp van [connectorgroepen](application-proxy-connector-groups.md) om apps te targeten die zich op verschillende locaties en netwerken bevinden.

## <a name="common-use-cases"></a>Algemene scenario’s

In deze sectie doorlopen we een paar veelvoorkomende scenario's. Ga ervan uit dat de Azure AD-tenant (en dus het eindpunt van de proxyservice) zich in de Verenigde Staten (VS) bevindt. De overwegingen die in deze gebruiksgevallen worden besproken, gelden ook voor andere regio's over de hele wereld.

Voor deze scenario's noemen we elke verbinding een "hop" en nummeren ze voor een gemakkelijkere discussie:

- **Hop 1**: Gebruiker naar de application proxy service
- **Hop 2**: Application Proxy-service naar de application proxy-connector
- **Hop 3**: Application Proxy-connector voor de doeltoepassing 

### <a name="use-case-1"></a>Gebruik case 1

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de VS, met gebruikers in dezelfde regio. Er bestaat geen ExpressRoute of VPN tussen het Azure-datacenter en het bedrijfsnetwerk.

**Aanbeveling:** Volg patroon 1, uitgelegd in de vorige sectie. Voor een betere latentie u overwegen expressroute te gebruiken, indien nodig.

Dit is een eenvoudig patroon. U optimaliseert hop 3 door de connector in de buurt van de app te plaatsen. Dit is ook een logische keuze, omdat de connector meestal is geïnstalleerd met lijn van het zicht naar de app en naar het datacenter om KCD-bewerkingen uit te voeren.

![Diagram waarin gebruikers, proxy, connector en app zijn allemaal in de VS](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Gebruik case 2

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de VS, met gebruikers verspreid over de hele wereld. Er bestaat geen ExpressRoute of VPN tussen het Azure-datacenter en het bedrijfsnetwerk.

**Aanbeveling:** Volg patroon 1, uitgelegd in de vorige sectie.

Nogmaals, het algemene patroon is om hop 3 te optimaliseren, waarbij u de connector in de buurt van de app plaatst. Hop 3 is niet typisch duur, als het allemaal binnen dezelfde regio. Hop 1 kan echter duurder zijn, afhankelijk van waar de gebruiker zich bevindt, omdat gebruikers over de hele wereld toegang moeten krijgen tot de instantie Application Proxy in de VS. Het is vermeldenswaard dat elke proxy-oplossing vergelijkbare kenmerken heeft met betrekking tot gebruikers die wereldwijd worden verspreid.

![Gebruikers zijn wereldwijd verspreid, maar al het andere is in de VS](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Gebruik case 3

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de VS. ExpressRoute met Microsoft peering bestaat tussen Azure en het bedrijfsnetwerk.

**Aanbeveling:** Volg patronen 1 en 2, uitgelegd in de vorige sectie.

Plaats eerst de connector zo dicht mogelijk bij de app. Vervolgens gebruikt het systeem automatisch ExpressRoute voor hop 2.

Als de ExpressRoute-koppeling Microsoft-peering gebruikt, stroomt het verkeer tussen de proxy en de connector over die koppeling. Hop 2 heeft de latentie geoptimaliseerd.

![Diagram met ExpressRoute tussen de proxy en de connector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Gebruik case 4

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de VS. ExpressRoute met private peering bestaat tussen Azure en het bedrijfsnetwerk.

**Aanbeveling:** Volg patroon 3, uitgelegd in de vorige sectie.

Plaats de connector in het Azure-datacenter dat is verbonden met het bedrijfsnetwerk via privépeering via ExpressRoute.

De connector kan in het Azure-datacenter worden geplaatst. Aangezien de connector nog steeds een zichtlijn heeft naar de toepassing en het datacenter via het privénetwerk, blijft hop 3 geoptimaliseerd. Daarnaast wordt hop 2 verder geoptimaliseerd.

![Connector in Azure-datacenter, ExpressRoute tussen connector en app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Gebruik case 5

**Scenario:** De app bevindt zich in het netwerk van een organisatie in Europa, met de instantie Application Proxy en de meeste gebruikers in de VS.

**Aanbeveling:** Plaats de connector in de buurt van de app. Omdat Amerikaanse gebruikers toegang hebben tot een instantie Application Proxy die zich toevallig in dezelfde regio bevindt, is hop 1 niet te duur. Hop 3 is geoptimaliseerd. Overweeg ExpressRoute te gebruiken om hop 2 te optimaliseren.

![Diagram toont gebruikers en proxy in de VS, connector en app in Europa](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

U ook overwegen om in deze situatie een andere variant te gebruiken. Als de meeste gebruikers in de organisatie zich in de VS bevinden, is de kans groot dat uw netwerk zich ook uitstrekt tot de VS. Plaats de connector in de VS en gebruik de speciale interne bedrijfsnetwerklijn voor de toepassing in Europa. Op deze manier worden hop 2 en 3 geoptimaliseerd.

![Diagram toont gebruikers, proxy en connector in de VS, app in Europa](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingsproxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)
