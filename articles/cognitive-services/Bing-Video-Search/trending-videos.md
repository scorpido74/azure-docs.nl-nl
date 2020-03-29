---
title: Zoek op internet naar trending video's met de Bing Video Search API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Bing Video Search API om op internet te zoeken naar trending video's.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500617"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Krijg trending video's met de Bing Video Search API 

Met de Bing Video Search API u de toonaangevende video's van vandaag vinden op het web en in verschillende categorieën. 

## <a name="get-request"></a>GET-aanvraag

Als u de trending video's van vandaag uit de Bing Video Search API wilt halen, stuurt u het volgende GET-verzoek:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Marktondersteuning

De volgende markten ondersteunen trending video's.  
 
-   en-AU (Engels, Australië)  
-   en-CA (Engels, Canada)  
-   nl-GB (Engels, Groot-Brittannië)  
-   en-ID (Engels, Indonesië)  
-   en-IE (Engels, Ierland)  
-   en-IN (Engels, India)  
-   nl-NZ (Engels, Nieuw-Zeeland)  
-   en-PH (Engels, Filippijnen)  
-   en-SG (Engels, Singapore)  
-   nl-VS (Engels, Verenigde Staten)  
-   nl-WW (Engels, Wereldwijd geaggregeerde code)  
-   nl-ZA (Engels, Zuid-Afrika)  
-   zh-CN (Chinees, China)

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord  

In het volgende voorbeeld wordt een API-antwoord weergegeven dat trending video's bevat, die worden vermeld per categorie en subcategorie. Het antwoord bevat ook bannervideo's, die de meest populaire trending video's zijn en afkomstig kunnen zijn uit een of meer categorieën.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Inzicht verkrijgen over video](video-insights.md)