---
title: 'Snelstartgids: zoeken naar afbeeldingen met behulp van de Bing Image Search REST API en Node.js'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om Zoek opdrachten voor afbeeldingen naar het Bing Image Search te verzenden REST API met behulp van Java script en JSON-reacties.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2c411a0c8ba838bdac8c0e487e5dcc54db522311
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85603397"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Snelstartgids: zoeken naar afbeeldingen met behulp van de Bing Image Search REST API en Node.js

Gebruik deze Quick Start om te leren hoe u zoek aanvragen naar de Bing Afbeeldingen zoeken-API verzendt. Deze JavaScript-toepassing verzendt een zoekquery naar de API en geeft de URL weer van de eerste afbeelding in de resultaten. Hoewel deze toepassing wordt geschreven in Java script, is de API een REST-webservice die compatibel is met de meeste programmeer talen.

De bron code voor dit voor beeld is beschikbaar [op github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) met aanvullende fout afhandeling en annotaties.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [Java script-aanvraag bibliotheek](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Zie [Cognitive Services prijzen-Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)voor meer informatie.

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw Java script-bestand in uw favoriete IDE of editor en stel de beperkende en HTTPS-vereisten in.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Maak variabelen voor het API-eindpunt, het zoekpad voor de afbeeldings-API, uw abonnementssleutel en zoekterm. Voor `host` kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Compileer de zoekopdracht en een query.

1. Gebruik de variabelen uit de laatste stap om een zoek-URL voor de API-aanvraag te formatteren. URL: Codeer uw zoek term voordat u deze naar de API verzendt.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Gebruik de aanvraagbibliotheek om uw query naar de API te verzenden. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Het antwoord verwerken en parseren

1. Definieer een functie met de naam `response_handler` en als parameter de HTTP-aanroep `response`. 

2. In deze functie definieert u een variabele die de hoofd tekst van het JSON-antwoord bevat. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Sla de hoofd tekst van het antwoord op wanneer de `data` markering wordt aangeroepen.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Wanneer een `end` vlag is gesignaleerd, haalt u het eerste resultaat op uit het JSON-antwoord. Druk de URL van de eerste afbeelding af, samen met het totale aantal geretourneerde afbeeldingen.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Antwoorden die afkomstig zijn van de Bing Afbeeldingen zoeken-API, worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app met één pagina maken](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zie ook

* [Wat is de Bing Afbeeldingen zoeken-API?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interactieve demo proberen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Prijs informatie voor de Bing Zoeken-API's](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Documentatie voor Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Naslag voor Bing Afbeeldingen zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
