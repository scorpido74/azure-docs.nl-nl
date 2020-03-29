---
title: Trending afbeeldingen bekijken met de Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Zoek naar de trending images van vandaag vanaf het web met de Bing Image Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66383437"
---
# <a name="get-trending-images-from-the-web"></a>Krijg trending afbeeldingen van het web

Als u de trending images van vandaag wilt krijgen, stuurt u het volgende GET-verzoek:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

De TRENDING Images API ondersteunt momenteel alleen de volgende markten:  

- nl-VS (Engels, Verenigde Staten)  
- en-CA (Engels, Canada)  
- en-AU (Engels, Australië)  
- zh-CN (Chinees, China)

Het antwoord bevat een object [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) dat afbeeldingen per categorie weergeeft. Gebruik de categorie's `title` om de afbeeldingen in uw gebruikerservaring te groeperen. De categorieën kunnen dagelijks veranderen.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Elke tegel bevat een afbeelding en opties voor het verkrijgen van gerelateerde afbeeldingen. Als u de gerelateerde afbeeldingen wilt `text` krijgen, u de query gebruiken om de [Image Search API](./search-the-web.md) aan te roepen en de gerelateerde afbeeldingen zelf weer te geven. U de URL `webSearchUrl` ook gebruiken om de gebruiker naar de pagina met zoekresultaten van Bing te brengen, die de bijbehorende afbeeldingen bevat.

Als u de Image Search API aanroept om de gerelateerde afbeeldingen `id` op te halen, stelt u de parameter [id-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) in op de id in het veld. Als u de id opgeeft, zorgt u ervoor dat het antwoord de afbeelding bevat (het is de eerste afbeelding in het antwoord) en de bijbehorende afbeeldingen. Stel ook de parameter [q-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) `query` in op `text` de tekst in het veld van het object.

In het volgende voorbeeld ziet u hoe u de afbeeldings-ID gebruiken om gerelateerde afbeeldingen van De heer Smith te krijgen in de voorgaande API-reactie van Trending Images.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
