---
title: Afbeeldingen van het web ophalen - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Image Search API om relevante afbeeldingen van het web te zoeken en te krijgen.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542764"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Afbeeldingen van het web ophalen met de Bing Image Search API

Wanneer u de BING Image Search REST API gebruikt, u afbeeldingen van het web ophalen die gerelateerd zijn aan uw zoekterm door het volgende GET-verzoek te verzenden:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Gebruik de [parameter q-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) voor uw door de URL gecodeerde zoekterm. Bijvoorbeeld, als u *zeilen rubberboten,* `sailing+dinghies` ingesteld `sailing%20dinghies` `q` op of .

> [!IMPORTANT]
> * Alle aanvragen moeten worden ingediend vanaf een server en niet van een client.
> * Als het de eerste keer is dat u een van de Bing-zoek-API's aanroept, neemt u de header client-id niet op. Neem alleen de client-id op als u eerder een Bing-API hebt aangeroepen die een client-id heeft geretourneerd voor de gebruikers- en apparaatcombinatie.

## <a name="get-images-from-a-specific-web-domain"></a>Afbeeldingen ophalen uit een specifiek webdomein

Als u afbeeldingen uit een bepaald domein wilt opvragen, gebruikt de query-operator [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Antwoorden op query's met de `site:` operator kunnen inhoud voor volwassenen bevatten, ongeacht de instelling [safeSearch.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) Gebruik `site:` alleen als u op de hoogte bent van de inhoud op het domein.

## <a name="filter-images"></a>Afbeeldingen filteren

 Standaard retourneert de Image Search API alle afbeeldingen die relevant zijn voor de query. Als u de afbeeldingen wilt filteren die Bing retourneert (bijvoorbeeld om alleen afbeeldingen met een transparante achtergrond of specifieke grootte terug te sturen), gebruikt u de volgende queryparameters:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)—Filter afbeeldingen op beeldverhouding (bijvoorbeeld standaard- of breedbeeldafbeeldingen).
* [kleur](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)—Filter afbeeldingen op dominante kleur of zwart-wit.
* [versheid](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)—Afbeeldingen filteren op leeftijd (bijvoorbeeld afbeeldingen die bing in de afgelopen week heeft ontdekt).
* [hoogte](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [breedte](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)—Afbeeldingen filteren op breedte en hoogte.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)—Filter afbeeldingen op inhoud (bijvoorbeeld afbeeldingen die alleen het gezicht van een persoon weergeven).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)—Afbeeldingen filteren op type (bijvoorbeeld illustraties, geanimeerde GIF's of transparante achtergronden).
* [licentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license): afbeeldingen filteren op het type licentie dat aan de site is gekoppeld.
* [grootte](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)—Afbeeldingen filteren op grootte, zoals kleine afbeeldingen tot 200x200 pixels.

Als u afbeeldingen uit een bepaald domein wilt opvragen, gebruikt de query-operator [site:](https://msdn.microsoft.com/library/ff795613.aspx).

In het volgende voorbeeld ziet u hoe u kleine afbeeldingen krijgen van ContosoSailing.com die Bing de afgelopen week heeft ontdekt.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Reactie-indeling Bing Image Search

Het antwoordbericht van Bing bevat een [antwoord op afbeeldingen](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) met een lijst met afbeeldingen waarvan Cognitive Services heeft vastgesteld dat ze relevant zijn voor de query. Elk [afbeeldingsobject](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) in de lijst bevat de volgende informatie over de afbeelding: de URL, de grootte ervan, de afmetingen, de coderingsindeling, een URL naar een miniatuur van de afbeelding en de afmetingen van de miniatuur.

> [!NOTE]
> * Afbeeldingen moeten worden weergegeven in de volgorde die in het antwoord wordt gegeven.
> * Omdat URL-indelingen en parameters zonder kennisgeving kunnen worden gewijzigd, gebruikt u alle URL's zoals deze zijn. U mag geen afhankelijkheden nemen van de URL-indeling of parameters, behalve waar vermeld.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Wanneer u de Bing Afbeeldingen zoeken-API aanroept, retourneert Bing een lijst met resultaten. De lijst is een subset van het totale aantal resultaten die relevant zijn voor de query. Het veld `totalEstimatedMatches` in de respons bevat een schatting van het aantal afbeeldingen dat beschikbaar is om weer te geven. Zie [Paging Images](../paging-images.md)voor meer informatie over het bekijken van de rest van de afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

Als u de Bing Image Search API nog niet eerder hebt geprobeerd, probeert u een [snelle start](../quickstarts/csharp.md). Als u op zoek bent naar iets complexers, probeert u de zelfstudie om een [web-app met één pagina](../tutorial-bing-image-search-single-page-app.md)te maken.
