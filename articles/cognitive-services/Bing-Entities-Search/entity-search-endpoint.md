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
ms.openlocfilehash: 1d9b7c79919569830834915fc609b849e717dce8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365836"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entiteiten zoeken-API-eind punt

> [!WARNING]
> Bing Zoeken-API's van Cognitive Services naar Bing Search-Services verplaatsen. Vanaf **30 oktober 2020** moeten nieuwe exemplaren van Bing Search worden ingericht volgens het proces dat [hier](https://aka.ms/cogsvcs/bingmove)wordt beschreven.
> Bing Zoeken-API's ingericht met Cognitive Services wordt voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst gebeurt.
> Zie [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)voor migratie-instructies.


De Bing Entiteiten zoeken-API heeft één eind punt dat entiteiten uit het web retourneert op basis van een query. Deze Zoek resultaten worden geretourneerd in JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Entiteits resultaten ophalen van het eind punt

Als u entiteits resultaten wilt ophalen met behulp van de **Bing API** , verzendt `GET` u een aanvraag naar het volgende eind punt. Gebruik [kopteksten](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) en [query parameters](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) om uw zoek opdracht aan te passen. Zoek opdrachten kunnen worden verzonden met behulp van de `?q=` para meter.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is de Bing Entiteiten zoeken-API?](overview.md)

## <a name="see-also"></a>Zie ook 

Voor meer informatie over kopteksten, para meters, markt codes, reactie objecten, fouten en meer, zie het naslag artikel over [Bing entiteiten zoeken-API V7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .