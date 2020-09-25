---
title: Azure DDoS Protection standaard overzicht
description: Meer informatie over hoe de Azure DDoS Protection Standard, in combi natie met aanbevolen procedures voor het ontwerpen van toepassingen, bescherming biedt tegen DDoS-aanvallen.
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
ms.openlocfilehash: 726bc34a309ac6a29c189f13037e059f200732ce
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335915"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection standaard overzicht

DDoS-aanvallen (Distributed Denial of Service-aanvallen) vormen een van de grootste beschikbaarheids- en beveiligingsproblemen voor klanten die hun toepassingen verplaatsen naar de cloud. Een DDoS-aanval probeert de bronnen van een toepassing te ontnemen, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.

Azure DDoS Protection, in combi natie met aanbevolen procedures voor het ontwerpen van toepassingen, biedt verdediging tegen DDoS-aanvallen. Elke eigenschap in azure wordt beschermd door de Azure Infrastructure DDoS (Basic)-beveiliging.Azure DDoS Protection Standard biedt extra mogelijkheden voor het beperken van de laag basis service die specifiek zijn afgestemd op Azure Virtual Network-Resources. 

DDoS Protection Standard is eenvoudig in te scha kelen en vereist geen wijzigingen in de toepassing. Beveiligingsbeleid is afgestemd door middel van specifieke controle van verkeer en algoritmen voor machine learning. Beleids regels worden toegepast op open bare IP-adressen die zijn gekoppeld aan resources die zijn geïmplementeerd in virtuele netwerken, zoals Azure Load Balancer, Azure-toepassing gateway en Azure Service Fabric instances, maar deze beveiliging is niet van toepassing op App Service omgevingen.Realtime-telemetrie is beschikbaar via Azure Monitor weergaven tijdens een aanval en voor geschiedenis. Uitgebreide aanvals beperkings analyse zijn beschikbaar via Diagnostische instellingen. U kunt de beveiliging van de toepassingslaag toevoegen via de [Azure-toepassing gateway Web Application firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of door een firewall van een derde partij te installeren vanuit Azure Marketplace. Beveiliging is beschikbaar voor IPv4-en IPv6 Azure [open bare IP-adressen](virtual-network-public-ip-address.md).

![DDoS Protection Basic versus standaard](./media/ddos-protection-overview/ddoscomparison.png)

Azure DDoS Protection slaat geen klant gegevens op.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typen DDoS-aanvallen die standaard oplossingen DDoS Protection

DDoS Protection Standard kan de volgende typen aanvallen beperken:

- **Volumetrische aanvallen**: deze aanvallen flooden de netwerklaag met een aanzienlijke hoeveelheid schijnbaar betrouwbaar verkeer. Ze omvatten UDP-flooden, versterking van stromen en andere vervalste pakket stromen. DDoS Protection Standard verkleint deze potentiële multi-Gigabyte-aanvallen door ze te absorberen en te reinigen, met de wereld wijde schaal van Azure automatisch.
- **Protocol aanvallen**: deze aanvallen genereren een doel dat niet toegankelijk is door een zwakte te misbruiken in de Layer 3-en Layer 4-protocol stack. Ze omvatten SYN-flood-aanvallen, reflectie-aanvallen en andere protocol aanvallen. DDoS Protection Standard verkleint deze aanvallen, onderscheidt zich van schadelijk en betrouwbaar verkeer door interactie met de client en het blok keren van schadelijk verkeer. 
- **Resource (Application) Layer-aanvallen**: deze aanvallen richten op webtoepassingen, om de overdracht van gegevens tussen hosts te verstoren. Ze omvatten HTTP-protocol schendingen, SQL-injectie, cross-site scripting en andere Layer 7-aanvallen. Gebruik een firewall voor webtoepassingen, zoals de Azure [Application Gateway Web Application firewall](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), en DDoS Protection standaard om bescherming tegen deze aanvallen te bieden. Er zijn ook Web Application Firewall aanbiedingen van derden beschikbaar in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS Protection Standard beveiligt bronnen in een virtueel netwerk, met inbegrip van open bare IP-adressen die zijn gekoppeld aan virtuele machines, load balancers en toepassings gateways. Wanneer de Application Gateway Web Application Firewall, of een Web Application Firewall van derden die in een virtueel netwerk met een openbaar IP-adres is geïmplementeerd, kan DDoS Protection Standard de mogelijkheid bieden om een volledige laag 3 te maken voor de beperking van laag 7.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standaard functies

![DDoS-functionaliteit](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection standaard functies zijn:

- **Systeem eigen platform integratie:** Systeem eigen geïntegreerd in Azure. Bevat configuratie via de Azure Portal. DDoS Protection standaard begrijpt uw resources en resource configuratie.
- Kant-en- **klare beveiliging:** Vereenvoudigde configuratie beveiligt onmiddellijk alle resources in een virtueel netwerk zodra DDoS Protection standaard is ingeschakeld. Er is geen interventie-of gebruikers definitie vereist. DDoS Protection Standard onmiddellijk en vermindert de aanval automatisch, zodra deze is gedetecteerd.
- **Bewaking over altijd verkeer:** Uw toepassings verkeers patronen worden 24 uur per dag, 7 dagen per week gecontroleerd en er wordt gezocht naar indica toren van DDoS-aanvallen. De beperking wordt toegepast wanneer het beveiligings beleid wordt overschreden.
- **Adaptieve afstemming:** Intelligent verkeer profile ring leert het verkeer van uw toepassing gedurende een bepaalde periode en selecteert en werkt het profiel dat het meest geschikt is voor uw service. Het profiel wordt aangepast naarmate het verkeer na verloop van tijd verandert.
- **Beveiliging met meerdere lagen:** Biedt volledige stack DDoS-beveiliging, wanneer deze met een Web Application Firewall wordt gebruikt.
- **Uitgebreide beperkings schaal:** Meer dan 60 verschillende typen aanvallen kunnen worden gereduceerd, met globale capaciteit om te beschermen tegen de grootste bekende DDoS-aanvallen.
- **Aanvals analyse:** Ontvang gedetailleerde rapporten in stappen van vijf minuten tijdens een aanval en een volledig overzicht nadat de aanval is beëindigd. Stroom logboeken voor risico beperking naar [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection) of offline Security Information en Event Management (Siem) System voor bijna realtime-bewaking tijdens een aanval.
- **Maat staven voor aanvallen:** Een overzicht van de metrische gegevens van elke aanval is toegankelijk via Azure Monitor.
- **Waarschuwing voor aanvallen:** Waarschuwingen kunnen worden geconfigureerd bij het starten en stoppen van een aanval en over de duur van de aanval, met behulp van ingebouwde aanvals waarden. Waarschuwingen worden geïntegreerd in uw operationele software, zoals Microsoft Azure controle logboeken, Splunk, Azure Storage, E-mail en de Azure Portal.
- **Kosten garantie:** Service-tegoeden voor gegevens overdracht en toepassingen voor gedocumenteerde DDoS-aanvallen.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection standaard beperking

DDoS Protection Standard bewaakt het werkelijke verkeers gebruik en vergelijkt deze met de drempel waarden die zijn gedefinieerd in het DDoS-beleid. Wanneer de drempel waarde voor verkeer wordt overschreden, wordt de DDoS-beperking automatisch gestart. Wanneer het verkeer onder de drempel waarde komt, wordt de risico beperking verwijderd.

![Oplossing](./media/ddos-protection-overview/mitigation.png)

Tijdens de risico beperking wordt het verkeer dat wordt verzonden naar de beveiligde bron, omgeleid door de DDoS Protection-Service en worden er diverse controles uitgevoerd, zoals de volgende controles:

- Zorg ervoor dat de pakketten voldoen aan de Internet specificaties en niet zijn beschadigd.
- Communiceer met de client om te bepalen of het verkeer mogelijk een vervalst pakket is (bijvoorbeeld: SYN auth of SYN cookie of door een pakket voor de bron te verwijderen om het opnieuw te verzenden).
- Aantal pakketten beperken als er geen andere afdwing methode kan worden uitgevoerd.

DDoS-beveiliging blokkeert aanvalsverkeer en stuurt het resterende verkeer door naar de beoogde bestemming. Binnen een paar minuten na detectie van aanvallen wordt u op de hoogte gebracht met behulp van metrische gegevens van Azure Monitor. Door logboek registratie in DDoS Protection standaard-telemetrie te configureren, kunt u de logboeken schrijven naar beschik bare opties voor toekomstige analyse. Metrische gegevens in Azure Monitor voor DDoS Protection standaard worden 30 dagen bewaard.

Micro soft heeft een samen werking met [BreakingPoint-Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) voor het bouwen van een interface waarmee u verkeer kunt genereren op basis van DDoS Protection open bare IP-adressen voor simulaties. Met de Cloud simulatie onderbrekings punt kunt u:

- Valideer hoe Microsoft Azure DDoS Protection Standard uw Azure-resources beveiligt tegen DDoS-aanvallen
- Optimaliseer uw respons proces van uw incidenten door onder DDoS-aanval
- Conformiteit document DDoS
- Uw netwerk beveiligings teams trainen

## <a name="next-steps"></a>Volgende stappen

- [DDoS Protection Standard configureren](manage-ddos-protection.md)
- [Azure DDoS Protection prijzen](https://azure.microsoft.com/pricing/details/ddos-protection/)
