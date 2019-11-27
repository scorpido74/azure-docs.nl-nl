---
title: Aandachtspunten voor de netwerk topologie voor Azure AD-toepassingsproxy
description: Bevat informatie over aandachtspunten voor topologie van netwerk bij het gebruik van Azure AD-toepassingsproxy.
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
ms.openlocfilehash: 075b2c92168afe0c366608266c38b14394b73cff
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275491"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Netwerk-topologie overwegingen bij het gebruik van Azure Active Directory-toepassingsproxy

In dit artikel wordt uitgelegd aandachtspunten voor topologie van netwerk bij het gebruik van de toepassingsproxy van Azure Active Directory (Azure AD) voor het publiceren en op afstand toegang krijgen tot uw toepassingen.

## <a name="traffic-flow"></a>Verkeersstroom

Wanneer een toepassing wordt gepubliceerd via Azure AD-toepassingsproxy, wordt verkeer van de gebruikers naar de toepassingen stromen via drie verbindingen:

1. De gebruiker verbinding maakt met het openbare eindpunt van Azure AD Application Proxy-service op Azure
1. De Application Proxy-service maakt verbinding met de Application Proxy-connector
1. De Application Proxy-connector maakt verbinding met de doeltoepassing

![Diagram van netwerkverkeer van gebruiker aan de doeltoepassing](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Tenantlocatie en de service voor toepassingsproxy

Wanneer u zich aanmeldt voor een Azure AD-Tenant, wordt de regio van uw Tenant bepaald door het land/de regio die u opgeeft. Wanneer u toepassingsproxy hebt ingeschakeld, worden de exemplaren van de toepassingsproxy voor uw tenant gekozen of gemaakt in dezelfde regio als uw Azure AD-tenant, of de dichtstbijzijnde regio.

Bijvoorbeeld, als het land of regio van uw Azure AD-tenant het Verenigd Koninkrijk is, gebruiken alle uw Application Proxy connectors service-exemplaren in EU-datacenters. Wanneer uw gebruikers toegang tot toepassingen gepubliceerde, hun verkeer wordt gerouteerd via de Application Proxy-service-exemplaren in deze locatie.

## <a name="considerations-for-reducing-latency"></a>Overwegingen voor de latentie wordt verminderd

Alle proxyoplossingen introduceren latentie in uw netwerkverbinding. Ongeacht welke proxy- of VPN-oplossing u ook als uw oplossing voor externe toegang kiest, bevat deze altijd een set servers voor het inschakelen van de verbinding met binnen uw bedrijfsnetwerk.

Organisaties bevatten doorgaans servereindpunten in het perimeternetwerk. Met Azure AD-toepassingsproxy, echter stroomt verkeer via de proxy-service in de cloud terwijl de connectoren bevinden zich op uw bedrijfsnetwerk. Er is geen perimeternetwerk is vereist.

De volgende secties bevatten aanvullende suggesties hebt, kunt u Verminder de latentie nog verder. 

### <a name="connector-placement"></a>Plaatsing van de connector

Toepassingsproxy kiest de locatie van exemplaren voor u, op basis van de tenantlocatie van uw. Echter, krijgt u om te bepalen waar u de connector installeert zodat u voor het definiëren van de kenmerken van de latentie van uw netwerkverkeer.

Bij het instellen van de service voor toepassingsproxy, kunt u de volgende vragen:

- Waar kan ik de app vinden?
- Waar bevinden de meeste gebruikers die toegang de app tot zich?
- Waar is de Application Proxy-instantie zich?
- Hebt u al een speciaal netwerkverbinding met Azure-datacenters instellen, zoals Azure ExpressRoute of een vergelijkbare VPN?

De connector heeft om te communiceren met zowel Azure als uw toepassingen (stap 2 en 3 in het diagram van de stroom verkeer), zodat de plaatsing van de connector is van invloed op de latentie van deze twee verbindingen. Bij het evalueren van de plaatsing van de connector, houd rekening met de volgende punten:

- Als u gebruiken van beperkte Kerberos-delegatie (KCD) voor eenmalige aanmelding wilt, moet de connector peeren naar een datacenter. Bovendien moet de connectorserver worden toegevoegd aan een domein.  
- Bij twijfel door de connector dichter naar de toepassing te installeren.

### <a name="general-approach-to-minimize-latency"></a>Algemene benadering om latentie te minimaliseren

U kunt de latentie van het end-to-end-verkeer minimaliseren door het optimaliseren van elke netwerkverbinding. Elke verbinding kan worden geoptimaliseerd door:

- De afstand tussen de twee kanten van de hop verlagen.
- Kies het juiste netwerk om door te gaan. Bijvoorbeeld, doorlopen van een particulier netwerk in plaats van het openbare Internet mogelijk sneller, vanwege de speciale koppelingen naar.

Als u een specifieke VPN of ExpressRoute-verbinding tussen Azure en uw bedrijfsnetwerk hebt, kunt u die gebruiken.

## <a name="focus-your-optimization-strategy"></a>Richt u uw strategie voor optimalisatie

Er is weinig die u voor het beheren van de verbinding tussen uw gebruikers en de service voor toepassingsproxy kunt uitvoeren. Gebruikers kunnen toegang krijgen tot uw apps vanuit een thuis netwerk, een café of een ander land/nieuwe regio. In plaats daarvan kunt u de verbindingen van de service voor toepassingsproxy om de Application Proxy connectors voor de apps te optimaliseren. Houd rekening met het opnemen van de volgende patronen in uw omgeving.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Patroon 1: De connector dicht bij de toepassing plaatsen

Plaats de connector dicht bij de doeltoepassing in het netwerk van de klant. Deze configuratie wordt stap 3 in het diagram topografie geminimaliseerd omdat de connector en de toepassing sluiten.

Als de connector een verbinding met de domeincontroller moet, zijn dit patroon is nuttig. De meeste van onze klanten gebruik dit patroon, omdat deze geschikt is voor de meeste scenario's. Dit patroon kan ook worden gecombineerd met het patroon 2 het optimaliseren van verkeer tussen de service en de connector.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Patroon 2: Profiteren van ExpressRoute met Microsoft-peering

Als u ExpressRoute instellen met Microsoft-peering hebt, kunt u de snellere ExpressRoute-verbinding voor verkeer tussen de Application Proxy en de connector. De connector is nog steeds op het netwerk dicht bij de app.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Patroon 3: Profiteren van ExpressRoute met persoonlijke peering

Als u een toegewezen VPN of ExpressRoute instellen met privé-peering tussen Azure en uw bedrijfsnetwerk, hebt u een andere optie. In deze configuratie is doorgaans het virtuele netwerk in Azure beschouwd als een uitbreiding van het bedrijfsnetwerk bevinden. U kunt dus de connector te installeren in het datacenter Azure, en nog steeds voldoen aan de eisen van de lage latentie van de connector-naar-app-verbinding.

Latentie is niet beschadigd omdat via een speciale verbinding verkeersstromen. U ophalen verbeterde Application Proxy-naar-serviceconnector latentie ook omdat de connector is geïnstalleerd in een Azure-datacenter dicht bij de locatie van uw Azure AD-tenant.

![Diagram van de connector is geïnstalleerd binnen een Azure-datacenter](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andere manieren

Hoewel de focus van dit artikel de plaatsing van de connector wordt, kunt u ook de plaatsing van de toepassing om op te halen van betere latentie kenmerken wijzigen.

Steeds meer verplaatst organisaties hun netwerken in gehoste omgevingen. Hierdoor kunnen ze hun apps plaatsen in een omgeving die u maakt ook deel uit van het bedrijfsnetwerk en nog steeds binnen het domein. In dit geval kunnen de patronen in de voorgaande secties besproken worden toegepast op de nieuwe locatie van de toepassing. Als u deze optie overweegt, raadpleegt u [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

U kunt ook uw connectors ordenen met behulp van [connector groepen](application-proxy-connector-groups.md) om apps te richten die zich op verschillende locaties en netwerken bevinden.

## <a name="common-use-cases"></a>Algemene scenario’s

In deze sectie doorlopen we enkele algemene scenario's. Wordt ervan uitgegaan dat de Azure AD-tenant (en dus proxy-service-eindpunt) bevindt zich in de Verenigde Staten (VS). De overwegingen beschreven in deze gevallen gelden ook voor andere regio's over de hele wereld gebruiken.

Voor deze scenario's we een 'hop' voor elke verbinding aanroepen en ze voor eenvoudiger bespreking cijfer:

- **Hop 1**: gebruiker naar de Application proxy-service
- **Hop 2**: Service toepassings proxy aan de connector voor de toepassings proxy
- **Hop 3**: toepassings proxy connector voor de doel toepassing 

### <a name="use-case-1"></a>Use-case 1

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten, met gebruikers in dezelfde regio. Er zijn geen ExpressRoute of VPN-bestaat tussen het Azure-datacenter en het bedrijfsnetwerk bevinden.

**Aanbeveling:** Volg patroon 1, zoals beschreven in de vorige sectie. Voor verbeterde latentie, overweeg het gebruik van ExpressRoute, indien nodig.

Dit is een eenvoudige patroon. U optimaliseren hops 3 door de connector in de buurt van de app. Dit is ook een logische keuze, omdat de connector wordt doorgaans geïnstalleerd met peeren naar de app en het datacenter KCD bewerkingen uit te voeren.

![Diagram waarin gebruikers, proxy, connector en app worden weer gegeven, zijn allemaal in de Verenigde Staten](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Use-case 2

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten, met gebruikers die wereld wijd verspreid zijn. Er zijn geen ExpressRoute of VPN-bestaat tussen het Azure-datacenter en het bedrijfsnetwerk bevinden.

**Aanbeveling:** Volg patroon 1, zoals beschreven in de vorige sectie.

Nogmaals, het algemene patroon is om te optimaliseren hops 3, plaatst u de connector in de buurt van de app. Hop 3 is niet normaal gesproken dure, als deze allemaal binnen dezelfde regio bevinden. Echter, hop 1 duurder kan zijn, afhankelijk van waar de gebruiker is, omdat gebruikers overal ter wereld moeten toegang hebben tot de Application Proxy-instantie in de Verenigde Staten. Het is vermelden waard dat een proxy-oplossing heeft dezelfde kenmerken met betrekking tot de gebruikers wordt wereldwijd verspreid.

![Gebruikers zijn wereld wijd verspreid, maar alle andere gegevens bevinden zich in de VS](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Use-case 3

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met Microsoft-peering bestaat tussen Azure en het bedrijfsnetwerk bevinden.

**Aanbeveling:** Volg de patronen 1 en 2, zoals beschreven in de vorige sectie.

Eerst, plaatst u de connector zo dicht mogelijk bij de app. Het systeem worden vervolgens automatisch de ExpressRoute gebruikt voor hop 2.

Als het ExpressRoute-verbinding van Microsoft gebruikmaakt-peering, loopt het verkeer tussen de proxy en de connector die koppeling. Latentie heeft een hop 2 is geoptimaliseerd.

![Diagram van ExpressRoute tussen de proxy- en -connector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Use-case 4

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met persoonlijke peering bestaat tussen Azure en het bedrijfsnetwerk bevinden.

**Aanbeveling:** Volg patroon 3, zoals beschreven in de vorige sectie.

Plaats de connector in het Azure-datacenter die is verbonden met het bedrijfsnetwerk via ExpressRoute-privépeering.

De connector kan worden geplaatst in het Azure-datacenter. Omdat de connector nog steeds een verbinding met de toepassing en het datacenter tot en met het particuliere netwerk is, blijft hops 3 geoptimaliseerd. Bovendien is hops 2 verder geoptimaliseerd.

![Connector in azure Data Center, ExpressRoute tussen connector en app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Use-case 5

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de EU, met het exemplaar van de toepassings proxy en de meeste gebruikers in de VS.

**Aanbeveling:** Plaats de connector in de buurt van de app. Omdat gebruikers in de VS toegang hebben tot een Application Proxy-exemplaar dat zich in dezelfde regio gebeurt, is het niet hop 1 te duur. Hop 3 is geoptimaliseerd. Overweeg het gebruik van ExpressRoute om te optimaliseren hops 2.

![Diagram toont gebruikers en proxy in de Verenigde Staten, connector en app in de EU](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

U kunt ook overwegen met behulp van een andere variant in dit geval. Als de meeste gebruikers in de organisatie in de Verenigde Staten, dan waarschijnlijk dat wordt uw netwerk ook de Verenigde Staten. Plaats de connector in de Verenigde Staten en gebruikt u de regel toegewezen interne bedrijfsnetwerk bevinden naar de toepassing in de EU. Deze manier hops 2 en 3 worden geoptimaliseerd.

![Diagram toont gebruikers, proxy en connectors in de VS, app in de EU](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassings proxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassings proxy oplossen](application-proxy-troubleshoot.md)
