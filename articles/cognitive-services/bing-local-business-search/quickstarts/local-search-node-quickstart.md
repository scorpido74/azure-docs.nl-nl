---
title: 'Quick Start: een query verzenden naar de Bing lokale Business Search-API met behulp van node. js'
titleSuffix: Azure Cognitive Services
description: Begin met het gebruik van de Bing lokale zakelijke zoek-API in het knoop punt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 02513d0596588b4e6ba05edf5342769e78c24242
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996665"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Quickstart: Een query naar de Bing Local Business Search-API verzenden met behulp van node. js

Gebruik deze Quick Start om aanvragen te verzenden naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing wordt geschreven in node. js, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor de `hotel in Bellevue`Zoek query.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [JavaScript-aanvragenbibliotheek](https://github.com/request/request)

U moet een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) hebben met Bing-api's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze snelstartgids. Gebruik de toegangs sleutel die wordt gebruikt door de gratis proef versie.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Codescenario

Met de volgende code wordt de aanvraag gedefinieerd en verzonden. De code wordt geïmplementeerd in de volgende stappen:

1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de query op en voeg de query parameter toe.
3. Maak een handlerfunctie voor het antwoord.
4. Definieer de zoek functie die de aanvraag maakt en voegt de header van de APIM-abonnements sleutel toe.
5. Voer de Search-functie uit.

Dit is de volledige code voor deze demo:

```
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

* [Snelstartgids voor lokale zakelijke Zoek opdrachten](local-quickstart.md)
* [Lokale zakelijke zoek opdracht voor zoeken in Java Quick Start](local-search-java-quickstart.md)
* [Snelstartgids voor lokale zakelijke Zoek opdrachten python](local-search-python-quickstart.md)
