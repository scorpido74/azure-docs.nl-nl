---
title: Snelstart - Een query naar de API verzenden met Node.js - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstart om aanvragen te verzenden naar de Bing Local Business Search API, een Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: d366195f9cd72e6baa88c17203ae93cbbc6cbe6a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475532"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snelstart: een query verzenden naar de Bing Local Business Search API met Node.js

Gebruik deze snelstart om aanvragen te verzenden naar de Bing Local Business Search API, een Azure Cognitive Service. Hoewel deze eenvoudige toepassing is geschreven in Node.js, is de API een RESTful Web-service die compatibel is met elke programmeertaal die HTTP-verzoeken kan uitvoeren en JSON kan ontleden.

Met deze voorbeeldtoepassing worden lokale antwoordgegevens `hotel in Bellevue`uit de API voor de zoekopdracht opgehaald.

## <a name="prerequisites"></a>Vereisten

* Nieuwste versie van [Node.js](https://nodejs.org/en/download/).

* De [JavaScript-aanvragenbibliotheek](https://github.com/request/request)

U moet een [API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) voor Cognitive Services hebben met Bing-API's. De [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) is voldoende voor deze quickstart. Gebruik de toegangssleutel van de gratis proefperiode.  Zie ook [Prijsinformatie Cognitive Services - Bing Zoeken-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Codescenario

De volgende code wordt gedefinieerd en verzendt het verzoek. De code wordt in de volgende stappen geïmplementeerd:

1. Declareer variabelen om het eindpunt op te geven met een host en pad.
2. Geef de query op en voeg de queryparameter toe.
3. Maak een handlerfunctie voor het antwoord.
4. Definieer de Search-functie die de aanvraag maakt en de header Ocp-Apim-Subscription-Key toevoegt.
5. Voer de Search-functie uit.

Dit is de volledige code voor deze demo:

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

* [Zoeken in lokaal bedrijf snel gestart](local-quickstart.md)
* [Lokaal zakelijk zoeken Java snelaan de dag](local-search-java-quickstart.md)
* [Lokaal zoeken in Business Python snel gestart](local-search-python-quickstart.md)
