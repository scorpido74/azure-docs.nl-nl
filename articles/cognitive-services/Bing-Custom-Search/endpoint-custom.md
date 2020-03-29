---
title: Bing Aangepaste zoekopdrachten-eindpunt
titleSuffix: Azure Cognitive Services
description: Creëer zoekervaringen op maat voor onderwerpen waar u om geeft. Gebruikers zien zoekresultaten die zijn afgestemd op de inhoud die ze belangrijk maken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072791"
---
# <a name="custom-search"></a>Aangepast zoeken
Met Bing Aangepaste zoekopdrachten kunt u op maat gemaakte zoekervaringen maken voor onderwerpen die u interesseren. Uw gebruikers zien dan zoekresultaten die zijn afgestemd op de inhoud die ze interessant vinden in plaats van dat ze door pagina's met zoekresultaten moeten bladeren die niet-relevante inhoud bevatten.

## <a name="custom-search-endpoint"></a>Aangepast zoekeindpunt
Als u resultaten wilt opdoen met `GET` de Bing Custom Search API, stuurt u een aanvraag naar het volgende eindpunt. Gebruik de kopteksten en URL-parameters om verdere specificaties te definiëren.

Eindpunt: retourneert zoeksuggesties als JSON-resultaten die relevant zijn `?q=""`voor de invoer van de gebruiker, gedefinieerd door .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Zie de [zelfstudie](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)voor voorbeelden waarin wordt beschreven hoe u aangepaste zoekbronnen instelt. Zie de referentie van de [Bing Custom Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten, enz..

## <a name="custom-search-response-json"></a>Json voor aangepaste zoekgereageerd
Een aangepaste zoekaanvraag retourneert resultaten als JSON-objecten, zie [Antwoordobjecten](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Aangepaste autosuggest
Met de aangepaste API Voor automatisch voorstellen u een gedeeltelijke zoekqueryterm naar Bing verzenden en een lijst met voorgestelde query's terugkrijgen die u configureren. Met Custom Autosuggest voegt u suggesties toe die door de API zijn geretourneerd en geeft u optioneel op of u suggesties wilt opnemen die door Bing zijn gegenereerd.

## <a name="custom-autosuggest-endpoint"></a>Aangepast eindpunt automatisch voorstellen
Als u aangepaste querysuggesties wilt aanvragen, stuurt u een GET-verzoek naar:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Zie [Aangepaste zoeksuggesties definiëren](define-custom-suggestions.md)voor informatie over het definiëren van aangepaste suggesties.

## <a name="custom-image-search"></a>Zoeken op aangepaste afbeeldingen
Met de aangepaste API voor zoeken naar afbeeldingen u een zoekopdracht naar Bing verzenden en een lijst met relevante afbeeldingen van uw aangepaste zoekexemplaar terugkrijgen.

## <a name="custom-image-search-endpoint"></a>Eindpunt voor zoeken in aangepaste afbeelding
Als u afbeeldingen wilt aanvragen van uw instantie Aangepast zoeken, stuurt u een GET-verzoek naar de volgende URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Zie [Uw aangepaste zoekervaring configureren](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view)voor informatie over het configureren van een instantie Aangepast zoeken.

## <a name="next-steps"></a>Volgende stappen
De **Bing** Bing-API's ondersteunen zoekacties die resultaten retourneren op basis van hun type.Alle zoekeindpunten retourneren resultaten als JSON-antwoordobjecten. Alle eindpunten ondersteunen query's die een specifieke taal en/of locatie retourneren op lengte, breedte- en zoekradius.

Zie de referentiepagina's voor elk type voor volledige informatie over de parameters die door elk eindpunt worden ondersteund.
Zie [Snelstarts](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) voor aangepast zoeken voor voorbeelden van basisaanvragen met de Aangepaste zoek-API
