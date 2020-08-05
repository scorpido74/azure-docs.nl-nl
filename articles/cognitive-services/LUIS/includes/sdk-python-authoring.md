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
ms.openlocfilehash: aa55a3550fbb4fcdcd97c26fca425b11ba589fcc
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369418"
---
Gebruik de creatieclientbibliotheek van LUIS (Language Understanding) voor Python voor het volgende:

* Een app maken.
* Intenties, entiteiten en voorbeelden van uitingen toevoegen.
* Kenmerken toevoegen, zoals een lijst met woordgroepen.
* Een app trainen en publiceren.

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Creatiepakket (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* De huidige versie van [Python 3.x](https://www.python.org/).
* Zodra u een Azure-abonnement hebt, [maakt u een Language Understanding-creatieresource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u [maakt](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) om de toepassing te verbinden met Language Understanding-creatie. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code. U kunt de gratis prijscategorie (`F0`) gebruiken om de service te proberen.

## <a name="setting-up"></a>Instellen

### <a name="install-the-python-library-for-luis"></a>De Python-bibliotheek voor LUIS installeren

Installeer in de toepassingsmap de creatie-clientbibliotheek van Language Understanding (LUIS) voor Python met de volgende opdracht:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objectmodel

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [maken](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [verwijderen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publiceren](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Voorbeelden van uitingen: [toevoegen per batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [verwijderen per id](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Kenmerken: [lijsten met woordgroepen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) beheren
* Model: [intenties](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) en entiteiten beheren
* Patroon: [patronen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) beheren
* Trainen: de app [trainen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) en de [trainingsstatus](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) uitvragen
* [Versies](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python): beheren door middel van klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de creatie-clientbibliotheek van Language Understanding (LUIS) voor Python:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van uitingen toevoegen](#add-example-utterance-to-intent)
* [De app trainen](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw favoriete editor of IDE. Importeer vervolgens de volgende bibliotheken.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Dependencies)]

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)-object te maken.

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app die de intenties, entiteiten en voorbeelduitingen van het model voor natuurlijke taalverwerking bevat.

1. Maak een [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)-methode van het [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python)-object om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt in overeenstemming gebracht met een groepering _intenties_ van uitingen van gebruikers. Een gebruiker kan een vraag stellen of een instructie maken op zoek naar een bepaalde _beoogde_ respons van een bot (of een andere clienttoepassing). Voorbeelden van intenties zijn het reserveren van een vlucht, vragen naar het weer op de plaats van bestemming en vragen naar de contactgegevens van de klantenservice.

Gebruik de methode [model. add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) met de naam van de unieke intentie, en voer de app-id, de versie-id en de nieuwe naam van de intentie door.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps aangetroffen. De entiteit extraheert informatie uit de uiting van de gebruiker, wat nodig is om aan de bedoeling van de gebruiker tegemoet te komen. Er zijn verschillende soorten [vooraf ontwikkelde](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) en aangepaste entiteiten, elk met hun eigen gegevenstransformatieobject.  Veelvoorkomende vooraf ontwikkelde entiteiten die u aan uw app kunt toevoegen, zijn [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) en [ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze **AddEntities**-methode is een eenvoudige entiteit, `Location`, gemaakt met twee rollen, de eenvoudige entiteit `Class`, de samengestelde entiteit `Flight` en er zijn verschillende vooraf ontwikkelde entiteiten toegevoegd.

Het is belangrijk te weten dat entiteiten niet met een intentie zijn gemarkeerd. Ze kunnen op veel intenties worden toegepast en dit gebeurt ook vaak. Alleen voorbeelden van uitingen van gebruikers zijn gemarkeerd voor een specifieke, enkelvoudige intentie.

Methoden voor het maken van entiteiten maken deel uit van de klasse [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Elk entiteitstype heeft een eigen gegevenstransformatieobjectmodel.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeld van uiting toevoegen aan intentie

De app heeft voorbeelden van uitingen nodig om de intentie van een uiting te kunnen vaststellen en entiteiten te kunnen ophalen. De voorbeelden moeten gericht zijn op een specifieke, afzonderlijke intentie. Alle aangepaste entiteiten moeten zijn gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python)-objecten te maken, één object voor elke voorbeelduiting. Elk voorbeeld moet alle entiteiten markeren met een woordenlijst met naam-/waardeparen met de naam en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weergegeven in de tekst van de voorbeelduiting.

Roep [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) aan met de app-id, versie-id en de lijst met voorbeelden. De aanroep retourneert een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om er zeker van te zijn dat het is toegevoegd aan het model.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md) of [worden gepubliceerd](../luis-how-to-publish-app.md) naar de fasering of productsleuven.

Voor de methode [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) is de app-id en de versie-id vereist.

Een heel klein model, kan heel snel worden getraind, zoals deze quickstart laat zien. Voor toepassingen op productieniveau moet het trainen van de app een pollingaanroep naar de methode [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) omvatten om te bepalen of de training is geslaagd. De respons is een lijst met [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python)-objecten met een afzonderlijke status voor elk object. De training is pas voltooid als alle objecten zijn geslaagd.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Een Language Understanding-app publiceren

Publiceer de LUIS-app met behulp van de methode [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intenties en het extraheren van entiteiten.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw quickstart-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de voorspellingen, kunt u het werk van deze quickstart opschonen door het bestand en de submappen ervan te verwijderen.