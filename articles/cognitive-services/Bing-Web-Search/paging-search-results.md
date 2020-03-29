---
title: Pagina's door zoekresultaten - Bing Search API's
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bekijken van zoekresultaten via de Bing Search API's.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481733"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Hoe pagina door middel van de resultaten van de Bing Search API's

Wanneer u een oproep verzendt naar de API's Bing Web, Custom, Image, News of Video Search, retourneert Bing een subset van het totale aantal resultaten dat mogelijk relevant is voor de query. Als u het geschatte totale aantal beschikbare resultaten `totalEstimatedMatches` wilt krijgen, hebt u toegang tot het veld van het antwoordobject. 

Bijvoorbeeld: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Doorzoek zoekresultaten

Als u de beschikbare resultaten `count` `offset` wilt bekijken, gebruikt u de queryparameters en queryparameters bij het verzenden van uw aanvraag.  

> [!NOTE]
>
> * Paging met de Bing Video, Image, en News`/video/search`API's`/news/search`is alleen`/image/search`van toepassing op algemene video (), nieuws ( ) en afbeelding ( ) zoekopdrachten. Paging door trending onderwerpen en categorieën wordt niet ondersteund.  
> * Het `TotalEstimatedMatches` veld is een schatting van het totale aantal zoekresultaten voor de huidige query. Wanneer u `count` de `offset` parameters instelt, kan deze schatting veranderen.

| Parameter | Beschrijving                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Hiermee geeft u het aantal resultaten op dat in het antwoord moet worden retournerd. Houd er rekening `count`mee dat de standaardwaarde van en het maximum aantal resultaten dat u aanvragen per API verschilt. U deze waarden vinden in de referentiedocumentatie onder [Volgende stappen](#next-steps). |
| `offset`  | Hiermee geeft u het aantal resultaten op dat moet worden overgeslagen. Het `offset` is op nul gebaseerd en`totalEstimatedMatches` - `count`moet lager zijn dan ( ).                                           |

Als u bijvoorbeeld 15 resultaten per pagina wilt weergeven, stelt u 15 `count` in en `offset` op 0 om de eerste pagina met resultaten te krijgen. Voor elke volgende API-aanroep wordt u met `offset` 15 verhoogd. In het volgende voorbeeld worden 15 webpagina's aanvragen die beginnen bij offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Als u de `count` standaardwaarde gebruikt, hoeft `offset` u alleen de queryparameter in uw API-aanroepen op te geven.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Wanneer u de Bing-beeld- en video-API's gebruikt, u de `nextOffset` waarde gebruiken om dubbele zoekresultaten te voorkomen. Haal de waarde `Images` `Videos` uit de of antwoordobjecten en `offset` gebruik deze in uw aanvragen met de parameter.  

> [!NOTE]
> De Bing Web Search API retourneert zoekresultaten die webpagina's, afbeeldingen, video's en nieuws kunnen bevatten. Wanneer u zoekresultaten uit de Bing Web Search API bekijkt, paging u alleen [webpagina's](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)en niet andere antwoordtypen zoals afbeeldingen of nieuws. Zoekresultaten in `WebPage` objecten kunnen ook resultaten bevatten die in andere antwoordtypen worden weergegeven.
>
> Als u `responseFilter` de queryparameter gebruikt zonder filterwaarden op `count` te `offset` geven, gebruikt u de parameters en parameters niet. 

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Web Search API's?](bing-api-comparison.md)
* [Web Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) (Naslaggids Web Search API v7)
* [Bing Custom Search API v7-verwijzing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7 referentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7-verwijzing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7-verwijzing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
