---
title: 'Model krijgen met REST-oproep in C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368396"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding - Authoring resource 32 tekensleutel en url voor het schrijven van eindpunten. Maken met de [Azure-portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent-app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) vanuit de GitHub-repository die gebruik maakt van de GitHub-opslagplaats voor cognitieve services.
* De LUIS toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt. De standaard-id is '0.1'.
* [Node.js](https://nodejs.org/)-programmeertaal
* [Visual Studio-code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een nieuw bestand met de naam `model.js`. Voeg de volgende code toe:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];

    const main = async() =>{


        await addUtterance();
        await train("POST");
        await trainStatus("GET");

    }
    const addUtterance = async () => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```

1. Vervang de waarden `YOUR-` die beginnen met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32 teken authoring toets.|
    |`YOUR-ENDPOINT`| Uw URL-eindpunt voor het ontwerpen. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`. U stelt uw resourcenaam in toen u de resource maakte.|
    |`YOUR-APP-ID`| Uw LUIS-app-id. |

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-portal in de sectie Beheren op de pagina **Azure-bronnen.** De app-id is beschikbaar in dezelfde sectie Beheren, op de pagina **Toepassingsinstellingen.**

1. Voer de volgende opdracht in om het bestand uit te voeren met een opdrachtprompt in dezelfde map als waar u het bestand hebt gemaakt:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)