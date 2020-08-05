---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 19da911cbc662f47e7bbd16aaddf8803d4109d6a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369249"
---
Gebruik de runtime-clientbibliotheek van LUIS (Language Understanding) voor Node.js voor het volgende:

* Voorspelling per sleuf
* Voorspelling per versie

[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Runtimepakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js)

## <a name="prerequisites"></a>Vereisten

* Runtime-resource van Language Understanding: [Maak er een in Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Een LUIS-app-id: gebruik de openbare IoT-app-id van `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. De gebruikersquery die in de code van de quickstart wordt gebruikt, is specifiek voor die app.

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-runtime-key"></a>De runtime-sleutel van Language Understanding (LUIS) ophalen

Haal de [runtime-sleutel](../luis-how-to-azure-subscription.md) op door een LUIS-runtime-resource te maken. Bewaar de sleutel en het eindpunt van de sleutel voor de volgende stap.

### <a name="create-a-new-javascript-nodejs-file"></a>Een nieuw JavaScript (Node.js)-bestand maken

Maak een nieuw JavaScript-bestand in uw favoriete editor of IDE en geef het de naam `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>De NPM-bibliotheek voor de LUIS-runtime installeren

Installeer de afhankelijkheden in de toepassingsmap met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objectmodel

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* [Voorspellings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) per `staging` of `production`-sleuf
* [Voorspelling per versie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de voorspellingsruntime-clientbibliotheek van Language Understanding (LUIS):

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap bestand `luis_prediction.js` in uw favoriete editor of IDE. Voeg de volgende afhankelijkheden toe:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor uw eigen vereiste LUIS-gegevens:

    Voeg variabelen toe om de voorspellingssleutel en de eindpuntsleutel te beheren. 
    
    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Maak een variabele voor de app-id met de naam `LUIS_APP_ID`. Stel de variabele in op de openbare IoT-app, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Maak een variabele om de gepubliceerde sleuf `production` in te stellen.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Maak een msRest.ApiKeyCredentials-object met uw sleutel en gebruik het met uw eindpunt om een [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)-object te maken.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Voorspelling uit runtime ophalen

Voeg de volgende methode toe om de aanvraag aan de voorspellingsruntime te maken.

De uiting van de gebruiker maakt deel uit van het [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)-object.

De **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** -methode heeft verschillende parameters nodig, zoals de app-id, de naam van de sleuf en het voorspellingsaanvraagobject, om aan de aanvraag te voldoen. De andere opties, zoals verbose, show all intents en log, zijn optioneel.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden samen te koppelen om de voorspelling op te halen.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `node luis_prediction.js`.

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

Wanneer u klaar bent met de voorspellingen, kunt u het werk van deze quickstart opschonen door het bestand en de submappen ervan te verwijderen.
