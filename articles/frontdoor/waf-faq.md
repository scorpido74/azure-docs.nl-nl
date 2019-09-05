---
title: Veelgestelde vragen over Azure Web Application Firewall
description: Op deze pagina vindt u antwoorden op veelgestelde vragen over de Azure front-deur service
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f7d205920704ce8aedf3b2e3c07bd429b3c64964
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375351"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Veelgestelde vragen over Azure Web Application Firewall

In dit artikel vindt u antwoorden op veelgestelde vragen over de functies en functionaliteit van Azure Web Application Firewall (WAF). 

## <a name="what-is-azure-waf"></a>Wat is Azure WAF?

Azure WAF is een Web Application Firewall waarmee u uw webtoepassingen kunt beschermen tegen veelvoorkomende bedreigingen, zoals SQL-injectie, cross-site scripting en andere webexploits. U kunt een WAF-beleid definiëren dat bestaat uit een combi natie van aangepaste en beheerde regels voor het beheren van de toegang tot uw webtoepassingen.

Een Azure WAF-beleid kan worden toegepast op webtoepassingen die worden gehost op Application Gateway of Azure front-deur Services.

## <a name="what-is-waf-for-azure-front-door-service"></a>Wat is WAF voor de Azure front-deur service? 

Azure front-deur is een zeer schaal bare, wereld wijd gedistribueerde toepassing en Content Delivery Network. Wanneer u Azure WAF hebt geïntegreerd met de voor deur, worden denial-of-service-en gerichte toepassings aanvallen op de Azure-rand van het netwerk gestopt, sluit u af op aanvals bronnen voordat ze het virtuele netwerk binnengaan, wordt er beveiliging geboden zonder dat de prestaties worden aangetast.

## <a name="does-azure-waf-support-https"></a>Ondersteunt Azure WAF HTTPS?

De front-deur service biedt SSL-offloading. WAF is systeem eigen geïntegreerd met de voor deur en kan een aanvraag controleren nadat deze is ontsleuteld.

## <a name="does-azure-waf-support-ipv6"></a>Biedt Azure WAF ondersteuning voor IPv6?

Ja. U kunt de IP-beperking voor IPv4 en IPv6 configureren.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hoe up-to-date zijn de beheerde regel sets?

We doen ons best om te blijven werken met het wijzigen van bedreigings landschap. Zodra een nieuwe regel is bijgewerkt, wordt deze toegevoegd aan de standaardregelset met een nieuw versie nummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Wat is de doorgifte tijd als ik een wijziging aanbreng in mijn WAF-beleid?

Het implementeren van een WAF-beleid wordt doorgaans ongeveer 5 minuten in beslag genomen en is vaak eerder voltooid.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kunnen WAF-beleids regels voor verschillende regio's verschillen?

Wanneer het is geïntegreerd met de voor deur-service, is WAF een wereld wijde bron. Dezelfde configuratie is van toepassing op alle locaties van de voor deur.
 

## <a name="which-azure-waf-options-should-i-choose"></a>Welke opties voor Azure WAF moet ik kiezen?

Er zijn twee opties voor het Toep assen van WAF-beleid in Azure. WAF met Azure front deur is een wereld wijd gedistribueerde, Edge-beveiligings oplossing. WAF met Application Gateway is een regionale, toegewezen oplossing. We raden u aan een oplossing te kiezen op basis van uw algemene prestatie-en beveiligings vereisten. Zie [taak verdeling met de Application Delivery Suite van Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)voor meer informatie.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Biedt u ondersteuning voor dezelfde WAF-functies in alle geïntegreerde platformen?

Momenteel worden ModSec CRS 2.2.9 en CRS 3,0-regels alleen ondersteund met WAF bij Application Gateway. Regels voor het beperken van beperkingen, geo-filtering en Azure Managed Rule set worden alleen ondersteund met WAF in azure front-deur.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Is DDoS Protection geïntegreerd met de voor deur? 

Wereld wijd gedistribueerd op Azure-netwerk randen kunnen grote volume aanvallen door de Azure front-deur worden geabsorbeerd en geografisch geïsoleerd. U kunt een aangepast WAF-beleid maken om te voor komen dat http (s)-aanvallen met bekende hand tekeningen automatisch worden geblokkeerd en beperkt. U kunt nog meer DDoS Protection Standard inschakelen op het VNet waar uw back-ends worden geïmplementeerd. Azure DDoS Protection Standard-klanten ontvangen extra voor delen, waaronder kosten beveiliging, SLA-garantie en toegang tot experts van DDoS Rapid Response-Team voor onmiddellijke ondersteuning tijdens een aanval. 

We raden u aan om uw back-ends in productie omgeving te vergren delen om het DDoS-aanvals oppervlak te verminderen. Zie [Hoe kan ik de toegang tot mijn back-end alleen vergren delen voor een Azure front-deur?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Web Application firewall](waf-overview.md).
- Meer informatie over de [voor deur van Azure](front-door-overview.md).
