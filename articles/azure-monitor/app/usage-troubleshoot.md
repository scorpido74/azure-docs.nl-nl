---
title: Gebruikersanalysehulpprogramma's oplossen - Azure Application Insights
description: Handleiding voor probleemoplossing - het analyseren van het gebruik van site en app met Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670913"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Problemen met analysetools voor gebruikersgedrag oplossen in Application Insights
Heeft u vragen over de analysetools voor [gebruikersgedrag in Application Insights:](usage-overview.md) [Gebruikers, sessies, gebeurtenissen,](usage-segmentation.md) [trechters,](usage-funnels.md) [gebruikersstromen,](usage-flows.md) [retentie](usage-retention.md)of cohorten? Hier zijn enkele antwoorden.

## <a name="counting-users"></a>Gebruikers tellen
**De analysetools voor gebruikersgedrag laten zien dat mijn app één gebruiker/sessie heeft, maar ik weet dat mijn app veel gebruikers/sessies heeft. Hoe kan ik deze onjuiste tellingen oplossen?**

Alle telemetriegebeurtenissen in Application Insights hebben een [anonieme gebruikers-id](../../azure-monitor/app/data-model-context.md) en een [sessie-id](../../azure-monitor/app/data-model-context.md) als twee van hun standaardeigenschappen. Standaard tellen alle gebruiksanalysetools gebruikers en sessies op basis van deze id's. Als deze standaardeigenschappen niet worden gevuld met unieke id's voor elke gebruiker en sessie van uw app, ziet u een onjuist aantal gebruikers en sessies in de hulpprogramma's voor gebruiksanalyse.

Als u een web-app controleert, is de eenvoudigste oplossing om de [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) aan uw app toe te voegen en ervoor te zorgen dat het scriptfragment wordt geladen op elke pagina die u wilt controleren. De JavaScript SDK genereert automatisch anonieme gebruikers- en sessie-id's en vult vervolgens telemetriegebeurtenissen met deze id's wanneer ze vanuit uw app worden verzonden.

Als u een webservice (geen gebruikersinterface) bewaakt, [maakt u een telemetrieinitialisator die de anonieme gebruikers-id- en sessie-id-eigenschappen vult](usage-send-user-context.md) volgens de noties van uw service met unieke gebruikers en sessies.

Als uw app [geverifieerde gebruikersnamen](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)verzendt, u rekenen op basis van geverifieerde gebruikersnamen in het hulpprogramma Gebruikers. Kies in de vervolgkeuzelijst 'Weergeven' de optie 'Geverifieerde gebruikers'.

De analysetools voor gebruikersgedrag ondersteunen momenteel geen het tellen van gebruikers of sessies op basis van andere eigenschappen dan anonieme gebruikers-id, geverifieerde gebruikersnaam of sessie-id.

## <a name="naming-events"></a>Gebeurtenissen een naam geven
**Mijn app heeft duizenden verschillende paginaweergave en aangepaste gebeurtenisnamen. Het is moeilijk om onderscheid te maken tussen hen, en de gebruiker gedrag analytics tools vaak niet reageren. Hoe kan ik deze naamgevingsproblemen oplossen?**

Paginaweergave en aangepaste gebeurtenisnamen worden gebruikt in de analysetools voor gebruikersgedrag. Het goed benoemen van gebeurtenissen is essentieel om waarde te halen uit deze tools. Het doel is een evenwicht tussen het hebben van te weinig, overdreven generieke namen ("Button clicked") en\/het hebben van te veel, overdreven specifieke namen ("Edit knop geklikt op http: /www.contoso.com/index").

Als u wijzigingen wilt aanbrengen in de paginaweergave en de aangepaste gebeurtenisnamen die uw app verzendt, moet u de broncode van uw app wijzigen en opnieuw implementeren. **Alle telemetriegegevens in Application Insights worden 90 dagen bewaard en kunnen niet worden verwijderd,** dus het duurt 90 dagen voordat wijzigingen in gebeurtenisnamen zijn aangebracht. Gedurende de 90 dagen na het aanbrengen van naamswijzigingen worden zowel de oude als de nieuwe gebeurtenisnamen weergegeven in uw telemetrie, dus pas query's aan en communiceer binnen uw teams.

Als uw app te veel namen voor paginaweergave verzendt, controleert u of deze paginaweergavenamen handmatig in code zijn opgegeven of dat ze automatisch worden verzonden door de JavaScript SDK van Application Insights:

* Als de namen van de paginaweergave handmatig zijn opgegeven in code met behulp van de [ `trackPageView` API,](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)wijzigt u de naam als minder specifiek. Vermijd veelvoorkomende fouten, zoals het plaatsen van de URL in de naam van de paginaweergave. Gebruik in plaats daarvan `trackPageView` de PARAMETER URL van de API. Andere details van de naam van de paginaweergave verplaatsen naar aangepaste eigenschappen.

* Als de Application Insights JavaScript SDK automatisch paginaweergavenamen verzendt, u de titels van uw pagina's wijzigen of overschakelen naar het handmatig verzenden van paginaweergavenamen. De SDK verzendt de [titel](https://developer.mozilla.org/docs/Web/HTML/Element/title) van elke pagina standaard als de naam van de paginaweergave. Je zou kunnen veranderen uw titels om meer algemeen, maar rekening houden met SEO en andere effecten van deze verandering zou kunnen hebben. Als u namen voor paginaweergave handmatig opgeeft met de `trackPageView` API, worden de automatisch verzamelde namen overschreven, zodat u meer algemene namen in telemetrie verzenden zonder paginatitels te wijzigen.   

Als uw app te veel aangepaste gebeurtenisnamen verzendt, wijzigt u de naam in de code om minder specifiek te zijn. Nogmaals, vermijd url's en andere pagina' of dynamische informatie rechtstreeks in de aangepaste gebeurtenisnamen te plaatsen. Verplaats deze gegevens in plaats daarvan naar `trackEvent` aangepaste eigenschappen van de aangepaste gebeurtenis met de API. Bijvoorbeeld, in `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`plaats van, `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`stellen we iets als .

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van analysetools voor gebruikersgedrag](usage-overview.md)

## <a name="get-help"></a>Help opvragen
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

