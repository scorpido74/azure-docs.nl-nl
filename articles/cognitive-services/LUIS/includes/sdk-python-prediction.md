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
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371987"
---
Gebruik de luis-voorspellingsclientbibliotheek (Language Understanding) voor Python om:

* Krijg voorspelling per sleuf
* Voorspelling per versie krijgen

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Voorspelling runtime Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [ Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS) portal account - [Maak er een gratis](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-runtime-key"></a>Luis-runtime-toets (Language Understanding)

Haal uw [runtime-sleutel](../luis-how-to-azure-subscription.md) op door een LUIS runtime-bron te maken. Bewaar uw sleutel en het eindpunt van de sleutel voor de volgende stap.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Een nieuw python-bestand maken

Maak een nieuw python-bestand in de `prediction_quickstart.py`voorkeurseditor of IDE met de naam .

### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de luis-voorspellingsruntimeclientbibliotheek voor Python met de volgende opdracht:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objectmodel

De Luis-voorspellingsruntimeclient (Language Understanding) is een [LUISRuntimeClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) dat zich verifieert naar Azure, dat uw resourcesleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* Voorspelling door [fasering of productiesleuf](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Voorspelling per [versie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de luis-voorspellingsclientbibliotheek voor de voorspelling van de taalovereenkomst voor Python:

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het bestand in `prediction_quickstart.py` de projectmap in de voorkeurseditor of IDE. Voeg de volgende afhankelijkheden toe:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor uw eigen benodigde LUIS-informatie:

    Voeg variabelen toe om uw voorspellingssleutel `LUIS_RUNTIME_KEY`te beheren die is opgehaald uit een omgevingsvariabele met de naam . Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

    Maak een variabele om `LUIS_RUNTIME_ENDPOINT`de naam van uw resource vast te houden.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Maak een variabele voor de app-id als een omgevingsvariabele met de naam `LUIS_APP_ID`. Stel de omgevingsvariabele in op **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** de openbare IoT-app. Maak een variabele `production` om de gepubliceerde sleuf in te stellen.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Maak een object met referenties met uw sleutel en gebruik het met uwhttps://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() eindpunt om een object [LUISRuntimeClientConfiguration] te maken.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Krijg voorspelling van runtime

Voeg de volgende methode toe om de aanvraag te maken aan de voorspellingsruntime.

De uiting van de gebruiker maakt deel uit van het [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) object.

De **[methode get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** heeft verschillende parameters nodig, zoals de app-id, de sleufnaam en het object voor het voorspellingsverzoek om aan de aanvraag te voldoen. De andere opties, zoals verbose, toon alle intenties en logboek zijn optioneel. De aanvraag retourneert een [object PredictionResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden aan elkaar te koppelen om de voorspelling te krijgen.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python prediction_quickstart.py` uit met de opdracht uit uw toepassingsmap.

```console
python prediction_quickstart.py
```

De quickstartconsole geeft de uitvoer weer:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw voorspellingen, ruim t-up van het werk van deze quickstart door het verwijderen van het bestand en de submappen.
