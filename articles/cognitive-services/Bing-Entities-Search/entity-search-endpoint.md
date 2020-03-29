---
title: Het eindpunt van de Bing-entiteit search-API
titleSuffix: Azure Cognitive Services
description: De Bing Entity Search API heeft één eindpunt dat entiteiten van het web retourneert op basis van een query. Deze zoekresultaten worden geretourneerd in JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072658"
---
# <a name="bing-entity-search-api-endpoint"></a>Eindpunt van Bing-entiteitzoeken API


De Bing Entity Search API heeft één eindpunt dat entiteiten van het web retourneert op basis van een query. Deze zoekresultaten worden geretourneerd in JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Entiteitsresultaten ophalen vanaf het eindpunt

Als u entiteitsresultaten wilt krijgen `GET` met de **Bing-API,** stuurt u een aanvraag naar het volgende eindpunt. Gebruik [kopteksten](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) en [queryparameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) om uw zoekaanvraag aan te passen. Zoekverzoeken kunnen worden `?q=` verzonden met behulp van de parameter.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is de Bing Entiteiten zoeken-API?](overview.md)

## <a name="see-also"></a>Zie ook 

Zie het referentieartikel [Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten en meer.
