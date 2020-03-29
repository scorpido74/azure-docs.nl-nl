---
title: Zoekresultaten filteren - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: U de typen antwoorden die Bing in het antwoord (bijvoorbeeld afbeeldingen, video's en nieuws) bevat, filteren met behulp van de queryparameter 'responseFilter'.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220268"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>De antwoorden filteren die het zoekantwoord bevat  

Wanneer u het web bevraagt, retourneert Bing alle relevante inhoud die het voor de zoekopdracht vindt. Als de zoekopdracht bijvoorbeeld 'zeilen+rubberboten' is, kan het antwoord de volgende antwoorden bevatten:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Queryparameters

Als u de antwoorden wilt filteren die door Bing zijn geretourneerd, gebruikt u de onderstaande queryparameters bij het aanroepen van de API.  

### <a name="responsefilter"></a>ResponseFilter

U de typen antwoorden die Bing in het antwoord (bijvoorbeeld afbeeldingen, video's en nieuws) bevat, filteren met behulp van de queryparameter [responseFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) een door komma's afgebakende lijst met antwoorden. Een antwoord wordt opgenomen in het antwoord als Bing relevante inhoud voor vindt. 

Als u specifieke antwoorden wilt uitsluiten van `-` het antwoord, zoals afbeeldingen, bereidt u een teken voor op het antwoordtype. Bijvoorbeeld:

```
&responseFilter=-images,-videos
```

Het volgende laat `responseFilter` zien hoe te gebruiken om beelden, video's en nieuws van zeilen rubberboten verzoek. Wanneer u de querytekenreeks codeert, worden de komma's gewijzigd in %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Hieronder ziet u de respons op de vorige query. Omdat Bing geen relevante video- en nieuwsresultaten heeft gevonden, bevat het antwoord deze niet.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Hoewel Bing geen video- en nieuwsresultaten heeft teruggegeven in de vorige reactie, betekent dit niet dat video- en nieuwsinhoud niet bestaat. Het betekent gewoon dat de pagina niet bevatten. Als u echter [meer](./paging-webpages.md) resultaten doorloopt, bevatten de volgende pagina's ze waarschijnlijk. Als u de [API voor videozoeken](../bing-video-search/search-the-web.md) en [de API voor nieuwszoeken](../bing-news-search/search-the-web.md) rechtstreeks aanroept, bevat het antwoord waarschijnlijk resultaten.

U wordt ontmoedigd `responseFilter` om resultaten uit één API te halen. Als u inhoud uit één Bing-API wilt, roept u die API rechtstreeks aan. Als u bijvoorbeeld alleen afbeeldingen wilt ontvangen, stuurt u `https://api.cognitive.microsoft.com/bing/v7.0/images/search` een verzoek naar het eindpunt van de Image Search API of een van de andere eindpunten van [afbeeldingen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Het aanroepen van de enige API is niet alleen belangrijk om prestatieredenen, maar omdat de inhoudsspecifieke API's rijkere resultaten bieden. U bijvoorbeeld filters gebruiken die niet beschikbaar zijn voor de Web Search API om de resultaten te filteren.  

### <a name="site"></a>Site

Als u zoekresultaten uit een specifiek `site:` domein wilt ophalen, neemt u de queryparameter op in de querytekenreeks.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Afhankelijk van de query bestaat `site:` de kans dat het antwoord inhoud voor volwassenen bevat, ongeacht de [instelling safeSearch,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) als u de queryoperator gebruikt. Gebruik `site:` alleen als u zich bewust bent van de inhoud op de site en uw scenario de mogelijkheid van inhoud voor volwassenen ondersteunt.

### <a name="freshness"></a>Nieuwheid

Als u de resultaten van het webantwoord wilt beperken tot webpagina's die Bing gedurende een bepaalde periode heeft ontdekt, stelt u de parameter [versheidsquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) in op een van de volgende hoofdletters:

* `Day`— Webpagina's retourneren die Bing in de afgelopen 24 uur heeft ontdekt
* `Week`— Webpagina's retourneren die Bing in de afgelopen 7 dagen heeft ontdekt
* `Month`— Webpagina's retourneren die in de afgelopen 30 dagen zijn ontdekt

U deze parameter ook instellen op een `YYYY-MM-DD..YYYY-MM-DD`aangepast datumbereik in het formulier. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Als u de resultaten wilt beperken tot één datum, stelt u de parameter versheid in op een specifieke datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

De resultaten kunnen webpagina's bevatten die buiten de opgegeven periode vallen als het aantal webpagina's dat Bing aan uw filtercriteria koppelt, kleiner is dan het aantal webpagina's dat u hebt aangevraagd (of het standaardnummer dat Bing retourneert).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Beperking van het aantal antwoorden in het antwoord

Bing kan meerdere antwoordtypen retourneren in het JSON-antwoord. Als u bijvoorbeeld *zeilen+rubberboten opvraagt,* `webpages`kan `images` `videos`Bing `relatedSearches`terugkeren, , en .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Als u het aantal antwoorden wilt beperken dat Bing terugkeert naar de bovenste twee antwoorden (webpagina's en afbeeldingen), stelt u de parameter [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) query in op 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Het antwoord `webPages` omvat `images`alleen en .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Als u `responseFilter` de queryparameter toevoegt aan de vorige query en deze instelt op webpagina's en nieuws, bevat het antwoord alleen webpagina's omdat nieuws niet wordt gerangschikt.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Antwoorden promoten die niet zijn gerangschikt

Als de best gerangschikte antwoorden die Bing voor een query retourneert webpagina's, afbeeldingen, video's en gerelateerde zoekopdrachten zijn, bevat het antwoord deze antwoorden. Als u [antwoordaantal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) instelt op twee (2), geeft Bing de bovenste twee gerangschikte antwoorden terug: webpagina's en afbeeldingen. Als u wilt dat Bing afbeeldingen en video's in het antwoord opneemt, geeft u de parameter [promotiequery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) op en stelt u deze in op afbeeldingen en video's.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Het volgende is het antwoord op het bovenstaande verzoek. Bing retourneert de bovenste twee antwoorden, webpagina's en afbeeldingen en promoot video's in het antwoord.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Als u `promote` op nieuws instelt, bevat het antwoord niet het nieuwsantwoord omdat het geen gerangschikt antwoord&mdash;is dat u alleen gerangschikte antwoorden promoten.

De antwoorden die u wilt promoten `answerCount` tellen niet mee voor de limiet. Als de gerangschikte antwoorden bijvoorbeeld nieuws, afbeeldingen en video's zijn en u bent ingesteld `answerCount` op 1 en `promote` op nieuws, bevat de reactie nieuws en afbeeldingen. Of als de gerangschikte antwoorden video's, afbeeldingen en nieuws zijn, bevat de reactie video's en nieuws.

U mag `promote` alleen gebruiken `answerCount` als u de queryparameter opgeeft.
