---
title: Bladeren door de zoek resultaten-Bing Zoeken-API's
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u door de zoek resultaten van de Bing Zoeken-API's bladert.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: e2149c7db20939a739380dc2df0e21b6a62ed916
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955358"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>De resultaten van de Bing Zoeken-API's pagina door lopen

Wanneer u een oproep naar de Bing Web-, Custom-, Image-, News-of Video's zoeken-Api's verzendt, retourneert Bing een subset van het totale aantal resultaten dat relevant kan zijn voor de query. Als u het geschatte totale aantal beschik bare resultaten wilt ophalen, opent u het veld `totalEstimatedMatches` van het antwoord object. 

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

## <a name="paging-through-search-results"></a>Paginering via zoek resultaten

Als u de beschik bare resultaten wilt door lopen, gebruikt u de para meters `count` en `offset` bij het verzenden van uw aanvraag.  

> [!NOTE]
>
> * Paginering met de Bing Video-, afbeeldings-en nieuws-Api's zijn alleen van toepassing op algemene video (`/video/search`), Nieuws (`/news/search`) en installatie kopieën (`/image/search`). Paginering via trending onderwerpen en categorieën wordt niet ondersteund.  
> * Het veld `TotalEstimatedMatches` is een schatting van het totale aantal Zoek resultaten voor de huidige query. Wanneer u de para meters `count` en `offset` instelt, kan deze schatting worden gewijzigd.

| Parameter | Description                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Hiermee geeft u het aantal resultaten op dat moet worden geretourneerd in het antwoord. Houd er rekening mee dat de standaard waarde van `count` en het maximale aantal resultaten dat u kunt aanvragen afhankelijk zijn van de API. U kunt deze waarden vinden in de referentie documentatie onder de [volgende stappen](#next-steps). |
| `offset`  | Hiermee geeft u het aantal resultaten dat moet worden overgeslagen. De `offset` is op nul gebaseerd en moet kleiner zijn dan (`totalEstimatedMatches` @ no__t-2 @ no__t-3).                                           |

Als u bijvoorbeeld 15 resultaten per pagina wilt weer geven, stelt u `count` in op 15 en `offset` op 0 om de eerste pagina met resultaten op te halen. Voor elke volgende API-aanroep zou u `offset` met 15 oplopen. In het volgende voor beeld worden 15 webpagina's aangevraagd vanaf de offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Als u de standaard waarde @no__t 0 gebruikt, hoeft u alleen de para meter `offset` op te geven in uw API-aanroepen.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> De Bing Webzoekopdrachten-API retourneert Zoek resultaten die webpagina's, afbeeldingen, Video's en nieuws kunnen bevatten. Wanneer u de zoek resultaten van de Bing Webzoekopdrachten-API doorloopt [, worden alleen webpagina's](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)gepagineerd en niet andere antwoord typen zoals afbeeldingen of nieuws. Zoek resultaten in `WebPage`-objecten kunnen resultaten bevatten die ook in andere antwoord typen worden weer gegeven.
>
> Als u de query parameter `responseFilter` gebruikt zonder filter waarden op te geven, moet u de para meters `count` en `offset` niet gebruiken. 

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Web Search-Api's?](bing-api-comparison.md)
* [Web Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-referenc) (Naslaggids Web Search API v7)
* [Naslag informatie over Bing Custom Search-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Naslag informatie over Bing Nieuws zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Naslag informatie over Bing Video's zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Naslag informatie over Bing Afbeeldingen zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)