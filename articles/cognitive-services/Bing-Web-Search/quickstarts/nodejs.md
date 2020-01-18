---
title: 'Quick Start: een webzoekopdracht uitvoeren met behulp van node. js-Bing Web Search REST API'
titleSuffix: Azure Cognitive Services
description: Gebruik deze snelstartgids om aanvragen naar de REST API van Bing Web Search te verzenden via Node.js en een JSON-antwoord te ontvangen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 54f4b38e01b51289319390779a140346befc6f0c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168807"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Quick Start: zoeken op internet met behulp van de Bing Web Search REST API en node. js

Gebruik deze quickstart om voor het eerst de Bing Webzoekopdrachten-API aan te roepen en een JSON-antwoord te ontvangen. Met deze node. js-toepassing wordt een zoek opdracht naar de API verzonden en wordt het antwoord weer gegeven. Hoewel deze toepassing in JavaScript is geschreven, is de API een RESTful-webservice die compatibel is met vrijwel elke programmeertaal.

## <a name="prerequisites"></a>Vereisten

U moet over het volgende beschikken voordat u verdergaat met deze snelstartgids:

* [Node.js 6](https://nodejs.org/en/download/) of later
* Een abonnementssleutel

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Een project maken en de vereiste modules declareren

Maak een nieuw Node.js-project in uw favoriete IDE of editor.
Kopieer vervolgens het codefragment hieronder in uw project in een bestand met de naam `search.js`.

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Abonnementssleutel instellen

Dit codefragment gebruikt de omgevingsvariabele `AZURE_SUBSCRIPTION_KEY` voor het opslaan van de abonnementssleutel, een goede gewoonte om te voorkomen dat uw sleutels per ongeluk worden weergegeven wanneer u code implementeert. Ga naar de [pagina uw api's](https://azure.microsoft.com/try/cognitive-services/my-apis/?apiSlug=search-api-v7) om uw abonnements sleutel op te zoeken.

Als u niet bekend bent met het gebruik van omgevingsvariabelen, of als u deze app zo snel mogelijk wilt uitvoeren, kunt u `process.env['AZURE_SUBSCRIPTION_KEY']` vervangen door uw abonnementssleutel die is ingesteld als een tekenreeks.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Een functie maken voor het versturen van de aanvraag

Met deze functie maakt u een beveiligde GET-aanvraag, waarbij de zoekopdracht als een queryparameter wordt opgeslagen in het pad. `hostname` kunnen het globale eind punt hieronder zijn of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) -eind punt dat wordt weer gegeven in de Azure portal voor uw resource.  `encodeURIComponent` wordt gebruikt als escape voor ongeldige tekens, en de abonnementssleutel wordt doorgegeven in een header. De callback ontvangt een [respons](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse) die zich abonneert op de gebeurtenis `data` om de JSON-hoofdtekst te aggregeren, op `error` om eventuele problemen vast te leggen en op `end` om te weten wanneer het bericht moet worden beschouwd als voltooid. Als de aanroep is voltooid, worden de interessante headers en hoofdtekst weergegeven door de app. U kunt experimenteren met de kleuren en de diepte instellen op basis van uw voorkeur. Een diepte van `1` biedt een goed overzicht van het antwoord.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>De query bepalen

We bekijken de argumenten van het programma om de query te vinden. Het eerste argument is het pad naar het knooppunt, het tweede de bestandsnaam en het derde de query. Als de query afwezig is, wordt de standaardquery 'Microsoft Cognitive Services' gebruikt.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Een aanvraag maken en het antwoord weergeven

En nu alles is gedefinieerd, kunnen we onze functie gaan aanroepen.

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Alles samenvoegen

De laatste stap is het uitvoeren van uw code: `node search.js "<your query>"`.

Als u uw code wilt vergelijken met die van ons, volgt hier het volledige programma:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="sample-response"></a>Voorbeeldantwoord

Antwoorden afkomstig van de Bing Webzoekopdrachten-API worden geretourneerd in de JSON-indeling. Dit voorbeeldantwoord is ingekort zodat één resultaat wordt weergegeven.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie voor app met één pagina voor Bing Web Search ](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
