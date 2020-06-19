---
title: 'Snelstartgids: Afdruk bare tekst extra heren, REST, Node.js'
titleSuffix: Azure Cognitive Services
description: In deze snelstart extraheert u tekst uit een afbeelding met behulp van de Computer Vision-API met Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9c245ed91d710ae4bb77e45d8b9c3cceaedc252a
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986738"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-nodejs"></a>Snelstartgids: Afdruk bare tekst (OCR) extra heren met behulp van de Computer Vision REST API en Node.js

> [!NOTE]
> Als u Engelse taal tekst wilt extra heren, kunt u overwegen de nieuwe [Lees bewerking](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)te gebruiken.

In deze Quick Start haalt u gedrukte tekst op met behulp van de Computer Vision REST API met OCR (optische teken herkenning) van een afbeelding. Met de [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) -methode kunt u gedrukte tekst in een afbeelding detecteren en herkende tekens uitpakken in een door een machine bruikbare teken stroom.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4. x of hoger 
* [NPM](https://www.npmjs.com/) 
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource Maak "  target="_blank"> een computer vision resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt **u op Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Computer Vision-service. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.
* [Maak omgevings variabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel-en eind punt-URL, `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk met de naam en `COMPUTER_VISION_ENDPOINT` .

## <a name="create-and-run-the-sample"></a>Het voorbeeld maken en uitvoeren

U kunt het voorbeeld maken en uitvoeren aan de hand van de volgende stappen:

1. Installeer het NPM- [`request`](https://www.npmjs.com/package/request) pakket.
   1. Open een opdrachtpromptvenster als beheerder.
   1. Voer de volgende opdracht uit:

      ```console
      npm install request
      ```

   1. Sluit het opdrachtpromptvenster nadat het pakket is geïnstalleerd.

1. Kopieer de volgende code naar een teksteditor.
1. Vervang optioneel de waarde van `imageUrl` door de URL van een andere afbeelding waaruit u gedrukte tekst wilt extraheren.
1. Sla de code op als een bestand met de extensie `.js`. Bijvoorbeeld `get-printed-text.js`.
1. Open een opdrachtpromptvenster.
1. Gebruik de opdracht `node` in de prompt om het bestand uit te voeren. Bijvoorbeeld `node get-printed-text.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.0/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Het antwoord bekijken

Een geslaagd antwoord wordt geretourneerd in JSON-indeling. Het voorbeeld parseert en geeft een geslaagd antwoord weer in het opdrachtpromptvenster dat vergelijkbaar is met het volgende voorbeeld:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het bestand wanneer u het niet meer nodig hebt en verwijder vervolgens het npm-pakket `request`. Voer de volgende stappen uit om het pakket te verwijderen:

1. Open een opdrachtpromptvenster als beheerder.
2. Voer de volgende opdracht uit:

   ```console
   npm uninstall request
   ```

3. Sluit het opdrachtpromptvenster nadat het pakket is verwijderd.

## <a name="next-steps"></a>Volgende stappen

Verken vervolgens de Computer Vision Api's die worden gebruikt voor het analyseren van een afbeelding, het detecteren van beroemdheden en bezienswaardigheden, het maken van een miniatuur en het extra heren van gedrukte en handgeschreven tekst.

> [!div class="nextstepaction"]
> [De Computer Vision-API verkennen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
