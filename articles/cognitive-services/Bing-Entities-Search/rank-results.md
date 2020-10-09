---
title: De volg orde gebruiken om antwoorden weer te geven-Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van een classificatie om de antwoorden weer te geven die de Bing Entiteiten zoeken-API retourneert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68423912"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Classificatie gebruiken om de zoek resultaten van entiteiten weer te geven  

Elk zoek antwoord van de entiteit bevat een [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) -antwoord dat aangeeft hoe zoek resultaten moeten worden weer gegeven die worden geretourneerd door de Bing entiteiten zoeken-API. De volg orde van de antwoord groepen resulteert in een stip-, Mainline-en Sidebar-inhoud. Het hengel resultaat is het belangrijkste of prominente resultaat en moet eerst worden weer gegeven. Als u de resterende resultaten niet weer geven in een traditionele Mainline-en Sidebar-indeling, moet u de mainline-inhoud beter zichtbaar maken dan de inhoud van de zijbalk. 
  
Binnen elke groep identificeert de [items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matrix de volg orde waarin de inhoud moet worden weer gegeven. Elk item biedt twee manieren om het resultaat binnen een antwoord te identificeren.  
 

|Veld | Beschrijving  |
|---------|---------|
|`answerType` en `resultIndex` | `answerType` identificeert het antwoord (ofwel entiteit of plaats) en `resultIndex` identificeert een resultaat binnen dat antwoord (bijvoorbeeld een entiteit). De index begint bij 0.|
|`value`    | `value` Bevat een ID die overeenkomt met de ID van een antwoord of een resultaat binnen het antwoord. Het antwoord of de resultaten bevatten de ID, maar niet beide. |
  
Met de `answerType` en `resultIndex` is een proces dat uit twee stappen bestaat. Gebruik eerst `answerType` om het antwoord te identificeren dat de resultaten bevat die moeten worden weer gegeven. Gebruik vervolgens `resultIndex` om de resultaten van dat antwoord te indexeren om het resultaat weer te geven. (De `answerType` waarde is de naam van het veld in het object [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Als u wilt dat alle antwoord resultaten samen worden weer gegeven, bevat het positie-antwoord item niet het `resultIndex` veld.

Als u de ID gebruikt, moet u de classificatie-ID overeenkomen met de ID van een antwoord of een van de resultaten. Als een antwoord object een `id` veld bevat, worden alle resultaten van het antwoord samen weer gegeven. Als het-object bijvoorbeeld `Entities` het veld bevat `id` , geeft alle artikelen van de entiteit samen. Als het `Entities` veld niet is opgenomen in het object `id` , bevat elke entiteit een `id` veld en wordt de rang orde van de entiteiten mixen met de locatie resultaten.  
  
## <a name="ranking-response-example"></a>Voor beeld van een rang orde-antwoord

Hieronder ziet u een voor beeld van een [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Op basis van deze positie reactie worden de twee entiteits resultaten weer gegeven die gerelateerd zijn aan Jimi Hendrix.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorial-bing-entities-search-single-page-app.md)
