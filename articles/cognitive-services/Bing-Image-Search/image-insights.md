---
title: Informatie over afbeeldingen verkrijgen - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Bing Image Search API voor meer informatie over een afbeelding.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: f84c6329c2a4dd0a9ad9e81f3700c9e31de95a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883433"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Afbeeldingen verkrijgen met de Bing Image Search API

> [!IMPORTANT]
> In plaats van het eindpunt /afbeeldingen/details te gebruiken om beeldinzichten te krijgen, moet u [Visual Search](../bing-visual-search/overview.md) gebruiken omdat het uitgebreidere inzichten biedt.


Elke afbeelding bevat een insights-token dat u gebruiken om informatie over de afbeelding te krijgen. U bijvoorbeeld een verzameling gerelateerde afbeeldingen, webpagina's met de afbeelding of een lijst met verkopers krijgen waar u het product in de afbeelding kopen.  

Als u inzicht wilt krijgen in een afbeelding, legt u het [imageInsightsToken-token](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) vast in de reactie.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Roep vervolgens het eindpunt Afbeeldingsdetails aan en stel de `imageInsightsToken`parameter [insightsToken-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) in op het token in .  

Als u de inzichten wilt opgeven `modules` die u wilt opdoen, stelt u de queryparameter in. Om alle inzichten `modules` te `All`krijgen, ingesteld op . Als u alleen de bijschrift- `modules` `Caption%2CCollection`en verzamelingsinzichten wilt ophalen, stel u in op . Zie [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)voor een volledige lijst met mogelijke inzichten. Niet alle inzichten zijn beschikbaar voor alle afbeeldingen. Het antwoord bevat alle inzichten die u hebt aangevraagd, indien beschikbaar.

In het volgende voorbeeld worden alle beschikbare inzichten voor de voorgaande afbeelding opgevraagd.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Inzicht krijgen in een bekende afbeelding

Als u de URL hebt naar een afbeelding waarvan u inzicht wilt krijgen, gebruikt u de parameter [imgUrl-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) in plaats van de parameter [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) om de afbeelding op te geven. Of, als u het afbeeldingsbestand hebt, u de binaire van de afbeelding in de hoofdtekst van een POST-verzoek verzenden. Als u een POST-verzoek gebruikt, moet de `Content-Type` koptekst zijn ingesteld op `multipart/data-form`. Bij beide opties mag de grootte van de afbeelding niet groter zijn dan 1 MB.  

Als u een URL naar de afbeelding hebt, ziet u in het volgende voorbeeld hoe u inzichten van een afbeelding opvragen.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Alle beeldinzichten verkrijgen  

Als u alle inzichten van een afbeelding `All`wilt opvragen, stelt u de parameter [modulesquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op . Als u gerelateerde zoekopdrachten wilt uitvoeren, moet de querytekenreeks van de gebruiker worden vermeld. In dit voorbeeld wordt weergegeven met behulp van de [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) om de afbeelding op te geven.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Het object op het hoogste niveau is een object [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) in plaats van een object [Afbeeldingen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Entiteiten in een afbeelding herkennen  

De entiteitsherkenningsfunctie identificeert entiteiten in een afbeelding, momenteel alleen mensen. Als u entiteiten in een afbeelding wilt `RecognizedEntities`identificeren, stelt u de parameter [modulesquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op .  

> [!NOTE]
> U mag deze module niet opgeven met een andere module. Als u deze module opgeeft met andere modules, bevat het antwoord geen erkende entiteiten.  

Het volgende laat zien hoe u de afbeelding opgeeft met behulp van de parameter [imgUrl.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) Vergeet niet om de queryparameters te coderen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Hieronder ziet u het antwoord op de vorige aanvraag. Omdat de afbeelding twee personen bevat, identificeert het antwoord een regio voor elke persoon. In dit geval werden de mensen erkend in de CelebrityAnnotations en CelebRecognitionAnnotations groepen. Bing geeft een lijst van de personen in elke groep op basis van de waarschijnlijkheid dat ze overeenkomen met de persoon in de oorspronkelijke afbeelding. De lijst is in dalende volgorde van vertrouwen. De CelebRecognitionAnnotations groep geeft het hoogste niveau van vertrouwen dat de wedstrijd correct is.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

Het `region` veld identificeert het gebied van de afbeelding waar Bing de entiteit herkende. Voor mensen vertegenwoordigt de regio het gezicht van de persoon.  

De waarden van de rechthoek zijn relatief ten opzichte van de breedte en hoogte van de oorspronkelijke afbeelding en bevinden zich in het bereik 0,0 tot en met 1,0. Als de afbeelding bijvoorbeeld 300x200 is en de bovenste, linkerhoek van de regio op punt (10, 20) en de rechterbenedenhoek op punt (290, 150), dan is de genormaliseerde rechthoek:  

-   Links: 10 / 300 = 0,03333...  
-   Top: 20 / 200 = 0,1  
-   Rechts: 290 / 300 = 0,9667...  
-   Bodem: 150 / 200 = 0,75  

U de regio gebruiken die Bing retourneert in volgende insights-oproepen. Bijvoorbeeld om visueel vergelijkbare afbeeldingen van de erkende entiteit te krijgen. Zie Afbeeldingen bijsnijden die u wilt gebruiken met modules voor visueel vergelijkbare en entiteitsherkenning voor meer informatie. Het volgende toont de toewijzing tussen de regiovelden en de queryparameters die u zou gebruiken om afbeeldingen bij te snijden.  

-   Linkerkaarten aan [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Hoogste kaarten aan [kat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Juiste kaarten aan [auto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Onderste kaarten naar [de cabine](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Visueel vergelijkbare afbeeldingen zoeken  

Als u afbeeldingen wilt vinden die visueel lijken op de oorspronkelijke afbeelding, stelt u de parameter [modulesquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op Vergelijkbare afbeeldingen.  

In het volgende verzoek ziet u hoe u visueel vergelijkbare afbeeldingen krijgen. De aanvraag gebruikt de parameter [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) query om de oorspronkelijke afbeelding te identificeren. Om de relevantie te verbeteren, moet u de querytekenreeks van de gebruiker opnemen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Hieronder ziet u het antwoord op de vorige aanvraag.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Afbeeldingen bijsnijden om te gebruiken met visueel vergelijkbare en entiteitsherkenningsmodules  

Als u het gebied wilt opgeven van de afbeelding die Bing gebruikt om te bepalen of afbeeldingen visueel vergelijkbaar zijn of om entiteitsherkenning uit te voeren, gebruikt u de parameters [cal,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal) [cat,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat) [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)en [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) query. Standaard gebruikt Bing de hele afbeelding.  

De parameters geven de boven-, linkerhoek en onderkant, rechterhoek van de regio die Bing gebruikt voor vergelijking. Geef de waarden op als breuken van de breedte en hoogte van de oorspronkelijke afbeelding. De fractionele waarden beginnen met (0,0, 0,0) boven, linkerhoek en eindigen met (1,0, 1,0) in de rechterbenedenhoek. Als u bijvoorbeeld wilt opgeven dat de linkerbovenhoek een kwart van de weg naar beneden begint vanaf `cal` de bovenkant en `cat` een kwart van de weg naar binnen vanaf de linkerkant, ingesteld op 0,25 en 0,25.  

De volgende reeks aanroepen toont het effect van het opgeven van het bijsnijdgebied. De eerste oproep omvat geen bijsnijden en Bing herkent twee mensen naast elkaar in het midden van de afbeelding.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Het antwoord toont twee erkende entiteiten.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Bij de tweede oproep wordt de afbeelding verticaal in het midden bijgewassen en herkende Bing één persoon aan de rechterkant van de afbeelding.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Het antwoord toont één erkende entiteit.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Het vinden van visueel vergelijkbare producten  

Als u afbeeldingen wilt vinden die producten bevatten die visueel lijken op de producten in de oorspronkelijke afbeelding, stelt u de parameter [modulesquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) in op Vergelijkbare producten.  

De volgende aanvraag laat zien hoe u afbeeldingen van visueel vergelijkbare producten krijgen. De aanvraag gebruikt de parameter [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) query om de oorspronkelijke afbeelding te identificeren die in een eerdere aanvraag is geretourneerd. Om de relevantie te verbeteren, moet u de querytekenreeks van de gebruiker opnemen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Hieronder ziet u het antwoord op de vorige aanvraag. Het antwoord bevat een afbeelding van een vergelijkbaar product en geeft aan hoeveel verkopers het product online `aggregateOffer` aanbieden, of er productbeoordelingen zijn en de laagste prijs is gevonden (zie het veld).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Als u een lijst wilt krijgen van de verkopers die het product online `modules` aanbieden (zie het veld [offerCount),](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) belt u de API opnieuw aan en stelt u in op ShoppingSources. Stel vervolgens `insightsToken` de queryparameter in op het token in de afbeelding van het productoverzicht.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Het volgende is het antwoord op het vorige verzoek.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
