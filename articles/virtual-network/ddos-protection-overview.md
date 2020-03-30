---
title: Overzicht van Azure DDoS Protection Standard
description: Meer informatie over de Azure DDoS Protection-service.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536340"
---
# <a name="azure-ddos-protection-standard-overview"></a>Overzicht van Azure DDoS Protection Standard

DDoS-aanvallen (Distributed Denial of Service-aanvallen) vormen een van de grootste beschikbaarheids- en beveiligingsproblemen voor klanten die hun toepassingen verplaatsen naar de cloud. Een DDoS-aanval probeert de bronnen van een toepassing uit te putten, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.

Azure DDoS-bescherming, gecombineerd met best practices voor het ontwerpen van toepassingen, biedt verdediging tegen DDoS-aanvallen. Azure DDoS-beveiliging biedt de volgende servicelagen:

- **Basis:** automatisch ingeschakeld als onderdeel van het Azure-platform. Always-on verkeersbewaking en real-time mitigatie van veelvoorkomende aanvallen op netwerkniveau bieden dezelfde verdedigingen die worden gebruikt door de online services van Microsoft.De volledige schaal van het wereldwijde netwerk van Azure kan worden gebruikt om aanvalsverkeer tussen regio's te distribueren en te beperken.Er wordt beveiliging geboden voor openbare [IP-adressen](virtual-network-public-ip-address.md)van IPv4 en IPv6 Azure.
- **Standaard:** biedt extra mitigatiemogelijkheden voor de basisservicelaag die specifiek zijn afgestemd op Azure Virtual Network-bronnen. DDoS Protection Standard is eenvoudig in te schakelen en vereist geen wijzigingen in de toepassing. Beveiligingsbeleid is afgestemd door middel van specifieke controle van verkeer en algoritmen voor machine learning. Beleidsregels worden toegepast op openbare IP-adressen die zijn gekoppeld aan resources die zijn geïmplementeerd in virtuele netwerken, zoals Azure Load Balancer, Azure Application Gateway en Azure Service Fabric-instanties, maar deze bescherming is niet van toepassing op App-serviceomgevingen.Realtime telemetrie is beschikbaar via Azure Monitor-weergaven tijdens een aanval en voor geschiedenis. Uitgebreide aanvalmitigatieanalyses zijn beschikbaar via diagnostische instellingen. Bescherming van toepassingslagen kan worden toegevoegd via de [Azure Application Gateway Web Application Firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of door een firewall van derden te installeren vanuit Azure Marketplace. Er wordt beveiliging geboden voor openbare [IP-adressen](virtual-network-public-ip-address.md)van IPv4 en IPv6 Azure.

|Functie                                         |DDoS Protection Basic                 |DDoS-beveiligingsstandaard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Actieve verkeersbewaking & altijd bij detectie |Ja                                   |Ja                                           |
|Automatische aanvalsoplossingen                    |Ja                                   |Ja                                           |
|Beschikbaarheidsgarantie                          |Azure-regio                          |Toepassing                                   |
|Mitigatiebeleid                             |Afgestemd op het volume van azure-verkeersregio's |Afgestemd op het volume van het toepassingsverkeer          |
|Statistieken & waarschuwingen                                |Nee                                    |Realtime aanvalsstatistieken & diagnostische logboeken via Azure-monitor                                 |
|Mitigatierapporten                              |Nee                                    |Rapporten over het beperken na aanvallen                |
|Stroomlogboeken voor mitigatie                            |Nee                                    |NRT-logboekstroom voor SIEM-integratie           |
|Aanpassing van het mitigatiebeleid                 |Nee                                    |DDoS-experts inschakelen                           |
|Ondersteuning                                         |Beste inspanning                           |Toegang tot DDoS-experts tijdens een actieve aanval|
|SLA                                             |Azure-regio                          |Toepassingsgarantie & kostenbescherming       |
|Prijzen                                         |Gratis                                  |Maandelijks & gebruik op basis van                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typen DDoS-aanvallen die DDoS Protection Standard vermindert

DDoS Protection Standard kan de volgende soorten aanvallen beperken:

- **Volumetrische aanvallen**: Het doel van de aanval is om de netwerklaag te overspoelen met een aanzienlijke hoeveelheid schijnbaar legitiem verkeer. Het omvat UDP overstromingen, versterking overstromingen, en andere spoofed-packet overstromingen. DDoS Protection Standard vermindert deze potentiële aanvallen van meerdere gigabytes door ze automatisch te absorberen en te schrobben, met de wereldwijde netwerkschaal van Azure.
- **Protocolaanvallen**: Deze aanvallen maken een doel ontoegankelijk door gebruik te maken van een zwakte in de laag 3- en layer 4-protocolstack. Het omvat, SYN flood aanvallen, reflectie aanvallen, en andere protocol aanvallen. DDoS Protection Standard vermindert deze aanvallen, onderscheidt zich tussen kwaadaardig en legitiem verkeer, door interactie met de client en het blokkeren van kwaadaardig verkeer. 
- **Bron (applicatie) laag aanvallen:** Deze aanvallen richten zich op webapplicatie pakketten, om de overdracht van gegevens tussen hosts te verstoren. De aanvallen omvatten HTTP-protocol schendingen, SQL-injectie, cross-site scripting, en andere laag 7 aanvallen. Gebruik een Web Application Firewall, zoals de [Azure Application Gateway-webtoepassingsfirewall](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)en DDoS Protection Standard om deze aanvallen te beschermen. Er zijn ook firewallaanbiedingen van derden beschikbaar in de [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)

DDoS Protection Standard beschermt resources in een virtueel netwerk, inclusief openbare IP-adressen die zijn gekoppeld aan virtuele machines, load balancers en toepassingsgateways. In combinatie met de Application Gateway-webtoepassingsfirewall of een firewall voor webtoepassingen van derden die zijn geïmplementeerd in een virtueel netwerk met een openbaar IP- netwerk, kan DDoS Protection Standard volledige laag 3-to-layer 7-mitigatiemogelijkheden bieden.

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard-functies

![DDoS-functionaliteit](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection Standard functies omvatten:

- **Native platform integratie:** Native geïntegreerd in Azure. Inclusief configuratie via de Azure-portal. DDoS Protection Standard begrijpt uw resources en resourceconfiguratie.
- **Beveiliging van de turnkey:** Vereenvoudigde configuratie beschermt onmiddellijk alle bronnen op een virtueel netwerk zodra DDoS Protection Standard is ingeschakeld. Er is geen interventie of gebruikersdefinitie vereist. DDoS Protection Standard vermindert de aanval direct en automatisch, zodra deze wordt gedetecteerd.
- **Always-on verkeersbewaking:** Uw applicatie verkeerspatronen worden 24 uur per dag, 7 dagen per week gemonitord, op zoek naar indicatoren van DDoS-aanvallen. Mitigatie wordt uitgevoerd wanneer het beveiligingsbeleid wordt overschreden.
- **Adaptieve tuning:** Intelligente verkeersprofilering leert het verkeer van uw toepassing in de loop van de tijd en selecteert en werkt het profiel bij dat het meest geschikt is voor uw service. Het profiel wordt aangepast naarmate het verkeer in de loop van de tijd verandert.
- **Bescherming met meerdere lagen:** Biedt full stack DDoS-bescherming, wanneer deze wordt gebruikt met een firewall voor webtoepassingen.
- **Uitgebreide mitigatieschaal:** Meer dan 60 verschillende aanvalstypen kunnen worden beperkt, met wereldwijde capaciteit, om te beschermen tegen de grootste bekende DDoS-aanvallen.
- **Aanvalsanalyses:** Ontvang gedetailleerde rapporten in stappen van vijf minuten tijdens een aanval en een volledig overzicht nadat de aanval is afgelopen. Stream mitigation flow logs to an offline security information and event management (SIEM) system for near real-time monitoring during an attack.
- **Aanvalsstatistieken:** Samengevatte statistieken van elke aanval zijn toegankelijk via Azure Monitor.
- **Waarschuwing voor aanvallen:** Waarschuwingen kunnen worden geconfigureerd bij het begin en einde van een aanval, en over de duur van de aanval, met behulp van ingebouwde aanvalsstatistieken. Waarschuwingen worden geïntegreerd in uw operationele software zoals Microsoft Azure Monitor-logboeken, Splunk, Azure Storage, E-mail en de Azure-portal.
- **Kostengarantie:** Data-overdracht en applicatie scale-out service credits voor gedocumenteerde DDoS-aanvallen.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard mitigatie

DDoS Protection Standard bewaakt het werkelijke verkeersgebruik en vergelijkt dit voortdurend met de drempelwaarden die zijn gedefinieerd in het DDoS-beleid. Wanneer de verkeersdrempel wordt overschreden, wordt DDoS-beperking automatisch gestart. Wanneer het verkeer onder de drempelwaarde terugkeert, wordt de beperking verwijderd.

![Oplossing](./media/ddos-protection-overview/mitigation.png)

Tijdens de beperking wordt verkeer dat naar de beveiligde bron wordt verzonden omgeleid door de DDoS-beveiligingsservice en worden verschillende controles uitgevoerd, zoals de volgende controles:

- Zorg ervoor dat pakketten voldoen aan internetspecificaties en niet misvormd zijn.
- Communiceer met de client om te bepalen of het verkeer mogelijk een vervalst pakket is (bijvoorbeeld: SYN Auth of SYN Cookie of door een pakket te laten vallen zodat de bron het opnieuw kan verzenden).
- Beoordeel-limiet pakketten, als er geen andere handhavingsmethode kan worden uitgevoerd.

DDoS-beveiliging blokkeert aanvalsverkeer en stuurt het resterende verkeer door naar de beoogde bestemming. Binnen een paar minuten na detectie van aanvallen wordt u op de hoogte gebracht met behulp van metrische gegevens van Azure Monitor. Door logboekregistratie op dDoS Protection Standard telemetrie te configureren, u de logboeken naar beschikbare opties voor toekomstige analyse schrijven. Metrische gegevens in Azure Monitor voor DDoS Protection Standard worden 30 dagen bewaard.

Microsoft werkt samen met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) om een interface te bouwen waar u verkeer genereren tegen openbare IP-adressen met DDoS Protection voor simulaties. Met de BreakPoint Cloud-simulatie u:

- Valideren hoe Microsoft Azure DDoS Protection Standard uw Azure-bronnen beschermt tegen DDoS-aanvallen
- Optimaliseer uw incidentresponsproces terwijl u onder DDoS-aanval staat
- DDoS-naleving van documenten
- Train uw netwerkbeveiligingsteams

## <a name="next-steps"></a>Volgende stappen

- [DDoS-beveiligingsstandaard configureren](manage-ddos-protection.md)
