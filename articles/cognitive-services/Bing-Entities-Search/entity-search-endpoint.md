---
title: Het Bing Entiteiten zoeken-API-eind punt
titleSuffix: Azure Cognitive Services
description: De Bing Entiteiten zoeken-API heeft één eind punt dat entiteiten uit het web retourneert op basis van een query. Deze Zoek resultaten worden geretourneerd in JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072658"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entiteiten zoeken-API-eind punt


De Bing Entiteiten zoeken-API heeft één eind punt dat entiteiten uit het web retourneert op basis van een query. Deze Zoek resultaten worden geretourneerd in JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Entiteits resultaten ophalen van het eind punt

Als u entiteits resultaten wilt ophalen met behulp van de **Bing API**, stuurt u een `GET` aanvraag naar het volgende eind punt. Gebruik [kopteksten](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) en [query parameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) om uw zoek opdracht aan te passen. Zoek opdrachten kunnen worden verzonden met behulp van de para meter `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is de Bing Entiteiten zoeken-API?](overview.md)

## <a name="see-also"></a>Zie ook 

Voor meer informatie over kopteksten, para meters, markt codes, reactie objecten, fouten en meer, zie het naslag artikel over [Bing entiteiten zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
