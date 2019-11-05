---
title: Firewall voor Azure Web Application-Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Web Application firewall op de Azure front-deur service
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: 263f929fa010450fcfb35b8db74f94cd6fd7dc84
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517174"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Veelgestelde vragen over Azure Web Application firewall op de Azure front-deur service

In dit artikel vindt u antwoorden op veelgestelde vragen over de functies en functionaliteit van Azure Web Application Firewall (WAF). 

## <a name="what-is-azure-waf"></a>Wat is Azure WAF?

Azure WAF is een Web Application Firewall waarmee u uw webtoepassingen kunt beschermen tegen veelvoorkomende bedreigingen, zoals SQL-injectie, cross-site scripting en andere webexploits. U kunt een WAF-beleid definiëren dat bestaat uit een combi natie van aangepaste en beheerde regels voor het beheren van de toegang tot uw webtoepassingen.

Een Azure WAF-beleid kan worden toegepast op webtoepassingen die worden gehost op Application Gateway of Azure front-deur Services.

## <a name="what-is-waf-on-azure-front-door-service"></a>Wat is WAF in de Azure front-deur service? 

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
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hoe kan ik de toegang tot mijn back-end beperken tot alleen de voor deur?

U kunt de IP-Access Control lijst in uw back-end configureren om alleen uitgaande IP-adresbereiken voor de voor deur toe te staan en directe toegang vanaf internet te weigeren. Service tags worden ondersteund voor gebruik in uw virtuele netwerk. Daarnaast kunt u controleren of het veld X-doorgestuurd-host-HTTP-header geldig is voor uw webtoepassing.

## <a name="which-azure-waf-options-should-i-choose"></a>Welke opties voor Azure WAF moet ik kiezen?

Er zijn twee opties voor het Toep assen van WAF-beleid in Azure. WAF met Azure front deur is een wereld wijd gedistribueerde, Edge-beveiligings oplossing. WAF met Application Gateway is een regionale, toegewezen oplossing. We raden u aan een oplossing te kiezen op basis van uw algemene prestatie-en beveiligings vereisten. Zie [taak verdeling met de Application Delivery Suite van Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)voor meer informatie.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Biedt u ondersteuning voor dezelfde WAF-functies in alle geïntegreerde platformen?

Momenteel worden ModSec CRS 2.2.9, CRS 3,0 en CRS 3,1-regels alleen ondersteund met WAF op Application Gateway. Regels voor het beperken van beperkingen, geo-filtering en Azure Managed Rule set worden alleen ondersteund met WAF in azure front-deur.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Is DDoS Protection geïntegreerd met de voor deur? 

Wereld wijd gedistribueerd op Azure-netwerk randen kunnen grote volume aanvallen door de Azure front-deur worden geabsorbeerd en geografisch geïsoleerd. U kunt een aangepast WAF-beleid maken om te voor komen dat http (s)-aanvallen met bekende hand tekeningen automatisch worden geblokkeerd en beperkt. U kunt nog meer DDoS Protection Standard inschakelen op het VNet waar uw back-ends worden geïmplementeerd. Azure DDoS Protection Standard-klanten ontvangen extra voor delen, waaronder kosten beveiliging, SLA-garantie en toegang tot experts van DDoS Rapid Response-Team voor onmiddellijke ondersteuning tijdens een aanval.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Waarom worden extra aanvragen boven de drempel waarde die is geconfigureerd voor de regel voor de frequentie limiet, aan mijn back-endserver door gegeven?

Een frequentie limiet regel kan abnormaal hoog verkeer beperken van elk client-IP-adres. U kunt een drempel waarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP-adres tijdens een duur van één minuut of vijf minuten. Voor een granulaire frequentie controle kan de frequentie limiet worden gecombineerd met aanvullende match-voor waarden, zoals HTTP (S)-para meters die overeenkomen. 

Aanvragen van dezelfde client komen vaak op dezelfde front-deur server voor. In dat geval ziet u extra aanvragen boven de drempel waarde onmiddellijk geblokkeerd. 

Het is echter mogelijk dat aanvragen van dezelfde client kunnen aankomen op een andere front-deur server die de teller voor frequentie limiet nog niet heeft vernieuwd. De client kan bijvoorbeeld een nieuwe verbinding voor elke aanvraag openen en de drempel waarde is laag. In dit geval wordt de frequentie limiet door de eerste aanvraag voor de nieuwe front-deur server door gegeven. Een drempel waarde voor frequentie limieten wordt doorgaans hoog ingesteld om te voor komen dat denial of service-aanvallen van elk client-IP-adres worden uitgevoerd. Voor een zeer lage drempel waarde ziet u mogelijk extra aanvragen boven de drempel waarde.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [firewall van Azure Web Application](../overview.md).
- Meer informatie over de [voor deur van Azure](../../frontdoor/front-door-overview.md).
