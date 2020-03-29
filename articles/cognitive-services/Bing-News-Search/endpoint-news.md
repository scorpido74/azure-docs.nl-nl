---
title: Bing Nieuws zoeken-eindpunten
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een overzicht van de API-eindpunten voor nieuwszoeken; nieuws, topnieuws en trending nieuws.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111478"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API eindpunten

De **News Search API** retourneert nieuwsartikelen, webpagina's, afbeeldingen, video's en [entiteiten.](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) Entiteiten bevatten beknopte informatie over een persoon, plaats of onderwerp.

## <a name="endpoints"></a>Eindpunten

Als u zoekresultaten wilt ontvangen met de `GET` Bing News Search API, stuurt u een aanvraag naar een van de volgende eindpunten. De headers en URL parameters definiëren verdere specificaties.

### <a name="news-items-by-search-query"></a>Nieuwsitems per zoekopdracht

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retourneert nieuwsberichten op basis van een zoekopdracht. Als de zoekopdracht leeg is, retourneert de API de belangrijkste nieuwsartikelen uit verschillende categorieën. Stuur een query door url codering van uw`q=""` zoekterm en toe te passen aan de parameter. Zie [Ondersteunde landen/regio's en markten voor](language-support.md#supported-markets-for-news-search-endpoint)beschikbaarheid .

### <a name="top-news-items-by-category"></a>Belangrijkste nieuwsobjecten per rubriek

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Geeft als resultaat de belangrijkste nieuwsitems per rubriek. U specifiek de topbusiness, sport- `category=business`of `category=sports`entertainmentartikelen aanvragen met behulp van, of `category=entertainment`. De `category` parameter kan alleen `/news` worden gebruikt met de URL. Er zijn enkele formele vereisten voor het specificeren van categorieën; verwijzen `category` naar in de query [parameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) documentatie. Stuur een query door url codering van uw`q=""` zoekterm en toe te passen aan de parameter. Zie [Ondersteunde landen/regio's en markten voor](language-support.md#supported-markets-for-news-endpoint)beschikbaarheid .

### <a name="trending-news-topics"></a>Trending nieuwsonderwerpen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Geeft als resultaat nieuwsonderwerpen die momenteel trending zijn op sociale netwerken. Wanneer `/trendingtopics` de optie is opgenomen, negeert Bing-zoekopdracht verschillende andere parameters, zoals `freshness` en `?q=""`. Zie [Ondersteunde landen/regio's en markten voor](language-support.md#supported-markets-for-news-trending-endpoint)beschikbaarheid .

## <a name="next-steps"></a>Volgende stappen

Zie de referentie van de [Bing News search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten, enz..

Zie de referentiepagina's voor elk type voor volledige informatie over de parameters die door elk eindpunt worden ondersteund.
Zie [Snelstarts](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)voor zoeken in Het nieuws voor voorbeelden van basisaanvragen die gebruikmaken van de API voor het zoeken naar nieuws .
