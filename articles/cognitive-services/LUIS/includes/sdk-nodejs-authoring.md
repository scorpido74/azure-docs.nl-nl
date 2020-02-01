---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: e4bba00d6506684a3baf345806bdaa340f6970ca
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909128"
---
Gebruik de Language Understanding (LUIS)-ontwerp-client bibliotheek voor node. js voor het volgende:

* Een app maken.
* Voeg intenties, entiteiten en voor beeld-uitingen toe.
* Voeg onderdelen, zoals een woordgroepen lijst, toe.
* Train en publiceer een app.
* App verwijderen

[Referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [ontwerp pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [runtime pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | voor [beelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Vereisten

* Language Understanding-bewerkings resource: [Maak er een in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>Uw Language Understanding-starter sleutel (LUIS) ophalen

Haal uw [Start sleutel](../luis-how-to-azure-subscription.md#starter-key) op door een Luis-ontwerp bron te maken. Behoud uw sleutel en het eind punt van de sleutel voor de volgende stap.

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

Met uw sleutel, en de regio voor de sleutel, maakt u twee omgevings variabelen voor verificatie:

* `LUIS_AUTHORING_KEY`: de resource sleutel voor het verifiëren van uw aanvragen.
* `LUIS_AUTHORING_ENDPOINT`-het eind punt dat is gekoppeld aan de sleutel.

Volg de instructies voor uw besturings systeem.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>De NPM-bibliotheek voor LUIS-ontwerp installeren

Installeer de afhankelijkheden in de toepassingsmap met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Object model

De ontwerp-client voor Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) -object dat wordt geverifieerd bij Azure, dat uw ontwerp sleutel bevat.

Nadat de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [toevoegen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [verwijderen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publiceren](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Voor beeld van uitingen- [toevoegen per batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [verwijderen op id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Functies: [woordgroepen lijsten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-) beheren
* Model- [intents](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) en entiteiten beheren
* Patroon: [patronen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) beheren
* [Train de app](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) en vraag naar [trainings status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versies](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) : beheren met klonen, exporteren en verwijderen


## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Language Understanding (LUIS)-ontwerp-client bibliotheek voor node. js:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voor beeld uitingen toevoegen](#add-example-utterance-to-intent)
* [De app trainen](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)
* [De app verwijderen](#delete-a-language-understanding-app)
* [Apps weer geven](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuw tekst bestand in uw voorkeurs editor of IDE met de naam `luis_authoring_quickstart.js`. Voeg vervolgens de volgende afhankelijkheden toe.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) -object met uw sleutel en gebruik het met uw eind punt om een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) -object te maken.

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app die de intenties, entiteiten en voorbeeld uitingen van het model voor natuurlijke taal verwerking (NLP) bevat.

1. De [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) -methode van een [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) -object maken om de app te maken. De naam en taal cultuur zijn vereiste eigenschappen.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Intentie maken voor de app
Het primaire object in het model van een LUIS-app is het doel. De intentie Lijnt uit met een groepering van gebruikers utterance _voornemens_. Een gebruiker kan een vraag stellen of een overzicht maken van een bepaalde _gewenste_ reactie van een bot (of een andere client toepassing). Voor beelden van bedoelingen zijn het reserveren van een vlucht, waarin wordt gevraagd om de weer gave van een bestemmings plaats en om contact op te nemen met de klanten service.

Gebruik de methode [model. add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) met de naam van de unieke intentie en geef vervolgens de App-ID, de versie-id en de naam van de nieuwe intentie door.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps gevonden. De entiteit extraheert informatie uit de utterance van de gebruiker, die nodig is om de bedoeling van de gebruiker te fullfilen. Er zijn verschillende typen [vooraf ontwikkelde](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) en aangepaste entiteiten met hun eigen DTO-modellen (Data Transformation object).  Algemene vooraf gemaakte entiteiten die u wilt toevoegen aan uw app, zijn [Number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [Ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze **add_entities** methode is een `Location` eenvoudige entiteit gemaakt met twee rollen, een `Class` eenvoudige entiteit, een `Flight` samengestelde entiteit en verschillende vooraf gemaakte entiteiten toegevoegd.

Het is belang rijk te weten dat entiteiten niet zijn gemarkeerd met een bedoeling. Ze kunnen en meestal worden toegepast op veel intenties. Alleen voor beelden van gebruikers uitingen zijn gemarkeerd voor een specifiek, enkelvoudig doel.

Methode voor het maken van entiteiten maakt deel uit van de [model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) klasse. Elk entiteits type heeft een eigen DTO-model (Data Transformation object).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Bijvoorbeeld utterance toevoegen aan intentie

De app heeft voor beelden van uitingen nodig om de voor delen en het ophalen van entiteiten van een utterance te bepalen. De voor beelden moeten gericht zijn op een specifieke, afzonderlijke intentie en alle aangepaste entiteiten moeten worden gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Een voor beeld van een uitingen toevoegen door een lijst met [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) -objecten te maken, één object voor elk voor beeld utterance. Elk voor beeld moet alle entiteiten markeren met een woorden lijst met naam/waarde-paren van de naam van de entiteit en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weer gegeven in de tekst van het voor beeld utterance.

Call- [voor beelden. batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) met de App-ID, versie-id en de lijst met voor beelden. De aanroep reageert met een lijst met resultaten. U moet het resultaat van elk voor beeld controleren om er zeker van te zijn dat het is toegevoegd aan het model.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md)of naar de staging-of product sleuven worden [gepubliceerd](../luis-how-to-publish-app.md) .

De methode [Train. trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) moet de App-ID en de versie-id hebben.

Een zeer klein model, zoals deze Quick Start laat zien, wordt zeer snel getraind. Voor toepassingen op productie niveau moet de training van de app een polling aanroep naar de [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) methode bevatten om te bepalen wanneer de training is geslaagd. Het antwoord is een lijst met [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) -objecten met een afzonderlijke status voor elk object. De training kan alleen volledig worden beschouwd als alle objecten zijn voltooid.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Het trainen van alle modellen vergt tijd. Gebruik de kan operationresult niet om de trainings status te controleren.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Een Language Understanding-app publiceren

Publiceer de LUIS-app met de methode [app. publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eind punt. Uw client toepassing gebruikt dit eind punt om gebruikers uitingen te verzenden voor het voors pellen van intentie en het uitpakken van entiteiten.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Een Language Understanding-app verwijderen

Verwijder de LUIS-app met behulp van de methode [app. deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) . Hiermee verwijdert u de huidige app.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Apps met een lijst taal

Haal een lijst op met de apps die zijn gekoppeld aan de sleutel over de taal

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node luis_authoring_quickstart.js` in uw Quick Start-bestand.

```console
node luis_authoring_quickstart.js
```

De opdracht regel uitvoer van de toepassing is:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
