---
title: 'Quick Start: een query naar de API verzenden met behulp van node. js-Bing lokale zakelijke zoek opdracht'
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
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873090"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Quick Start: een query verzenden naar de Bing lokale Business Search-API met behulp van node. js

Gebruik deze Quick Start om te leren hoe u aanvragen verzendt naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing wordt geschreven in node. js, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor een zoek query.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).
* De [Java script-aanvraag bibliotheek](https://github.com/request/request).
* Een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met Bing zoeken-API's. Voor deze Quick start is de [gratis proef versie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) voldoende. Sla de API-sleutel op die wordt weer gegeven wanneer u de gratis proef versie activeert. Zie [Cognitive Services prijzen-Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)voor meer informatie.


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
