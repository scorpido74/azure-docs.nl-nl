---
title: Over Azure ExpressRoute direct
description: Meer informatie over de belangrijkste functies van Azure ExpressRoute direct en informatie die nodig is voor het onboarden van ExpressRoute direct, zoals beschik bare Sku's en technische vereisten.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f0faa4992b025783e4e5448dcf04fcfa958ba5ed
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192355"
---
# <a name="about-expressroute-direct"></a>Over ExpressRoute Direct

ExpressRoute direct biedt u de mogelijkheid om rechtstreeks verbinding te maken met het wereld wijde netwerk van micro soft op locatie van peering strategisch gedistribueerd over de hele wereld. ExpressRoute direct biedt een Dual 100 Gbps-of 10 Gbps-connectiviteit, die ondersteuning biedt voor actieve/actieve connectiviteit op schaal.

De belangrijkste functies die ExpressRoute direct biedt, zijn inclusief, maar zijn niet beperkt tot:

* Massale gegevensopname in services als Storage en Cosmos DB
* Fysieke isolatie voor branches die worden gereguleerd en die speciale en geïsoleerde connectiviteit vereisen, zoals bankieren, overheids instellingen en handels versie
* Gedetailleerde controle van circuitdistributie op basis van bedrijfsonderdelen

## <a name="onboard-to-expressroute-direct"></a>Onboarding naar ExpressRoute direct

Voordat u ExpressRoute direct gebruikt, moet u uw abonnement eerst registreren. Als u zich wilt inschrijven, stuurt u een E-mail naar <ExpressRouteDirect@microsoft.com> met uw abonnements-id, met inbegrip van de volgende gegevens:

* Scenario's die u wilt uitvoeren met **ExpressRoute direct**
* Locatie voorkeuren: Zie [partners en peering-locaties](expressroute-locations-providers.md) voor een volledige lijst met alle locaties
* Tijdlijn voor implementatie
* Eventuele andere vragen

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute met behulp van een service provider en ExpressRoute direct

| **ExpressRoute met behulp van een service provider** | **ExpressRoute Direct** | 
| --- | --- |
| Maakt gebruik van service providers om snelle onboarding en connectiviteit in bestaande infra structuur mogelijk te maken | Vereist een infra structuur van 100 Gbps/10 Gbps en volledig beheer van alle lagen
| Kan worden geïntegreerd met honderden providers, waaronder Ethernet en MPLS | Directe/toegewezen capaciteit voor gereguleerde industrieën en massale gegevens opname |
| Circuits-Sku's van 50 Mbps tot 10 Gbps | Klant kan een combi natie van de volgende circuit-Sku's op 100 Gbps ExpressRoute direct selecteren: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Klant kan een combi natie van de volgende circuit-Sku's op 10 Gbps ExpressRoute direct selecteren:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Geoptimaliseerd voor één Tenant | Geoptimaliseerd voor één Tenant met meerdere bedrijfs eenheden en meerdere werk omgevingen

## <a name="expressroute-direct-circuits"></a>ExpressRoute directe circuits

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met micro soft-Cloud Services, zoals Microsoft Azure, en Office 365.

Elke peering-locatie heeft toegang tot het wereld wijde netwerk van micro soft en heeft standaard toegang tot elke regio in een geopolitieke zone en heeft toegang tot alle wereld wijde regio's met een Premium-circuit.  

De functionaliteit in de meeste scenario's is gelijk aan circuits die gebruikmaken van een ExpressRoute-service provider om te werken. Ter ondersteuning van verdere granulatie en nieuwe mogelijkheden die worden aangeboden via ExpressRoute direct, zijn er bepaalde belang rijke mogelijkheden voor ExpressRoute direct-circuits.

## <a name="circuit-skus"></a>Circuit-Sku's

ExpressRoute direct ondersteunt massale gegevens opname scenario's in azure Storage en andere big data services. ExpressRoute-circuits op 100 Gbps ExpressRoute direct bieden ook ondersteuning voor **40 Gbps** en **100 Gbps** circuit-sku's. De fysieke poort paren zijn **100 of 10 Gbps** en kunnen meerdere virtuele circuits hebben. Circuit grootten:

| **100 Gbps ExpressRoute direct** | **10 Gbps ExpressRoute direct** | 
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

## <a name="vlan-tagging"></a>VLAN-tagging

ExpressRoute direct ondersteunt zowel QinQ-als Dot1Q VLAN-tagging.

* **QINQ VLAN-tagging** maakt geïsoleerde routerings domeinen mogelijk op basis van een per ExpressRoute-circuit. Azure wijst dynamisch een S-tag toe aan het maken van een circuit en kan niet worden gewijzigd. Voor elke peering op het circuit (persoonlijk en micro soft) wordt een unieke C-tag gebruikt als VLAN. De C-tag hoeft niet uniek te zijn in de circuits op de ExpressRoute directe poorten.

* Met **DOT1Q VLAN tagging** kunt u een enkele gelabelde VLAN op basis van een per ExpressRoute directe poort paar. Een C-tag die wordt gebruikt voor een peering moet uniek zijn voor alle circuits en peerings op het ExpressRoute direct-poort paar.

## <a name="workflow"></a>Werkstroom

[![workflowconfiguraties](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute direct biedt dezelfde SLA op ondernemings niveau met actieve/actieve redundante verbindingen in het wereld wijde netwerk van micro soft. De ExpressRoute-infra structuur is redundant en de connectiviteit met het wereld wijde micro soft-netwerk is overbodig en geschaald en kan worden aangepast aan de vereisten van de klant. 

## <a name="next-steps"></a>Volgende stappen

[ExpressRoute Direct configureren](expressroute-howto-erdirect.md)
