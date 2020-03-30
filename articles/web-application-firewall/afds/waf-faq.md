---
title: Azure Web Application Firewall - Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Web Application Firewall op Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460633"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Veelgestelde vragen voor Azure Web Application Firewall op Azure Front Door Service

In dit artikel worden veelgestelde vragen beantwoord over WAF-functies en -functionaliteit (Azure Web Application Firewall). 

## <a name="what-is-azure-waf"></a>Wat is Azure WAF?

Azure WAF is een firewall voor webtoepassingen die uw webtoepassingen beschermt tegen veelvoorkomende bedreigingen zoals SQL-injectie, cross-site scripting en andere webexploits. U een WAF-beleid definiëren dat bestaat uit een combinatie van aangepaste en beheerde regels om de toegang tot uw webtoepassingen te beheren.

Een Azure WAF-beleid kan worden toegepast op webtoepassingen die worden gehost op Application Gateway of Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>Wat is WAF op Azure Front Door? 

Azure Front Door is een zeer schaalbaar, wereldwijd gedistribueerd netwerk voor het leveren van applicaties en inhoud. Azure WAF stopt, wanneer het is geïntegreerd met Front Door, met denial-of-service en gerichte toepassingsaanvallen op de Azure-netwerkrand, dicht bij aanvalsbronnen voordat ze uw virtuele netwerk binnenkomen, bescherming bieden zonder dat dit ten koste gaat van de prestaties.

## <a name="does-azure-waf-support-https"></a>Ondersteunt Azure WAF HTTPS?

Front Door biedt SSL offloading. WAF is native geïntegreerd met Front Door en kan een aanvraag inspecteren nadat het is gedecodeerd.

## <a name="does-azure-waf-support-ipv6"></a>Ondersteunt Azure WAF IPv6?

Ja. U IP-beperking configureren voor IPv4 en IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hoe up-to-date zijn de beheerde regelsets?

We doen ons best om gelijke tred te houden met veranderende dreiginglandschap. Zodra een nieuwe regel is bijgewerkt, wordt deze toegevoegd aan de standaardregelset met een nieuw versienummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Wat is de voortplantingstijd als ik mijn WAF-beleid wijzig?

Het wereldwijd implementeren van een WAF-beleid duurt meestal ongeveer 5 minuten en wordt vaak eerder voltooid.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kan het WAF-beleid voor verschillende regio's anders zijn?

Wanneer geïntegreerd met Front Door, WAF is een wereldwijde bron. Dezelfde configuratie is van toepassing op alle frontdeurlocaties.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hoe beperk ik de toegang tot mijn back-end om alleen van front door te zijn?

U de IP Access Control List in uw back-end configureren om alleen uitgaande IP-adresbereiken voor de voordeur mogelijk te maken en directe toegang vanaf internet te weigeren. Servicetags worden ondersteund voor gebruik op uw virtuele netwerk. Bovendien u controleren of het http-headerveld X-Forwarded-Host geldig is voor uw webtoepassing.

## <a name="which-azure-waf-options-should-i-choose"></a>Welke Azure WAF-opties moet ik kiezen?

Er zijn twee opties bij het toepassen van WAF-beleidsregels in Azure. WAF met Azure Front Door is een wereldwijd gedistribueerde beveiligingsoplossing voor rand. WAF met Application Gateway is een regionale, specifieke oplossing. We raden u aan een oplossing te kiezen op basis van uw algemene prestatie- en beveiligingsvereisten. Zie [Load-balancing met azure's application delivery suite](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)voor meer informatie.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Ondersteunt u dezelfde WAF-functies in alle geïntegreerde platforms?

Momenteel worden ModSec CRS 2.2.9- en CRS 3.0- en CRS 3.1-regels alleen ondersteund met WAF op Application Gateway. Regels voor tariefbeperkende, geofiltering en Azure managed Default Rule Set worden alleen ondersteund met WAF op Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Is DDoS-bescherming geïntegreerd met voordeur? 

Azure Front Door is wereldwijd gedistribueerd op Azure-netwerkranden en kan grote volumeaanvallen opnemen en geografisch isoleren. U aangepast WAF-beleid maken om http-aanvallen met bekende handtekeningen automatisch te blokkeren en te beperken. Verder u DDoS Protection Standard inschakelen op het VNet, waar uw back-ends worden ingezet. Azure DDoS Protection Standard-klanten ontvangen extra voordelen, waaronder kostenbescherming, SLA-garantie en toegang tot experts van DDoS Rapid Response Team voor onmiddellijke hulp tijdens een aanval.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Waarom worden aanvullende aanvragen boven de drempelwaarde die is geconfigureerd voor mijn tarieflimietregel doorgegeven aan mijn backendserver?

Een tarieflimietregel kan abnormaal veel verkeer beperken vanaf elk IP-adres van de client. U een drempelwaarde configureren voor het aantal webaanvragen dat is toegestaan vanaf een IP-adres van een client gedurende een duur van één minuut of vijf minuten. Voor granulaire tariefregeling kan tariefbeperking worden gecombineerd met aanvullende wedstrijdvoorwaarden, zoals http(S)-parametermatching. 

Aanvragen van dezelfde client komen vaak bij dezelfde Front Door-server. In dat geval ziet u dat extra aanvragen boven de drempelwaarde onmiddellijk worden geblokkeerd. 

Het is echter mogelijk dat aanvragen van dezelfde client kunnen aankomen bij een andere Front Door-server die de limietteller nog niet heeft vernieuwd. De client kan bijvoorbeeld voor elke aanvraag een nieuwe verbinding openen en de drempelwaarde is laag. In dit geval zou het eerste verzoek aan de nieuwe Front Door-server de tarieflimietcontrole passeren. Een drempelwaarde voor tariefgrenzen is meestal hoog ingesteld om te beschermen tegen denial of service-aanvallen vanaf een IP-adres van de client. Voor een zeer lage drempelwaarde ziet u mogelijk extra aanvragen boven de drempelwaarde worden doorstaan.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Web Application Firewall](../overview.md).
- Meer informatie over [Azure Front Door](../../frontdoor/front-door-overview.md).
