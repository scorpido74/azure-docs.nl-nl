---
title: 'Quick Start: een query naar de API verzenden met behulp van Node.js-Bing lokale zakelijke zoek opdracht'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om aanvragen te verzenden naar de Bing lokale Business Search-API, een Azure-cognitieve service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 2b5de1c5bf99ff2516a0eb836d540fc2833d99cb
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611251"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snelstartgids: een query naar de Bing Local Business Search-API verzenden met Node.js

Gebruik deze Quick Start om te leren hoe u aanvragen verzendt naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing wordt geschreven in Node.js, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor een zoek query.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).
* De [Java script-aanvraag bibliotheek](https://github.com/request/request).
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" maakt u een Bing Search resource Maak "  target="_blank"> een Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.


## <a name="code-scenario"></a>Codescenario

Met de volgende code wordt de aanvraag gedefinieerd en verzonden, die in de volgende stappen wordt geïmplementeerd:

1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de query op en voeg de query parameter toe.
3. Maak een handlerfunctie voor het antwoord.
4. Definieer de zoek functie die de aanvraag maakt en voegt de `Ocp-Apim-Subscription-Key` header toe.
5. Voer de zoek functie uit.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Volgende stappen

* [Lokale zakelijke zoek acties C# Quick Start](local-quickstart.md)
* [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](local-search-java-quickstart.md)
* [Snelstartgids voor lokale zakelijke Zoek opdrachten python](local-search-python-quickstart.md)
