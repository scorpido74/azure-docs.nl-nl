---
title: Rangschikking gebruiken om antwoorden weer te geven - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van rangschikking om de antwoorden weer te geven die de Bing Entity Search API retourneert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423912"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Rangschikking gebruiken om zoekresultaten van entiteiten weer te geven  

Elk zoekantwoord van elke entiteit bevat een [RankingResponse-antwoord](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) dat aangeeft hoe u zoekresultaten moet weergeven die zijn geretourneerd door de Bing Entity Search API. De ranking responsgroepen resulteert in inhoud van de pool-, hoofdlijn- en zijbalk. Het paalresultaat is het belangrijkste of meest prominente resultaat en moet eerst worden weergegeven. Als u de overige resultaten niet in een traditionele hoofdlijn- en zijbalkindeling weergeeft, moet u de hoofdlijninhoud beter zichtbaar maken dan de inhoud van de zijbalk. 
  
Binnen elke groep identificeert de array [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) de volgorde waarin de inhoud moet worden weergegeven. Elk item biedt twee manieren om het resultaat binnen een antwoord te identificeren.  
 

|Veld | Beschrijving  |
|---------|---------|
|`answerType` en `resultIndex` | `answerType`hiermee wordt het antwoord (Entiteit `resultIndex` of Plaats) geïdentificeerd en wordt een resultaat in dat antwoord (bijvoorbeeld een entiteit) geïdentificeerd. De index begint bij 0.|
|`value`    | `value`Bevat een ID die overeenkomt met de ID van een antwoord of een resultaat in het antwoord. Ofwel het antwoord of de resultaten bevatten de ID, maar niet beide. |
  
Met `answerType` behulp `resultIndex` van de en is een proces in twee stappen. Gebruik eerst `answerType` het antwoord dat de resultaten bevat om weer te geven. Vervolgens `resultIndex` kunt u indexeren in de resultaten van dat antwoord om het resultaat weer te geven. (De `answerType` waarde is de naam van het veld in het object [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Als u alle resultaten van het antwoord samen moet weergeven, bevat `resultIndex` het antwoorditem van de rangschikking geen veld.

Als u de ID gebruikt, moet u de classificatie-id koppelen aan de id van een antwoord of een van de resultaten. Als een antwoordobject `id` een veld bevat, geeft u alle resultaten van het antwoord samen weer. Als het object `Entities` bijvoorbeeld `id` het veld bevat, worden alle entiteiten samen weergegeven. Als `Entities` het object het `id` veld niet bevat, `id` bevat elke entiteit een veld en mengt de positierespons de entiteiten met de resultaten Plaatsen.  
  
## <a name="ranking-response-example"></a>Voorbeeld van het rangschikken van antwoorden

Hieronder ziet u een voorbeeld [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Op basis van deze ranking reactie, zou de zijbalk de twee entiteit resultaten met betrekking tot Jimi Hendrix weer te geven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor één pagina maken](tutorial-bing-entities-search-single-page-app.md)
