---
title: 'Snelstartgids: een zoek opdracht naar de REST API verzenden met behulp van node. js-Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om een aanvraag naar de Bing Entiteiten zoeken-REST API te verzenden via C# en een JSON-antwoord te ontvangen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 48d2b36e35a2e0b41b1202beda9944339dc7530c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327117"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Snelstartgids: een zoek opdracht naar het Bing Entity Search REST API verzenden met behulp van node. js

Gebruik deze quickstart om voor het eerst de Bing Entiteiten zoeken-API aan te roepen en het JSON-antwoord te bekijken. Deze eenvoudige JavaScript-toepassing stuurt een nieuwszoekquery naar de API en geeft het antwoord weer. De broncode voor dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [JavaScript-aanvragenbibliotheek](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor, en stel de vereisten voor striktheid en https in.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Maak variabelen voor het API-eindpunt, uw abonnementssleutel en zoekquery.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Voeg de markt- en queryparameters toe aan string `query`. Voer voor uw query een URL-codering uit met `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Het antwoord verwerken en parseren

1. Definieer een functie met de naam `response_handler` en als parameter de HTTP-aanroep `response`. Voer binnen deze functie de volgende stappen uit:

    1. Definieer een variabele voor de hoofdtekst van het JSON-antwoord.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. De hoofdtekst van het antwoord opslaan wanneer de **gegevens**vlag wordt aangeroepen
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Wanneer een **End** -vlag is gesignaleerd, parseert u de JSON en drukt u deze af.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Een aanvraag versturen

1. Maak een functie met de naam `Search` om een zoekaanvraag te verzenden. Voer hierin de volgende stappen uit.

   1. Maak een JSON-object die uw aanvraagparameters bevat: gebruik `Get` voor de methode en voeg de gegevens over uw host en pad in. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header. 
   2. Gebruik `https.request()` om de aanvraag te verzenden met de antwoordhandler die u eerder hebt gemaakt, en met de zoekparameters.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. Roep de functie `Search()` aan.

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../tutorial-bing-entities-search-single-page-app.md).

* [Wat is de Bing Entiteiten zoeken-API?](../overview.md )
* [Naslaghandleiding Bing Entiteiten zoeken-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
