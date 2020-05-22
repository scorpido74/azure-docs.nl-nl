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
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 72afc4f6e03b24e545ad18948119d418970cddf3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747580"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole REST API en node. js

Gebruik deze quickstart om uw eerste aanroep naar de Bing Spellingcontrole REST API te maken. Deze eenvoudige knooppunt toepassing stuurt een aanvraag naar de API en retourneert een lijst met woorden die niet zijn herkend, gevolgd door voorgestelde correcties. Hoewel deze toepassing is geschreven in node. js, is de API een REST-webservice die compatibel is met de meeste programmeer talen. De bron code voor deze toepassing is beschikbaar op [github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Vereisten

* [Node.js 6](https://nodejs.org/en/download/) of later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw JavaScript-bestand in uw favoriete IDE of editor. Stel de beperking in en vereist `https` . Maak vervolgens variabelen voor de host en het pad van het API-eindpunt en uw abonnementssleutel. U kunt het volgende globale eind punt gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Maak variabelen voor de zoek parameters en de tekst die u wilt controleren. Voeg uw markt code toe na `mkt=` . De markt code is het land/de regio waarin u de aanvraag maakt. Voeg ook de modus voor spelling controle toe `&mode=` . Modus (de `proof` meeste spelling-en grammatica fouten worden onderschept) of `spell` (de meeste spelling wordt niet zo veel grammatica fouten onderschept).

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


## <a name="run-the-application"></a>De toepassing uitvoeren

Uw project bouwen en uitvoeren.

Als u de opdracht regel gebruikt, gebruikt u de volgende opdrachten om de toepassing te bouwen en uit te voeren.

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
- [Referentie voor de Bing Spellingcontrole-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
