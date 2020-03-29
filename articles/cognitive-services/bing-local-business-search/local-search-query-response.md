---
title: API-query's en -antwoorden verzenden en gebruiken - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel voor meer informatie over het verzenden en gebruiken van zoekopdrachten met de Bing Local Business Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326726"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>API-query's en -antwoorden van Bing Local Business Search verzenden en gebruiken

U lokale resultaten uit de Bing Local Business Search API ophalen `Ocp-Apim-Subscription-Key` door een zoekopdracht naar het eindpunt te verzenden en de header op te nemen, wat vereist is. Samen met beschikbare [kopteksten](local-search-reference.md#headers) en [parameters](local-search-reference.md#query-parameters)kunnen zoekopdrachten worden aangepast door [geografische grenzen](specify-geographic-search.md) op te geven voor het te zoeken gebied en de [categorieën](local-search-query-response.md) plaatsen die worden geretourneerd.

## <a name="creating-a-request"></a>Een aanvraag maken

Als u een aanvraag wilt verzenden naar de Bing Local `q=` Business Search API, voegt u een `Ocp-Apim-Subscription-Key` zoekterm toe aan de parameter voordat u deze toevoegt aan het API-eindpunt en de koptekst. Bijvoorbeeld:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

De syntaxis van de volledige URL van het verzoek wordt hieronder weergegeven. Zie de Bing Local Business Search API [quickstarts](quickstarts/local-quickstart.md)en referentie-inhoud voor [kopteksten](local-search-reference.md#headers) en [parameters](local-search-reference.md#query-parameters) voor meer informatie over het verzenden van aanvragen. 

Zie [Zoekcategorieën voor de Bing Local Business Search API voor](local-categories.md)informatie over lokale zoekcategorieën.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Reacties gebruiken

JSON-antwoorden uit de Bing Local `SearchResponse` Business Search API bevatten een object. De API retourneert relevante `places` zoekresultaten in het veld. als er geen resultaten `places` worden gevonden, wordt het veld niet opgenomen in het antwoord.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Kenmerken van zoekresultaten

De JSON-resultaten die door de API worden geretourneerd, bevatten de volgende kenmerken:

* _type
* adres
* entiteitPresentatieInfo
* Geo
* id
* name
* routeablePoint
* telefoon
* url

Zie de [bing Local Search API v7-referentie](local-search-reference.md) voor algemene informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten, enz..

> [!NOTE]
> U, of een derde partij namens u, mag geen gegevens uit de Local Search API gebruiken, bewaren, opslaan, opslaan, delen of distribueren met het oog op het testen, ontwikkelen, trainen, distribueren of beschikbaar stellen van niet-Microsoft-service of -functies. 


## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Het volgende JSON-antwoord bevat zoekresultaten `?q=restaurant+in+Bellevue`die door de query zijn opgegeven.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Volgende stappen
- [Zoeken in lokaal bedrijf snel gestart](quickstarts/local-quickstart.md)
- [Lokaal zakelijk zoeken Java snelaan de dag](quickstarts/local-search-java-quickstart.md)
- [Lokaal zakelijk zoekknooppunt snel aan de slag](quickstarts/local-search-node-quickstart.md)
- [Lokaal zoeken in Business Python snel gestart](quickstarts/local-search-python-quickstart.md)
