---
title: Wat is de Bing Webzoekopdrachten-API?
titleSuffix: Azure Cognitive Services
description: De Bing Web Search API is een RESTful-service die direct antwoord geeft op zoekopdrachten op het web. Configureer resultaten met webpagina's, afbeeldingen, video's, nieuws en meer. Resultaten worden gegeven als JSON en zijn gebaseerd op zoekrelevantie en uw Bing Webzoekopdrachten-abonnementen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 5ea98a4ef759937c5962dc86f0384051b8fa0bb3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477461"
---
# <a name="what-is-the-bing-web-search-api"></a>Wat is de Bing Webzoekopdrachten-API?

De Bing Webzoekopdrachten-API is een RESTful-service die directe antwoorden op gebruikersquery's biedt. Zoekresultaten kunnen eenvoudig zo worden geconfigureerd dat deze webpagina's, afbeeldingen, video's, nieuws, vertalingen en meer bevatten. Bing Web Search biedt de resultaten als JSON op basis van de relevantie van zoekopdrachten en uw Bing Web Search-abonnementen.

Deze API werkt optimaal voor toepassingen die toegang moeten hebben tot alle inhoud die relevant is voor de zoekquery van een gebruiker. Als u een toepassing maakt waarvoor alleen een specifiek type resultaat nodig is, kunt u ook de [Bing Afbeeldingen zoeken-API](../Bing-Image-Search/overview.md), [Bing Video's zoeken-API](../Bing-Video-Search/search-the-web.md) of [Bing Nieuws zoeken-API](../Bing-News-Search/search-the-web.md) gebruiken. Zie [Cognitive Services-API's](https://docs.microsoft.com/azure/cognitive-services) voor een volledige lijst met de Bing Zoeken-API's.

Wilt u zien hoe dit werkt? Probeer onze [Demo voor de Bing Webzoekopdrachten-API](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Functies  

Bing Web Search geeft u niet alleen toegang tot directe antwoorden. Het biedt ook extra functies en functionaliteit waarmee u zoekresultaten voor uw gebruikers aanpassen.

| Functie | Beschrijving |
|---------|-------------|
| [Zoektermen in realtime voorstellen](../bing-autosuggest/get-suggested-search-terms.md) | Verbeter uw toepassingservaring met de Bing Automatische suggestie-API om voorgestelde zoektermen weer te geven wanneer deze worden getypt. |
| [Resultaten filteren en beperken op inhoudstype](filter-answers.md) | Pas zoekresultaten aan en verfijn deze met filters en queryparameters voor webpagina's, afbeeldingen, video's, veilig zoeken en meer. |
| [Treffers markeren voor Unicode-tekens](hit-highlighting.md) | Identificeer en verwijder ongewenste Unicode-tekens uit de zoekresultaten voordat deze worden weergegeven voor gebruikers met de functie voor het markeren van treffers. |
| [Lokaliseer de zoekresultaten op land, regio en/of markt](supported-countries-markets.md) | Bing Webzoekopdrachten ondersteunt meer dan 30 landen of regio's. Gebruik deze functie om zoekresultaten te verfijnen voor een specifiek land/specifieke regio of markt. |
| [Metrische zoekgegevens analyseren met Bing Statistieken](bing-web-stats.md) | Bing Statistieken is een betaald abonnement waarmee u analyses van het aanroepvolume, de belangrijkste queryreeksen en de geografische verdeling kunt bekijken. |

## <a name="workflow"></a>Werkstroom

De Bing Webzoekopdrachten-API kan eenvoudig worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON-antwoorden kunnen worden geparseerd. De service is toegankelijk via de [REST API](quickstarts/python.md) of de Bing [Web Search-clientbibliotheken.](./quickstarts/client-libraries.md)

1. [Maak een Azure-bron](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor de Bing Search API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) maken.  
2. Verzend een [aanvraag naar de Bing Webzoekopdrachten-API](quickstarts/python.md).
3. Parseer het JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze [Python-snelstartgids](./quickstarts/client-libraries.md?pivots=programming-language-python) om uw eerste aanroep naar de Bing Webzoekopdrachten-API te maken.  
* [Maak een web-app met één pagina](tutorial-bing-web-search-single-page-app.md).
* Bekijk de documentatie [Referentie voor Webzoekopdrachten-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Meer informatie over [Vereisten voor gebruik en weergave](UseAndDisplayRequirements.md) voor Bing Webzoekopdrachten.  
