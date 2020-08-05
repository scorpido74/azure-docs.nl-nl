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
ms.openlocfilehash: db866da43310f5407ce4daae1cade2c7512b91ea
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369247"
---
Gebruik de clientbibliotheek met voorspellingen van LUIS (Language Understanding) voor Python voor het volgende:

* Voorspelling per sleuf ophalen
* Voorspelling per versie ophalen

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Voorspellingsruntimepakket (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS)-portalaccount - [Maak er een gratis](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Een LUIS-app-id: gebruik de openbare IoT-app-id van `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. De gebruikersquery die in de code van de quickstart wordt gebruikt, is specifiek voor die app.

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-runtime-key"></a>De runtime-sleutel van Language Understanding (LUIS) ophalen

Haal de [runtime-sleutel](../luis-how-to-azure-subscription.md) op door een LUIS-runtime-resource te maken. Bewaar de sleutel en het eindpunt van de sleutel voor de volgende stap.

### <a name="create-a-new-python-file"></a>Een nieuw Python-bestand maken

Maak een nieuw Python-bestand in uw favoriete editor of IDE en geef het de naam `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de voorspellingsruntime-clientbibliotheek van Language Understanding (LUIS) voor Python met de volgende opdracht:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objectmodel

De voorspellingsruntime-client van Language Understanding (LUIS) is een [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python)-object dat wordt geverifieerd bij Azure, dat de bronsleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Voorspelling per [fasering of productiesleuf](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Voorspelling per [versie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de voorspellingsruntime-clientbibliotheek van Language Understanding (LUIS) voor Python:

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap bestand `prediction_quickstart.py` in uw favoriete editor of IDE. Voeg de volgende afhankelijkheden toe:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor uw eigen vereiste LUIS-informatie: de voorspellingssleutel en het voorspellingseindpunt.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. Maak een variabele voor de app-id die is ingesteld op de openbare IoT-app, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Maak een variabele om de gepubliceerde sleuf `production` in te stellen.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. Maak een referenties-object met uw sleutel en gebruik dit met uw eindpunt om een [LUISRuntimeClientConfiguration](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python)-object te maken.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Voorspelling uit runtime ophalen

Voeg de volgende methode toe om de aanvraag aan de voorspellingsruntime te maken.

De uiting van de gebruiker maakt deel uit van het [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)-object.

De **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** -methode heeft verschillende parameters nodig, zoals de app-id, de naam van de sleuf en het voorspellingsaanvraagobject, om aan de aanvraag te voldoen. De andere opties, zoals verbose, show all intents en log, zijn optioneel. De aanvraag retourneert een [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)-object.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden samen te koppelen om de voorspelling op te halen.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `python prediction_quickstart.py`.

```console
python prediction_quickstart.py
```

In de quickstart-console wordt de uitvoer weergegeven:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de voorspellingen, kunt u het werk van deze quickstart opschonen door het bestand en de submappen ervan te verwijderen.
