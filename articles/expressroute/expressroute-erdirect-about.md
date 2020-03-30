---
title: Over Azure ExpressRoute Direct
description: Deze pagina geeft een overzicht van ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083591"
---
# <a name="about-expressroute-direct"></a>Over ExpressRoute Direct

ExpressRoute Direct biedt u de mogelijkheid om rechtstreeks verbinding te maken met het wereldwijde netwerk van Microsoft op peeringlocaties die strategisch over de hele wereld zijn verspreid. ExpressRoute Direct biedt dual 100 Gbps of 10 Gbps connectiviteit, die Active/Active-connectiviteit op schaal ondersteunt.

De belangrijkste functies die ExpressRoute Direct biedt, zijn, maar zijn niet beperkt tot:

* Massale gegevensopname in services als Storage en Cosmos DB
* Fysieke isolatie voor industrieën die gereguleerd zijn en speciale en geïsoleerde connectiviteit vereisen, zoals: Banking, Government en Retail
* Gedetailleerde controle van circuitdistributie op basis van bedrijfsonderdelen

## <a name="onboard-to-expressroute-direct"></a>Aan boord naar ExpressRoute Direct

Voordat u ExpressRoute Direct gebruikt, moet u eerst uw abonnement inschrijven. Als u zich wilt <ExpressRouteDirect@microsoft.com> inschrijven, stuurt u een e-mail naar met uw abonnements-ID, inclusief de volgende gegevens:

* Scenario's die u wilt uitvoeren met **ExpressRoute Direct**
* Locatievoorkeuren - zie [Partners en peering locaties](expressroute-locations-providers.md) voor een volledige lijst van alle locaties
* Tijdlijn voor implementatie
* Alle andere vragen

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute met behulp van een dienstverlener en ExpressRoute Direct

| **ExpressRoute met behulp van een serviceprovider** | **ExpressRoute Direct** | 
| --- | --- |
| Maakt gebruik van serviceproviders om snelle onboarding en connectiviteit in bestaande infrastructuur mogelijk te maken | Vereist 100 Gbps/10 Gbps infrastructuur en volledig beheer van alle lagen
| Integreert met honderden providers, waaronder Ethernet en MPLS | Directe/specifieke capaciteit voor gereguleerde industrieën en massale gegevensopname |
| Circuits SKU's van 50 Mbps naar 10 Gbps | De klant kan een combinatie van de volgende circuit SKU's selecteren op 100 Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> De klant kan een combinatie van de volgende circuit SKU's selecteren op 10 Gbps ExpressRoute Direct:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Geoptimaliseerd voor één tenant | Geoptimaliseerd voor één tenant met meerdere bedrijfsonderdelen en meerdere werkomgevingen

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct-circuits

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute u verbindingen tot stand brengen met Microsoft-cloudservices, zoals Microsoft Azure en Office 365.

Elke peeringlocatie heeft toegang tot het wereldwijde netwerk van Microsoft en heeft standaard toegang tot elke regio in een geopolitieke zone en heeft toegang tot alle wereldwijde regio's met een premium circuit.  

De functionaliteit in de meeste scenario's is gelijk aan circuits die gebruik maken van een ExpressRoute-serviceprovider om te werken. Om verdere granulariteit en nieuwe mogelijkheden te ondersteunen die worden aangeboden met ExpressRoute Direct, bestaan er bepaalde belangrijke mogelijkheden op ExpressRoute Direct Circuits.

## <a name="circuit-skus"></a>Circuit SKU's

ExpressRoute Direct ondersteunt enorme scenario's voor het innemen van gegevens in Azure-opslag en andere big data-services. ExpressRoute circuits op 100 Gbps ExpressRoute Direct ondersteunen nu ook **40 Gbps** en **100 Gbps** circuit SKU's. De fysieke poortparen zijn slechts **100 of 10 Gbps** en kunnen meerdere virtuele circuits hebben. Circuitmaten:

| **ExpressRoute direct van 100 Gbps** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Geabonneerde bandbreedte**: 200 Gbps | **Geabonneerde bandbreedte**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Technische voorschriften

* MSEE-interfaces (Microsoft Enterprise Edge Router):
    * Dual 10 of 100 Gigabit Ethernet-poorten alleen via routerpaar
    * LR Fiber-connectiviteit met één modus
    * IPv4 en IPv6
    * IP MTU 1500 bytes

* Switch/Router Layer 2/Layer 3-connectiviteit:
    * Moet 1 802.1Q (Dot1Q) tag of twee Tag 802.1Q (QinQ) tag inkapseling ondersteunen
    * Ethertype = 0x8100
    * Moet de buitenste VLAN-tag (STAG) toevoegen op basis van de VLAN-id die door Microsoft is opgegeven - *alleen van toepassing op QinQ*
    * Moet meerdere BGP-sessies (VLAN's) per poort en apparaat ondersteunen
    * IPv4- en IPv6-connectiviteit. *Voor IPv6 wordt geen extra subinterface gemaakt. IPv6-adres wordt toegevoegd aan de bestaande subinterface.* 
    * Optioneel: [BFD-ondersteuning (Bidirectional Forwarding Detection),](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) die standaard is geconfigureerd op alle private peerings op expressroute-circuits

## <a name="vlan-tagging"></a>VLAN-labeling

ExpressRoute Direct ondersteunt zowel QinQ als Dot1Q VLAN tagging.

* **QinQ VLAN Tagging** zorgt voor geïsoleerde routing domeinen op een per ExpressRoute circuit basis. Azure wijst dynamisch een S-Tag toe bij het maken van circuits en kan niet worden gewijzigd. Elke peering op het circuit (Private en Microsoft) zal gebruik maken van een unieke C-Tag als de VLAN. De C-Tag hoeft niet uniek te zijn op circuits op de ExpressRoute Direct-poorten.

* **Dot1Q VLAN Tagging** zorgt voor een enkele gecodeerde VLAN op een per ExpressRoute Direct poort paar basis. Een C-Tag die wordt gebruikt op een peering moet uniek zijn op alle circuits en peerings op het ExpressRoute Direct-poortpaar.

## <a name="workflow"></a>Werkstroom

[![Workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct biedt dezelfde SLA van bedrijfsniveau met actieve/actieve redundante verbindingen in het Microsoft Global Network. ExpressRoute-infrastructuur is redundant en de connectiviteit in het Microsoft Global Network is redundant en divers en schaalt dienovereenkomstig op met de eisen van de klant. 

## <a name="next-steps"></a>Volgende stappen

[ExpressRoute Direct configureren](expressroute-howto-erdirect.md)
