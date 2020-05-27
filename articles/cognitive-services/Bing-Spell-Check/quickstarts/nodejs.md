---
title: 'Snelstartgids: Controleer de spelling met de REST API en node. js-Bing Spellingcontrole'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de Bing Spellingcontrole REST API om de spelling en grammatica te controleren met deze Snelstartgids.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 3a98308716696f677f04db66e83ff4e9d5d08c85
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869840"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole REST API en node. js

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige Java script-toepassing stuurt een aanvraag naar de API en retourneert een lijst met voorgestelde correcties. 

Hoewel deze toepassing wordt geschreven in Java script, is de API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor deze toepassing is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js 6](https://nodejs.org/en/download/) of later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor. Stel de beperking in en vereist `https` . Maak vervolgens variabelen voor de host, het pad en uw abonnements sleutel van uw API-eind punt. U kunt het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Maak variabelen voor de zoek parameters en de tekst die u wilt controleren: 

   a. Wijs uw markt code toe aan de `mkt` para meter met de `=` operator. De markt code is de code van het land of de regio waarvan u de aanvraag maakt. 

   b. Voeg de `mode` para meter met de `&` operator toe en wijs vervolgens de spelling controle modus toe. De modus kan ofwel `proof` (de meeste spelling-en grammatica fouten) of `spell` (de meeste spel fouten worden onderschept, maar niet zo veel grammatica fouten).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Parameters voor de aanvraag maken

Maak aanvraagparameters door een nieuw object met een methode van het type `POST` te maken. Voeg uw pad toe door uw eindpuntpad en de queryreeks toe te voegen. Voeg vervolgens uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` koptekst.

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

Maak een functie met de naam `response_handler` om een JSON-antwoord van de API weer te geven. Maak een variabele voor de hoofdtekst van het antwoord. Het antwoord toevoegen wanneer een `data` markering wordt ontvangen met behulp van `response.on()` . Nadat een `end` markering is ontvangen, drukt u de JSON-hoofd tekst af naar de-console.

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

Aanroepen van de API met behulp `https.request()` van uw aanvraag parameters en een antwoord-handler. Schrijf uw tekst naar de API en beëindig vervolgens de aanvraag.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>De toepassing uitvoeren

1. Uw project bouwen en uitvoeren.

1. Als u de opdracht regel gebruikt, gebruikt u de volgende opdracht om de toepassing te bouwen en uit te voeren:

   ```bash
   node <FILE_NAME>.js
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
- [Naslag informatie over Bing Spellingcontrole-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
