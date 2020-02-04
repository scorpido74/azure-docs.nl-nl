---
title: Model met REST-aanroep ophalen inC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 056b2d2b1951b6630b61bbd6fd8a8c38b272900a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966915"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding: resource 32-teken sleutel en bewerkings eind punt-URL ontwerpen. Maken met de [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure cli](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) -app uit de cognitieve-Services-Language-Standing github-opslag plaats.
* De LUIS-toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-ID in de toepassing die de uitingen ontvangt. De standaard-id is '0.1'.
* [Node.js](https://nodejs.org/)-programmeertaal
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Model via een programma wijzigen

Gebruik Go om een door de machine geleerde entiteits- [API](https://aka.ms/luis-apim-v3-authoring) aan de toepassing toe te voegen.

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

1. Vervang de waarden die beginnen met `YOUR-` met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|De bewerkings sleutel voor uw 32-teken.|
    |`YOUR-ENDPOINT`| Het eind punt van de ontwerp-URL. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`. U stelt de naam van de resource in wanneer u de resource hebt gemaakt.|
    |`YOUR-APP-ID`| De ID van uw LUIS-app. |

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-Portal in de sectie beheren op de pagina **Azure-resources** . De App-ID is beschikbaar in hetzelfde gedeelte beheren op de pagina **Toepassings instellingen** .

1. Voer bij een opdracht prompt in de map waarin u het bestand hebt gemaakt de volgende opdracht uit om het bestand uit te voeren:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)