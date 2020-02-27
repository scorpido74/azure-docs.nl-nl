---
title: Wat is de Bing Webzoekopdrachten-API?
titleSuffix: Azure Cognitive Services
description: De Bing Webzoekopdrachten-API is een RESTful-service die directe antwoorden op gebruikersquery's biedt. Zoekresultaten kunnen eenvoudig zo worden geconfigureerd dat deze webpagina's, afbeeldingen, video's, nieuws, vertalingen en meer bevatten. Resultaten worden gegeven als JSON en zijn gebaseerd op zoekrelevantie en uw Bing Webzoekopdrachten-abonnementen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: a7b2627b5837a124ebbcd76783bb49679cbe6313
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650279"
---
# <a name="what-is-the-bing-web-search-api"></a>Wat is de Bing Webzoekopdrachten-API?

De Bing Webzoekopdrachten-API is een RESTful-service die directe antwoorden op gebruikersquery's biedt. Zoekresultaten kunnen eenvoudig zo worden geconfigureerd dat deze webpagina's, afbeeldingen, video's, nieuws, vertalingen en meer bevatten. Bing Web Search levert de resultaten als JSON op basis van zoek relevantie en uw Bing Web Search-abonnementen.

Deze API werkt optimaal voor toepassingen die toegang moeten hebben tot alle inhoud die relevant is voor de zoekquery van een gebruiker. Als u een toepassing maakt waarvoor alleen een specifiek type resultaat nodig is, kunt u ook de [Bing Afbeeldingen zoeken-API](../Bing-Image-Search/overview.md), [Bing Video's zoeken-API](../Bing-Video-Search/search-the-web.md) of [Bing Nieuws zoeken-API](../Bing-News-Search/search-the-web.md) gebruiken. Zie [Cognitive Services-API's](https://docs.microsoft.com/azure/cognitive-services) voor een volledige lijst met de Bing Zoeken-API's.

Wilt u zien hoe dit werkt? Probeer onze [Demo voor de Bing Webzoekopdrachten-API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Functies  

Bing Web Search biedt u geen toegang tot direct antwoord. Het bevat ook aanvullende functies en functionaliteit waarmee u de zoek resultaten voor uw gebruikers kunt aanpassen.

| Functie | Beschrijving |
|---------|-------------|
| [Zoektermen in realtime voorstellen](../bing-autosuggest/get-suggested-search-terms.md) | Verbeter uw toepassingservaring met de Bing Automatische suggestie-API om voorgestelde zoektermen weer te geven wanneer deze worden getypt. |
| [Resultaten filteren en beperken op inhoudstype](filter-answers.md) | Pas zoekresultaten aan en verfijn deze met filters en queryparameters voor webpagina's, afbeeldingen, video's, veilig zoeken en meer. |
| [Treffers markeren voor Unicode-tekens](hit-highlighting.md) | Identificeer en verwijder ongewenste Unicode-tekens uit de zoekresultaten voordat deze worden weergegeven voor gebruikers met de functie voor het markeren van treffers. |
| [Lokaliseer de zoekresultaten op land, regio en/of markt](supported-countries-markets.md) | Bing Webzoekopdrachten ondersteunt meer dan 30 landen of regio's. Gebruik deze functie om zoekresultaten te verfijnen voor een specifiek land/specifieke regio of markt. |
| [Metrische zoekgegevens analyseren met Bing Statistieken](bing-web-stats.md) | Bing Statistieken is een betaald abonnement waarmee u analyses van het aanroepvolume, de belangrijkste queryreeksen en de geografische verdeling kunt bekijken. |

## <a name="workflow"></a>Werkstroom

De Bing Webzoekopdrachten-API kan eenvoudig worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON-antwoorden kunnen worden geparseerd. De service is toegankelijk via de [rest API](quickstarts/python.md) -of de [Bing Web Search-client bibliotheken](./quickstarts/client-libraries.md).

1. [Maak een Azure-resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor de Bing zoeken-API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) maken.  
2. Verzend een [aanvraag naar de Bing Webzoekopdrachten-API](quickstarts/python.md).
3. Parseer het JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze [Python-snelstartgids](./quickstarts/client-libraries.md?pivots=programming-language-python) om uw eerste aanroep naar de Bing Webzoekopdrachten-API te maken.  
* [Maak een web-app van één pagina](tutorial-bing-web-search-single-page-app.md).
* Bekijk de documentatie [Referentie voor Webzoekopdrachten-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Meer informatie over [Vereisten voor gebruik en weergave](UseAndDisplayRequirements.md) voor Bing Webzoekopdrachten.  
