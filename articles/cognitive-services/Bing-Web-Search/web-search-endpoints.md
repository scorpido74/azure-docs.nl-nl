---
title: Eindpunt voor zoeken op het web
titleSuffix: Azure Cognitive Services
description: Als u zoekresultaten op `GET` het web wilt opvragen, stuurt u een verzoek naar het volgende eindpunt. De headers en URL parameters definiëren verdere specificaties.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111332"
---
# <a name="web-search-endpoint"></a>Eindpunt voor webzoeken

De **Web Search API** retourneert webpagina's, nieuws, afbeeldingen, video's en [entiteiten.](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) Entiteiten hebben beknopte informatie over een persoon, plaats of onderwerp.

## <a name="endpoint"></a>Eindpunt

Als u zoekresultaten op het web `GET` wilt krijgen met de Bing-API, stuurt u een aanvraag naar het volgende eindpunt. De headers en URL parameters definiëren verdere specificaties.

**Eindpunt:** retourneert webresultaten die relevant zijn voor de `?q=""`zoekopdracht van de gebruiker die is gedefinieerd door .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Eindpunt: zie de [Bing Web API v7-verwijzing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten en meer.

## <a name="response-json"></a>Reactie JSON

Het antwoord op een webzoekaanvraag bevat alle resultaten als JSON-objecten. Voor het ontregelen van het resultaat zijn procedures nodig die de elementen van elk type verwerken. Zie de [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) en [broncode](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) voor voorbeelden.

## <a name="next-steps"></a>Volgende stappen

De **Bing** Bing-API's ondersteunen zoekacties die resultaten retourneren op basis van hun type.Alle zoekeindpunten retourneren resultaten als JSON-antwoordobjecten. Alle eindpunten ondersteunen query's die een specifieke taal en locatie retourneren op lengte, breedtegraad en zoekradius.

Zie de referentiepagina's voor elk type voor volledige informatie over de parameters die door elk eindpunt worden ondersteund.
Zie [Zoeken in het web snelstarts](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web)voor voorbeelden van basisaanvragen met behulp van de WEB Search API.
