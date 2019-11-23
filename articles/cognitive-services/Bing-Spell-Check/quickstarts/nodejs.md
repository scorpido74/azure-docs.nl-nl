---
title: 'Quickstart: Check spelling with the REST API and Node.js - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole-REST API om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: ab8f1d52b5a0b9f5f2539de0acc4728277f9f7b2
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378825"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Quickstart: Check spelling with the Bing Spell Check REST API and Node.js

Gebruik deze snelstartgids om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. This simple Node application sends a request to the API and returns a list of words it didn't recognize, followed by suggested corrections. While this application is written in Node.js, the API is a RESTful Web service compatible with most programming languages. De broncode voor deze toepassing is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js 6](https://nodejs.org/en/download/) of later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor. Set the strictness, and require `https`. Maak vervolgens variabelen voor de host en het pad van het API-eindpunt en uw abonnementssleutel.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Create variables for your search parameters and the text you want to check. Append your market code after `mkt=`. The market code is the country you make the request from. Also, append your spell-check mode after `&mode=`. Mode is either `proof` (catches most spelling/grammar errors) or `spell` (catches most spelling but not as many grammar errors).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Parameters voor de aanvraag maken

Maak aanvraagparameters door een nieuw object met een methode van het type `POST` te maken. Voeg uw pad toe door uw eindpuntpad en de queryreeks toe te voegen. Voeg uw abonnementssleutel toe aan de `Ocp-Apim-Subscription-Key`-header.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Een antwoordhandler maken

Maak een functie met de naam `response_handler` om een JSON-antwoord van de API weer te geven. Maak een variabele voor de hoofdtekst van het antwoord. Voeg met behulp van `response.on()` het antwoord toe als er een `data`-vlag wordt ontvangen. Wanneer de vlag `end` wordt ontvangen, geeft u de JSON-hoofdtekst weer in de console.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>De aanvraag verzenden

Roep de API aan met behulp van `https.request()`, met de parameters van de aanvraag en de antwoordverwerker. Schrijf de tekst naar de API en beëindig daarna de aanvraag.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Voorbeeld van JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](../tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](../overview.md)
- [Referentie voor de Bing Spellingcontrole-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
