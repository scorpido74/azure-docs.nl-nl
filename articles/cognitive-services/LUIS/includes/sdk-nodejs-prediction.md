---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 05e668ff5b0ec19c5e380cf6bfee4b6e46900b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371921"
---
Gebruik de runtime-clientbibliotheek voor taalbegrip (LUIS) voor Node.js om:

* Voorspelling per sleuf
* Voorspelling per versie

[Voorbeeld van](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | naslagdocumentatieBibliotheekbroncode[Library source code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Runtime Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Vereisten

* Runtime-bron voor taalgebruik: [er een maken in de Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-runtime-key"></a>Luis-runtime-toets (Language Understanding)

Haal uw [runtime-sleutel](../luis-how-to-azure-subscription.md) op door een LUIS runtime-bron te maken. Bewaar uw sleutel en het eindpunt van de sleutel voor de volgende stap.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Een nieuw javascriptbestand (Node.js) maken

Maak een nieuw javascriptbestand in uw voorkeurseditor of IDE met de naam `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>De NPM-bibliotheek voor de LUIS-runtime installeren

Installeer in de toepassingsmap de afhankelijkheden met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objectmodel

De tekstclient voor taalbegrip (LUIS) is een [LUISAuthoringClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) dat is geverifieerd naar Azure, dat uw ontwerpsleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* [Voorspelling](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` per `production` sleuf of sleuf
* [Voorspelling per versie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de luis-voorspellingsclientbibliotheek (Language Understanding):

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand in `luis_prediction.js` de projectmap in de voorkeurseditor of IDE. Voeg de volgende afhankelijkheden toe:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor uw eigen benodigde LUIS-informatie:

    Voeg variabelen toe om uw voorspellingssleutel `LUIS_RUNTIME_KEY`te beheren die is opgehaald uit een omgevingsvariabele met de naam . Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

    Maak een variabele om `LUIS_RUNTIME_ENDPOINT`de naam van uw resource vast te houden.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Maak een variabele voor de app-id als een omgevingsvariabele met de naam `LUIS_APP_ID`. Stel de omgevingsvariabele in op **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** de openbare IoT-app. Maak een variabele `production` om de gepubliceerde sleuf in te stellen.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Maak een object msRest.ApiKeyCredentials met uw sleutel en gebruik het met uw eindpunt om een LUIS te [maken. LUISRuntimeClient-object.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Krijg voorspelling van runtime

Voeg de volgende methode toe om de aanvraag te maken aan de voorspellingsruntime.

De uiting van de gebruiker maakt deel uit van het [object predictionRequest.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)

De methode **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** heeft verschillende parameters nodig, zoals de app-id, de sleufnaam en het object voorspellingsverzoek om aan de aanvraag te voldoen. De andere opties, zoals verbose, toon alle intenties en logboek zijn optioneel.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden aan elkaar te koppelen om de voorspelling te krijgen.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `node luis_prediction.js` uit met de opdracht uit uw toepassingsmap.

```console
node luis_prediction.js
```

Het voorspellingsresultaat retourneert een JSON-object:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
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

Wanneer u klaar bent met uw voorspellingen, ruim t-up van het werk van deze quickstart door het verwijderen van het bestand en de submappen.
