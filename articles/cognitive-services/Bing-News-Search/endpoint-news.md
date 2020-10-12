---
title: Bing Nieuws zoeken-eindpunten
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u een overzicht van de eind punten van de nieuws zoekopdracht-API. Nieuws, nieuws en frequente nieuws.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74111478"
---
# <a name="bing-news-search-api-endpoints"></a>Bing Nieuws zoeken-API-eind punten

De **Nieuws zoeken-API** retourneert nieuws artikelen, webpagina's, afbeeldingen, Video's en [entiteiten](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteiten bevatten samenvattings informatie over een persoon, plaats of onderwerp.

## <a name="endpoints"></a>Eindpunten

Als u zoek resultaten wilt ontvangen met behulp van de Bing Nieuws zoeken-API, verzendt `GET` u een aanvraag naar een van de volgende eind punten. De para meters headers en URL definiëren verdere specificaties.

### <a name="news-items-by-search-query"></a>Nieuws items per Zoek query

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Hiermee worden nieuws items geretourneerd op basis van een zoek query. Als de zoek query leeg is, retourneert de API de meeste nieuws artikelen uit verschillende categorieën. Een query verzenden via URL uw zoek term coderen en toevoegen aan de `q=""` para meter. Zie [ondersteunde landen/regio's en markten](language-support.md#supported-markets-for-news-search-endpoint)voor de beschik baarheid.

### <a name="top-news-items-by-category"></a>Nieuws items van het hoogste niveau per categorie

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Retourneert de nieuws items van het hoogste niveau per categorie. U kunt de meest voorkomende artikelen van uw bedrijf, sport of ontspanning specifiek aanvragen met `category=business` , `category=sports` of `category=entertainment` . De `category` para meter kan alleen worden gebruikt met de `/news` URL. Er zijn enkele formele vereisten voor het opgeven van categorieën. Raadpleeg `category` de documentatie over de [query parameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Een query verzenden via URL uw zoek term coderen en toevoegen aan de `q=""` para meter. Zie [ondersteunde landen/regio's en markten](language-support.md#supported-markets-for-news-endpoint)voor de beschik baarheid.

### <a name="trending-news-topics"></a>Onderwerpen over trending nieuws 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Hiermee worden nieuws onderwerpen geretourneerd die momenteel worden getrendd op sociale netwerken. Als de `/trendingtopics` optie is opgenomen, negeert Bing Search enkele andere para meters, `freshness` zoals en `?q=""` . Zie [ondersteunde landen/regio's en markten](language-support.md#supported-markets-for-news-trending-endpoint)voor de beschik baarheid.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over kopteksten, para meters, markt codes, antwoord objecten, fouten, enzovoort, zie de naslag informatie voor [Bing Nieuws zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Zie de naslag pagina's voor elk type voor volledige informatie over de para meters die door elk eind punt worden ondersteund.
Zie [Bing News Search quick start (](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)Engelstalig) voor voor beelden van basis aanvragen met de nieuws zoekopdracht-API.
