---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 504ba9106cc9d617858e8fad7ea421c8707707b3
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171265"
---
Gebruik de Language Understanding (LUIS) runtime-client bibliotheek voor node. js voor het volgende:

* Voor spelling per sleuf
* Voor spelling per versie

[Referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)  |  [Bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)  |  van bibliotheek [Runtime pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  Voor [beelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Vereisten

* Language Understanding runtime-resource: [Maak er een in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Een LUIS-App-ID: gebruik de open bare IoT-App-ID van `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . De gebruikers query die in de Quick Start code wordt gebruikt, is specifiek voor die app.

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-runtime-key"></a>De runtime sleutel van uw Language Understanding (LUIS) ophalen

Down load uw [runtime sleutel](../luis-how-to-azure-subscription.md) door een Luis-runtime bron te maken. Behoud uw sleutel en het eind punt van de sleutel voor de volgende stap.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Een nieuw Java script-bestand (node. js) maken

Maak een nieuw Java script-bestand in uw voorkeurs editor of IDE, met de naam `luis_prediction.js` .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>De NPM-bibliotheek voor de LUIS-runtime installeren

Installeer de afhankelijkheden in de toepassingsmap met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Object model

De ontwerp-client voor Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) -object dat wordt geverifieerd bij Azure, dat uw ontwerp sleutel bevat.

Nadat de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Voor [Spelling](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) per `staging` of `production` sleuf
* [Voor spelling per versie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Language Understanding (LUIS) prediction runtime-client bibliotheek:

* [Voor spelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand in de map van het project `luis_prediction.js` in uw voorkeurs editor of IDE. Voeg de volgende afhankelijkheden toe:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor uw eigen vereiste LUIS-informatie:

    Voeg variabelen toe om de Voorspellings sleutel te beheren die wordt opgehaald uit een omgevings variabele met de naam `LUIS_RUNTIME_KEY` . Als u de omgevings variabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

    Maak een variabele voor de naam van uw resource `LUIS_RUNTIME_ENDPOINT` .

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Maak een variabele voor de App-ID als een omgevings variabele met de naam `LUIS_APP_ID` . Stel de omgevings variabele in op de open bare IoT-app **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Maak een variabele om de gepubliceerde sleuf in te stellen `production` .

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Maak een msRest. ApiKeyCredentials-object met uw sleutel en gebruik dit met uw eind punt om een [Luis te maken. LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) -object.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Voor spelling van runtime ophalen

Voeg de volgende methode toe om de aanvraag te maken voor de Voorspellings runtime.

De gebruiker utterance maakt deel uit van het [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) -object.

De methode **[luisRuntimeClient. prediction. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** heeft verschillende para meters nodig, zoals de App-ID, de naam van de sleuf en het object voor de Voorspellings aanvraag om te voldoen aan de aanvraag. De andere opties, zoals uitgebreid, alle intenten weer geven en logboeken zijn optioneel.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hoofd code voor de voor spelling

Gebruik de volgende methode om de variabelen en methoden samen te koppelen om de voor spelling te verkrijgen.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `node luis_prediction.js` opdracht uit de toepassingsmap.

```console
node luis_prediction.js
```

Het Voorspellings resultaat retourneert een JSON-object:

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

Wanneer u klaar bent met de voor spelling, kunt u het werk van deze Quick Start opschonen door het bestand en de submappen ervan te verwijderen.
