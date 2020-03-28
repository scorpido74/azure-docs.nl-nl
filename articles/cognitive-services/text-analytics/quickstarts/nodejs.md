---
title: 'Snelstart: gebruik Node.js om de Text Analytics REST API aan te roepen'
titleSuffix: Azure Cognitive Services
description: In deze quickstart u informatie en codevoorbeelden opvragen om u snel aan de slag te helpen met de Text Analytics API in Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.custom: seo-javascript-september2019
ms.openlocfilehash: c111937dbbea5e588e82bc9753a71d1d597ca767
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378786"
---
# <a name="quickstart-use-nodejs-to-call-the-text-analytics-cognitive-service"></a>Snelstart: gebruik Node.js om de Text Analytics Cognitive Service te bellen  
<a name="HOLTop"></a>

In dit artikel ziet u hoe u de  [Text Analytics-API's](//go.microsoft.com/fwlink/?LinkID=759711)  met Node.js kunt gebruiken om [taal te detecteren](#Detect), [sentiment te analyseren](#SentimentAnalysis), [sleuteltermen op te halen](#KeyPhraseExtraction) en [gekoppelde entiteiten te identificeren](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Taal detecteren

Met de Language Detection-API wordt de taal van een tekstdocument gedetecteerd met behulp van de [methode Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Maak een nieuw Node.JS-project in uw favoriete IDE of een map op uw bureaublad.
1. Voeg de onderstaande code `.js` toe aan een nieuw bestand.
1. Kopieer uw sleutel en eindpunt naar de code. 
1. Voer het programma uit vanaf uw IDE- of opdrachtregel, bijvoorbeeld `npm start` of `node detect.js`.

```javascript
'use strict';

let https = require ('https');
subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/languages';

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

let get_language = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'text': 'This is a document written in English.' },
        { 'id': '2', 'text': 'Este es un document escrito en Español.' },
        { 'id': '3', 'text': '这是一个用中文写的文件' }
    ]
};

get_language(documents);
```

**Antwoord voor taaldetectie**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Stemming analyseren

Met de Sentiment Analysis-API wordt een set tekstrecords gedetecteerd met behulp van de [methode Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). Sentiment analyse kan worden gebruikt om erachter te komen wat klanten denken van uw merk of onderwerp door het analyseren van ruwe tekst voor aanwijzingen over positief of negatief sentiment. In het volgende voorbeeld worden scores gegeven voor twee documenten, een in het Engels en een in het Spaans.

1. Maak een nieuw Node.JS-project in uw favoriete IDE of een map op uw bureaublad.
1. Voeg de onderstaande code `.js` toe aan een nieuw bestand.
1. Kopieer uw Text Analytics-sleutel en eindpunt naar de code. 
1. Voer het programma uit vanaf uw IDE- of opdrachtregel, bijvoorbeeld `npm start` of `node sentiment.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/sentiment';

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

let get_sentiments = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
    ]
};

get_sentiments(documents);
```

**Antwoord bij sentimentanalyse**

Het resultaat wordt als positief gemeten als het dichter bij 1.0 en negatief wordt gescoord als het dichter bij 0.0 wordt gescoord.
Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Belangrijke woordgroepen herkennen

Met de Key Phrase Extraction-API worden sleuteltermen opgehaald uit een tekstdocument met behulp van de [methode Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Sleutelzinextractie wordt gebruikt om snel de belangrijkste punten van een document of tekst te identificeren. In het volgende voorbeeld worden sleuteltermen opgehaald voor zowel de Engelse als Spaanse documenten.

1. Maak een nieuw Node.JS-project in uw favoriete IDE of een map op uw bureaublad.
1. Voeg de onderstaande code `.js` toe aan een nieuw bestand.
1. Kopieer uw Text Analytics-sleutel en eindpunt naar de code. 
1. Voer het programma uit vanaf uw IDE- of opdrachtregel, bijvoorbeeld `npm start` of `node key-phrases.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/keyPhrases';

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

let get_key_phrases = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
        { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
    ]
};

get_key_phrases(documents);
```

**Antwoord bij extraheren van sleuteltermen**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Gekoppelde entiteiten identificeren

De Entities-API identificeert bekende entiteiten in een tekstdocument, met behulp van de [methode Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). [Entiteiten](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) halen woorden uit tekst, zoals 'Verenigde Staten', en geven u vervolgens het type en/of wikipedia-koppeling voor dit woord(en). Het type voor "Verenigde `location`Staten" is , `https://en.wikipedia.org/wiki/United_States`terwijl de link naar Wikipedia is .  In het volgende voorbeeld worden entiteiten geïdentificeerd voor Engelse documenten.

1. Maak een nieuw Node.JS-project in uw favoriete IDE of een map op uw bureaublad.
1. Voeg de onderstaande code `.js` toe aan een nieuw bestand.
1. Uw tekstanalysesleutel en eindpunt naar de code kopiëren
1. Voer het programma uit vanaf uw IDE- of opdrachtregel, bijvoorbeeld `npm start` of `node entities.js`.

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v2.1/entities';

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

let get_entities = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'Microsoft is an It company.' }
    ]
};

get_entities(documents);
```

**Antwoord bij extraheren van entiteiten**

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Tekstanalyse met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zie ook 

 [Overzicht van Text Analytics](../overview.md)  
 [Veelgestelde vragen (FAQ)](../text-analytics-resource-faq.md)
