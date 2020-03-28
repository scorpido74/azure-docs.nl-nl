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
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371879"
---
Gebruik de luis (Language Understanding) die de clientbibliotheek voor Python maakt om:

* Maak een app.
* Intents, entiteiten en voorbeelduitingen toevoegen.
* Voeg functies toe, zoals een woordenlijst.
* Train en publiceer een app.

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Authoring Package (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS) portal account: [Maak er een gratis](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>Uw startersleutel voor taalbegrip (LUIS)

Haal uw [startsleutel](../luis-how-to-azure-subscription.md#starter-key) op door een LUIS-ontwerpbron te maken. Bewaar uw sleutel en de regio van de sleutel voor de volgende stap.

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

Maak met uw sleutel en de regio voor de sleutel twee omgevingsvariabelen voor verificatie:

* `LUIS_AUTHORING_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `LUIS_REGION`- De regio die aan uw sleutel is gekoppeld. Bijvoorbeeld `westus`.

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="install-the-python-library-for-luis"></a>De Python-bibliotheek voor LUIS installeren

Installeer in de toepassingsmap de clientbibliotheek Language Understanding (LUIS) voor python met de volgende opdracht:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objectmodel

De tekstclient voor taalbegrip (LUIS) is een [LUISAuthoringClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) dat is geverifieerd naar Azure, dat uw ontwerpsleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* Apps - [maken,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) [verwijderen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publiceren](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Voorbeelduitingen - [toevoegen per batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), verwijderen per [id](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Functies - [woordgroeplijsten](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) beheren
* Model - intents en [entiteiten](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) beheren
* Patroon - [patronen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) beheren
* Train - [train](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) de app en poll voor [trainingsstatus](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versies](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - beheren met klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek voor het schrijven van taalafspraken (LUIS) voor python:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van utterances toevoegen](#add-example-utterance-to-intent)
* [Train de app](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuwe Python-toepassing in uw voorkeurseditor of IDE. Importeer vervolgens de volgende bibliotheken.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [object CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) te maken.

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app met het NLP-model (Natural Language Processing) met intents, entiteiten en voorbeelduitingen.

1. Maak de [add-methode van](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) een [AppsOperation-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt uitgelijnd met een groepering van de intenties voor _gebruikersuitingen._ Een gebruiker kan een vraag stellen of een verklaring afleggen op zoek naar een bepaald _bedoeld_ antwoord van een bot (of andere clienttoepassing). Voorbeelden van intenties zijn het boeken van een vlucht, vragen over het weer in een bestemmingsstad en vragen stellen over contactgegevens voor klantenservice.

Gebruik de [methode model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) met de naam van de unieke intentie en geef vervolgens de app-id, versie-id en nieuwe intentienaam door.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps gevonden. De entiteit haalt informatie uit de gebruiker uiting, die nodig is om de intentie van de gebruiker volledig te verwerken. Er zijn verschillende soorten [vooraf gebouwde](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) en aangepaste entiteiten, elk met hun eigen DTO-modellen (Data Transformation Object).  Algemene vooraf gebouwde entiteiten die aan uw app moeten worden toegevoegd, omvatten [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografieV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Met **deze** add_entities `Location` methode een eenvoudige `Class` entiteit gemaakt `Flight` met twee rollen, een eenvoudige entiteit, een samengestelde entiteit en voegt u verschillende vooraf gebouwde entiteiten toe.

Het is belangrijk om te weten dat entiteiten niet zijn gemarkeerd met een intentie. Ze kunnen en meestal van toepassing op vele bedoelingen. Alleen voorbeelduitingen van gebruikers zijn gemarkeerd voor een specifieke, enkele intentie.

Maakmethoden voor entiteiten maken deel uit van de klasse [ModelOperations.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) Elk entiteitstype heeft zijn eigen DTO-model (Data Transformation Object).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeldutterance toevoegen aan intentie

Om de intentie van een utterance te bepalen en entiteiten te extraheren, heeft de app voorbeelden van uitingen nodig. De voorbeelden moeten zich richten op een specifieke, enkelvoudige intentie en moeten alle aangepaste entiteiten markeren. Vooraf gebouwde entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject-objecten](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) te maken, één object voor elke voorbeeldutterance. Elk voorbeeld moet alle entiteiten markeren met een woordenboek met naam/waardeparen van entiteitsnaam en entiteitswaarde. De entiteitswaarde moet precies zijn zoals deze wordt weergegeven in de tekst van de voorbeeldutterance.

Oproep [voorbeelden.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) met de app-id, versie-ID en de lijst met voorbeelden. De oproep reageert met een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om te controleren of het aan het model is toegevoegd.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container,](../luis-container-howto.md)of [gepubliceerd](../luis-how-to-publish-app.md) in de staging of product slots.

De [methode train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) heeft de app-id en de versie-ID nodig.

Een zeer klein model, zoals deze quickstart shows, zal zeer snel trainen. Voor toepassingen op productieniveau moet de app een pollingcall bevatten naar de [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) methode om te bepalen wanneer en of de training is geslaagd. Het antwoord is een lijst met [ModelTrainingInfo-objecten](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) met een aparte status voor elk object. Alle objecten moeten succesvol zijn om de training als voltooid te laten worden beschouwd.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Een app Voor taalbegrip publiceren

Publiceer de LUIS-app met de [methode app.publish.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) Hiermee publiceert u de huidige getrainde versie naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intentie en entiteitsextractie.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `python` uit met de opdracht voor uw quickstartbestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw voorspellingen, ruim t-up van het werk van deze quickstart door het verwijderen van het bestand en de submappen.