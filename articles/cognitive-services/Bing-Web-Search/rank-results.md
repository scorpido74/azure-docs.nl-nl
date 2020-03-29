---
title: Ranglijst gebruiken om zoekresultaten weer te geven - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van rangschikking om zoekresultaten weer te geven vanuit de Bing Web Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390127"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Rangschikking gebruiken om API-resultaten van Bing Web Search weer te geven  

Elk zoekantwoord bevat een [RankingResponse-antwoord,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) dat aangeeft hoe u de zoekresultaten moet weergeven. De ranking respons groepen resultaten door mainline inhoud en sidebar inhoud voor een traditionele zoekresultaten pagina. Als u de resultaten niet in een traditionele hoofdlijn- en zijbalkindeling weergeeft, moet u de hoofdlijninhoud beter zichtbaar maken dan de inhoud van de zijbalk.  

Binnen elke groep (hoofdregel of zijbalk) identificeert de array [Items](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) de volgorde waarin de inhoud moet worden weergegeven. Elk item biedt de volgende twee manieren om het resultaat binnen een antwoord te identificeren.  

-   `answerType`en `resultIndex` — `answerType` Het veld identificeert het antwoord (bijvoorbeeld `resultIndex` Webpagina of Nieuws) en identificeert een resultaat in het antwoord (bijvoorbeeld een nieuwsartikel). De index is nul gebaseerd.  

-   `value`— `value` Het veld bevat een id die overeenkomt met de id van een antwoord of een resultaat in het antwoord. Ofwel het antwoord of de resultaten bevatten de ID, maar niet beide.  

Het gebruik van de ID is eenvoudiger te gebruiken omdat u alleen de ranking-ID hoeft te matchen met de ID van een antwoord of een van de resultaten. Als een antwoordobject `id` een veld bevat, geeft u alle resultaten van het antwoord samen weer. Als het object `News` bijvoorbeeld `id` het veld bevat, worden alle nieuwsartikelen samen weergegeven. Als `News` het object het `id` veld niet bevat, `id` bevat elk nieuwsartikel een veld en mengt de rankingrespons de nieuwsartikelen met de resultaten van andere antwoorden.  

Met `answerType` behulp `resultIndex` van de en is een beetje ingewikkelder. U `answerType` gebruikt om het antwoord te identificeren dat de resultaten bevat om weer te geven. Vervolgens kunt `resultIndex` u de resultaten van het antwoord doorindexeren om het resultaat weer te geven. (De `answerType` waarde is de naam van het veld in het object [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Als u alle resultaten van het antwoord samen moet weergeven, bevat `resultIndex` het antwoorditem van de rangschikking geen veld.  

## <a name="ranking-response-example"></a>Voorbeeld van het rangschikken van antwoorden

Hieronder ziet u een voorbeeld [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Omdat het webantwoord geen `id` veld bevat, geeft u alle webpagina's afzonderlijk weer `id` op basis van de rangschikking (elke webpagina bevat een veld). En omdat de antwoorden op afbeeldingen, video's en gerelateerde zoekopdrachten het `id` veld bevatten, geeft u de resultaten van elk van deze antwoorden samen weer op basis van de rangschikking.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Op basis van deze ranking reactie, zou de hoofdlijn de volgende zoekresultaten weer te geven:  

-   Het eerste resultaat op de webpagina
-   Alle afbeeldingen  
-   De resultaten van de tweede en derde webpagina  
-   Alle video's  
-   De resultaten van de 4e, 5e en 6e webpagina  

En op de zijbalk worden de volgende zoekresultaten weergegeven:  

-   Alle gerelateerde zoekopdrachten  


## <a name="next-steps"></a>Volgende stappen

Zie [Antwoorden promoten die niet zijn gerangschikt](./filter-answers.md#promoting-answers-that-are-not-ranked)voor informatie over het promoten van niet-gerangschikte resultaten.

Zie [Het aantal antwoorden in het antwoord beperken](./filter-answers.md#limiting-the-number-of-answers-in-the-response)voor informatie over het beperken van het aantal gerangschikte antwoorden .

Zie [C# ranking tutorial](./csharp-ranking-tutorial.md)voor een C#-voorbeeld dat classificatie gebruikt om resultaten weer te geven.
