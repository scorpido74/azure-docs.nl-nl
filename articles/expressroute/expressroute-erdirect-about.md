---
title: Over Azure ExpressRoute direct
description: Op deze pagina vindt u een overzicht van ExpressRoute direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083591"
---
# <a name="about-expressroute-direct"></a>Over ExpressRoute Direct

ExpressRoute Direct biedt de mogelijkheid om rechtstreeks in het wereldwijde netwerk van Microsoft op peeringlocaties strategisch verdeeld over de hele wereld verbinding te maken. ExpressRoute direct biedt een Dual 100 Gbps-of 10 Gbps-connectiviteit, die ondersteuning biedt voor actieve/actieve connectiviteit op schaal.

Belangrijke functies van ExpressRoute Direct bevatten, maar niet beperkt tot:

* Massale gegevensopname in services als Storage en Cosmos DB
* Fysieke isolatie voor branches die worden geregeld en nodig toegewezen en geïsoleerd connectiviteit, zoals: bankieren-, overheids- en detailhandel
* Gedetailleerde controle van circuitdistributie op basis van bedrijfsonderdelen

## <a name="onboard-to-expressroute-direct"></a>Onboarding naar ExpressRoute direct

Voordat u ExpressRoute direct gebruikt, moet u uw abonnement eerst registreren. Als u wilt inschrijven, stuur een E-mail naar <ExpressRouteDirect@microsoft.com> door uw abonnements-ID, met inbegrip van de volgende gegevens:

* Scenario's die u nodig hebt om uit te voeren met **ExpressRoute Direct**
* Voorkeuren voor locatie - Zie [Partners en peeringlocaties](expressroute-locations-providers.md) voor een volledige lijst van alle locaties
* Tijdlijn voor implementatie
* Andere vragen

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute met behulp van een serviceprovider en ExpressRoute Direct

| **ExpressRoute met behulp van een serviceprovider** | **ExpressRoute Direct** | 
| --- | --- |
| Maakt gebruik van serviceproviders om in te schakelen voor snelle onboarding en connectiviteit in bestaande infrastructuur | Vereist een infra structuur van 100 Gbps/10 Gbps en volledig beheer van alle lagen
| Kan worden geïntegreerd met honderden van met inbegrip van Ethernet- en MPLS-providers | Direct/toegewezen capaciteit voor gereguleerde industrieën en enorme gegevensopname |
| Circuits SKU's van 50 Mbps naar 10 Gbps | Klant kan een combi natie van de volgende circuit-Sku's op 100 Gbps ExpressRoute direct selecteren: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Klant kan een combi natie van de volgende circuit-Sku's op 10 Gbps ExpressRoute direct selecteren:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Geoptimaliseerd voor één tenant | Geoptimaliseerd voor één Tenant met meerdere bedrijfs eenheden en meerdere werk omgevingen

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct-circuits

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services, zoals Microsoft Azure, en Office 365.

Elke locatie toegang heeft tot het wereldwijde netwerk van Microsoft en hebben toegang tot andere regio's in een geopolitieke zone standaard en hebben toegang tot alle globale regio's met een premium-circuit.  

De functionaliteit in de meeste gevallen is gelijk aan-circuits die gebruikmaken van een ExpressRoute-provider om te werken. Ter ondersteuning van meer granulariteit en nieuwe functionaliteit die met behulp van ExpressRoute Direct, zijn er bepaalde kernfuncties die aanwezig zijn op directe ExpressRoute-Circuits.

## <a name="circuit-skus"></a>Circuit-SKU 's

ExpressRoute Direct biedt ondersteuning voor scenario's voor het opnemen van enorme gegevens in Azure storage en andere services big data. ExpressRoute-circuits op 100 Gbps ExpressRoute direct bieden ook ondersteuning voor **40 Gbps** en **100 Gbps** circuit-sku's. De fysieke poort paren zijn **100 of 10 Gbps** en kunnen meerdere virtuele circuits hebben. Circuit grootten:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Geabonneerde band breedte**: 200 Gbps | **Geabonneerde band breedte**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Technische vereisten

* MSEE-interfaces (micro soft Enter prise edge router):
    * Dual 10 of 100 Gigabit Ethernet-poorten alleen via router paar
    * Enkelvoudige modus LR glasvezel connectiviteit
    * IPv4 en IPv6
    * IP-MTU 1500 bytes

* Switch/router Layer 2/Layer 3-connectiviteit:
    * Moet ondersteuning bieden voor 1 802.1 Q (Dot1Q)-tag of twee tag 802.1 Q-tags (QinQ)
    * Ethertype = 0x8100
    * Moet de buiten-VLAN-tag (STAG) toevoegen op basis van de VLAN-ID die door micro soft is opgegeven, *alleen van toepassing op QinQ*
    * Moet ondersteuning bieden voor meerdere BGP-sessies (VLAN'S) per poort en apparaat
    * IPv4-en IPv6-connectiviteit. *Voor IPv6 wordt geen extra subinterface gemaakt. Het IPv6-adres wordt toegevoegd aan de bestaande subinterface*. 
    * Optioneel: ondersteuning voor [bidirectionele forwarding-detectie (Bfd)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , die standaard wordt geconfigureerd voor alle particuliere peerings op ExpressRoute-circuits

## <a name="vlan-tagging"></a>VLAN-Tagging

ExpressRoute Direct biedt ondersteuning voor zowel QinQ en Dot1Q VLAN-tagging.

* **QinQ VLAN-Tagging** kunnen voor geïsoleerde Routeringsdomeinen op een per ExpressRoute-circuit gefactureerd. Azure dynamisch een S-Tag bij het maken van circuit kan worden toegewezen en kan niet worden gewijzigd. Elke peering van het circuit (privé- en Microsoft), wordt een unieke C-code gebruiken als het VLAN. De C-Tag is niet uniek zijn in circuits op de poorten die ExpressRoute rechtstreeks vereist.

* **Dot1Q VLAN-Tagging** kunt voor een enkel VLAN gelabeld op een per ExpressRoute directe poort paar gefactureerd. Een C-code die wordt gebruikt op een peering moet uniek zijn in alle circuits en op het paar van de poort ExpressRoute Direct-peerings.

## <a name="workflow"></a>Werkstroom

[![werk stroom](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct biedt de dezelfde SLA op ondernemingsniveau met redundante verbindingen actief/actief in het wereldwijde netwerk van Microsoft. ExpressRoute-infrastructuur is overbodig en connectiviteit in het wereldwijde netwerk van Microsoft is overbodig en diverse en kan worden geschaald dienovereenkomstig aan de klantvereisten. 

## <a name="next-steps"></a>Volgende stappen

[ExpressRoute Direct configureren](expressroute-howto-erdirect.md)
