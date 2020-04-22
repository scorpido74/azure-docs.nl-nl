---
title: Intent krijgen met REST-oproep in Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733331"
---
## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/)-programmeertaal
* [Visual Studio Code](https://code.visualstudio.com/)
* Een LUIS-app-id - gebruik de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`openbare IoT-app-id van . De gebruikersquery die wordt gebruikt in de quickstartcode is specifiek voor die app.

## <a name="create-luis-runtime-key-for-predictions"></a>Luis-runtime-sleutel maken voor voorspellingen

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Klik [op **Taalverstaan maken** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor **Runtime-toets** in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Passend abonnement selecteren|
    |Locatie|Selecteer een nabijgelegen en beschikbare locatie|
    |Prijscategorie|`F0`- de minimale prijscategorie|
    |Resourcegroep|Een beschikbare resourcegroep selecteren|

1. Klik **op Maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, navigeert u naar de resourcepagina.
1. Verzamel geconfigureerd `endpoint` en `key`een .

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik Node.js om het [voorspellingseindpunt](https://aka.ms/luis-apim-v3-prediction) op te vragen en een voorspellingsresultaat te krijgen.

1. Kopieer het volgende codefragment `predict.js`naar een bestand met de naam :

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

1. Vervang `YOUR-KEY` de `YOUR-ENDPOINT` waarden en waarden door uw eigen voorspelling **Runtime-toets** en eindpunt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32-tekenvoorspelling **Runtime-toets.**|
    |`YOUR-ENDPOINT`| Uw voorspelling URL eindpunt. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installeer `request`de `request-promise`afhankelijkheden en `querystring` afhankelijkheden met deze opdracht:

    ```console
    npm install request request-promise querystring
    ```

1. Voer uw app uit met deze opdracht:

    ```console
    node predict.js
    ```

 1. Bekijk de voorspellingsrespons, die wordt geretourneerd als JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    De JSON-respons die is opgemaakt voor leesbaarheid:

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

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen toevoegen en trainen](../get-started-get-model-rest-apis.md)