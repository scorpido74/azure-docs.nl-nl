---
title: Doel intentie met REST-aanroep in node. js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733331"
---
## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/)-programmeertaal
* [Visual Studio Code](https://code.visualstudio.com/)
* Een LUIS-App-ID: gebruik de open bare IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`-app-id van. De gebruikers query die in de Quick Start code wordt gebruikt, is specifiek voor die app.

## <a name="create-luis-runtime-key-for-predictions"></a>Een LUIS-runtime sleutel maken voor voor spellingen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **Language Understanding** maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor de **runtime** sleutel in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. Verzameling geconfigureerd `endpoint` en een `key`.

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik node. js om het [Voorspellings eindpunt](https://aka.ms/luis-apim-v3-prediction) te doorzoeken en een Voorspellings resultaat te krijgen.

1. Kopieer het volgende code fragment naar een bestand met `predict.js`de naam:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Vervang de `YOUR-KEY` waarden `YOUR-ENDPOINT` en door uw eigen Voorspellings **runtime** sleutel en eind punt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|De **runtime** -sleutel voor de voor spelling van 32-tekens.|
    |`YOUR-ENDPOINT`| Het eind punt voor de voor Spellings-URL. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installeer de `request`- `request-promise`en `querystring` -afhankelijkheden met deze opdracht:

    ```console
    npm install request request-promise querystring
    ```

1. Voer uw app uit met de volgende opdracht:

    ```console
    node predict.js
    ```

 1. Bekijk het Voorspellings antwoord dat wordt geretourneerd als JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    Het JSON-antwoord dat is opgemaakt voor de Lees baarheid:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen](../get-started-get-model-rest-apis.md)