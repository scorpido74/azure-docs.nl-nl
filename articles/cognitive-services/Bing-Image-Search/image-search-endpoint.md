---
title: Eindpunten voor de Bing Image Search API
titleSuffix: Azure Cognitive Services
description: De Image Search API bevat drie eindpunten. Eindpunt 1 retourneert afbeeldingen van het web. Eindpunt 2 retourneert ImageInsights. Endpoint 3 retourneert trending images.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072632"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Eindpunten voor de Bing Image Search API

De **Image Search API** bevat drie eindpunten.  Eindpunt 1 retourneert afbeeldingen van het web op basis van een query. Eindpunt 2 retourneert [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Endpoint 3 retourneert trending images.

## <a name="endpoints"></a>Eindpunten

Als u afbeeldingsresultaten wilt krijgen met de Bing-API, stuurt u een aanvraag naar een van de volgende eindpunten. Gebruik de kopteksten en URL-parameters om verdere specificaties te definiëren.

**Eindpunt 1:** Geeft als resultaat afbeeldingen die relevant zijn voor `?q=""`de zoekopdracht van de gebruiker die is gedefinieerd door .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Eindpunt 2:** Geeft als resultaat inzichten `GET` over `POST`een afbeelding met behulp van een afbeelding of .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Een GET-aanvraag retourneert inzichten over een afbeelding, zoals webpagina's die de afbeelding bevatten. Neem de parameter [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) op bij een `GET` aanvraag.

U ook een binaire afbeelding opnemen `POST` in de hoofdtekst `RecognizedEntities`van een aanvraag en de parameter [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) instellen op. Hiermee wordt een [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) geretourneerd om als `GET` parameter te gebruiken in een volgende aanvraag, waarmee informatie over personen in de afbeelding wordt geretourneerd.  Ingesteld `modules` `All` op om alle `RecognizedEntities` inzichten te krijgen, behalve in de resultaten van de `POST` zonder een ander gesprek met behulp van de `insightsToken`.


**Eindpunt 3:** Geeft als resultaat afbeeldingen die trending zijn op basis van zoekverzoeken van anderen. De afbeeldingen zijn onderverdeeld in verschillende categorieën, bijvoorbeeld op basis van opmerkelijke personen of gebeurtenissen.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Zie [Trending Images](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)voor een lijst met markten die trending images ondersteunen.

Zie de referentie van de [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten, enz..
## <a name="response-json"></a>Reactie JSON
Het antwoord op een zoekaanvraag voor afbeeldingen bevat resultaten als JSON-objecten. Voor voorbeelden van ontwering van de resultaten zie de [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) en [broncode](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Volgende stappen
De **Bing** Bing-API's ondersteunen zoekacties die resultaten retourneren op basis van hun type.Alle zoekeindpunten retourneren resultaten als JSON-antwoordobjecten. Alle eindpunten ondersteunen query's die een specifieke taal en/of locatie retourneren op lengte, breedte- en zoekradius.

Zie de referentiepagina's voor elk type voor volledige informatie over de parameters die door elk eindpunt worden ondersteund.
Zie [Snelstarts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)voor image search voor voorbeelden van basisaanvragen met de Image search API.
