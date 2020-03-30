---
title: Wat is Azure Firewall Manager Preview?
description: Meer informatie over Azure Firewall Manager-functies
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366271"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Wat is Azure Firewall Manager Preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview is een beveiligingsbeheerservice die centraal beveiligingsbeleid en routebeheer biedt voor cloudgebaseerde beveiligingsperimeters. 

Firewall Manager kan beveiligingsbeheer bieden voor twee typen netwerkarchitectuur:

- **beveiligde virtuele hub**

   Een [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) is een door Microsoft beheerde bron waarmee u eenvoudig hub- en spoke-architecturen maken. Wanneer beveiligings- en routeringsbeleid aan een dergelijke hub zijn gekoppeld, wordt het een *[beveiligde virtuele hub genoemd.](secured-virtual-hub.md)* 
- **virtueel netwerk hub**

   Dit is een standaard Virtueel Azure-netwerk dat u zelf maakt en beheert. Wanneer beveiligingsbeleid is gekoppeld aan een dergelijke hub, wordt het een *virtueel hubnetwerk genoemd.* Op dit moment wordt alleen azure firewallbeleid ondersteund. U peer spoke virtuele netwerken die uw workload servers en services bevatten. U firewalls ook beheren in zelfstandige virtuele netwerken die niet zijn gekeken naar een spaak.

Zie [Wat zijn de azure Firewall Manager-architectuuropties voor](vhubs-and-vnets.md)een gedetailleerde vergelijking van beveiligde virtuele *hub-* en *hub-virtuele* netwerkarchitecturen.

![firewall-manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Azure Firewall Manager Preview-functies

Azure Firewall Manager Preview biedt de volgende functies:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Centrale Azure Firewall-implementatie en -configuratie

U meerdere Azure Firewall-exemplaren die verschillende Azure-regio's en -abonnementen bestrijken, centraal implementeren en configureren. 

### <a name="hierarchical-policies-global-and-local"></a>Hiërarchisch beleid (globaal en lokaal)

U Azure Firewall Manager Preview gebruiken om azure firewall-beleid centraal te beheren voor meerdere beveiligde virtuele hubs. Uw centrale IT-teams kunnen een algemeen firewallbeleid maken om het firewallbeleid voor de hele organisatie tussen teams af te dwingen. Met lokaal geschreven firewallbeleid kan een DevOps-selfservicemodel worden toegestaan voor een betere wendbaarheid.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Geïntegreerd met security-as-a-service van derden voor geavanceerde beveiliging

Naast Azure Firewall u services as a service-providers (SECaaS) van derden integreren om extra netwerkbescherming te bieden voor uw VNet- en branch-internetverbindingen.

Deze functie is alleen beschikbaar met beveiligde virtuele hub-implementaties.

- VNet naar Internet (V2I) verkeersfiltering

   - Filter uitgaand virtueel netwerkverkeer met de gewenste externe beveiligingsprovider.
   - Maak gebruik van geavanceerde internetbeveiliging die door de gebruiker is ingeschakeld voor uw cloudworkloads die op Azure worden uitgevoerd.

- Vertakking naar internet (B2I) verkeersfiltering

   Maak gebruik van uw Azure-connectiviteit en wereldwijde distributie om eenvoudig filtering van derden toe te voegen voor branch-to-internet-scenario's.

Zie [Wat zijn azure Firewall Manager vertrouwde beveiligingspartners (preview)voor](trusted-security-partners.md) meer informatie over vertrouwde beveiligingsproviders?

### <a name="centralized-route-management"></a>Gecentraliseerd routebeheer

Leid eenvoudig verkeer naar uw beveiligde hub voor filteren en logboeken zonder dat u handmatig UDR(User Defined Routes) hoeft in te stellen op gesproken virtuele netwerken. 

Deze functie is alleen beschikbaar met beveiligde virtuele hub-implementaties.

U externe providers gebruiken voor Branch to Internet (B2I) verkeerfiltering, naast Azure Firewall voor Branch to VNet (B2V), VNet naar VNet (V2V) en VNet naar Internet (V2I). U ook externe providers gebruiken voor V2I-verkeersfiltering zolang Azure Firewall niet vereist is voor B2V of V2V. 

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Azure Firewall-beleid kan worden gebruikt in verschillende regio's. U bijvoorbeeld een beleid maken in West-VS en het gebruiken in Oost-VS. 

## <a name="known-issues"></a>Bekende problemen

Azure Firewall Manager Preview heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
|Filterbeperkingen van derden.|V2I-verkeersfiltering met externe providers wordt niet ondersteund met Azure Firewall B2V en V2V.|Er wordt een onderzoek uitgevoerd|
|Verkeer splitsen momenteel niet ondersteund.|Het splitsen van Office 365- en Azure Public PaaS-verkeer wordt momenteel niet ondersteund. Als zodanig stuurt het selecteren van een externe provider voor V2I of B2I ook al het Azure Public PaaS- en Office 365-verkeer via de partnerservice.|Onderzoek naar het splitsen van het verkeer op de hub.
|Eén beveiligde virtuele hub per regio.|U niet meer dan één beveiligde virtuele hub per regio hebben.|Meerdere virtuele WAN's maken in een regio.|
|Het basisbeleid moet zich in dezelfde regio bevinden als het lokale beleid.|Maak al uw lokale beleidsregels in dezelfde regio als het basisbeleid. U nog steeds een beleid toepassen dat in de ene regio is gemaakt op een beveiligde hub vanuit een andere regio.|Er wordt een onderzoek uitgevoerd|
|Interhubcommunicatie werkt niet met beveiligde virtuele hub|Beveiligde Virtual Hub naar Beveiligde Virtual Hub-communicatie wordt nog niet ondersteund.|Er wordt een onderzoek uitgevoerd|
|Alle beveiligde virtuele hubs die dezelfde virtuele WAN delen, moeten zich in dezelfde brongroep bevindt.|Dit gedrag is vandaag afgestemd op virtuele WAN-hubs.|Maak meerdere virtuele WAN's waarmee beveiligde virtuele hubs kunnen worden gemaakt in verschillende brongroepen.|
|IP-groepen worden niet ondersteund in Firewallbeleid.|IP-groepen zijn in openbare preview en momenteel alleen ondersteund met traditionele firewall regels.|Fix in progress.
|CSP-abonnementen (Cloud Solution Provider) worden niet ondersteund.|Momenteel worden [CSP-abonnementen](https://azure.microsoft.com/offers/ms-azr-0145p/) niet ondersteund.|Er wordt een onderzoek uitgevoerd

## <a name="next-steps"></a>Volgende stappen

- Overzicht van [het overzicht van de implementatievan Azure Firewall Manager Preview controleren](deployment-overview.md)
- Meer informatie over [beveiligde virtuele hubs](secured-virtual-hub.md).