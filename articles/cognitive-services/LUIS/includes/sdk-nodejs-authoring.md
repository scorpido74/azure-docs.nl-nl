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
ms.openlocfilehash: 6d805dfc15264a34abe1f177f688dae96d4a49f7
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369419"
---
Gebruik de creatieclientbibliotheek van LUIS (Language Understanding) voor Node.js voor het volgende:

* Een app maken.
* Intenties, entiteiten en voorbeelden van uitingen toevoegen.
* Kenmerken toevoegen, zoals een lijst met woordgroepen.
* Een app trainen en publiceren.
* App verwijderen

[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Creatiepakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [Runtimepakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Vereisten

* Language Understanding-creatieresource: [Maak er een in Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>De startsleutel van Language Understanding (LUIS) ophalen

Haal de [creatiesleutel](../luis-how-to-azure-subscription.md) op door een LUIS-creatieresource te maken. Bewaar de sleutel en het eindpunt van de sleutel. U hebt ze nodig om deze tekenreeksen toe te voegen aan de bovenkant van het codebestand.

### <a name="install-the-npm-library-for-luis-authoring"></a>De NPM-bibliotheek voor LUIS-creatie installeren

Installeer de afhankelijkheden in de toepassingsmap met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objectmodel

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [toevoegen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [verwijderen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publiceren](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Voorbeelden van uitingen: [toevoegen per batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [verwijderen per id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Kenmerken: [lijsten met woordgroepen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-) beheren
* Model: [intenties](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) en entiteiten beheren
* Patroon: [patronen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) beheren
* Trainen: de app [trainen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) en de [trainingsstatus](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) uitvragen
* [Versies](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest): beheren door middel van klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de creatie-clientbibliotheek van Language Understanding (LUIS) voor Node.js:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van uitingen toevoegen](#add-example-utterance-to-intent)
* [De app trainen](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)
* [De app verwijderen](#delete-a-language-understanding-app)
* [Apps vermelden](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuw tekstbestand in uw favoriete editor of IDE en geef het de naam `luis_authoring_quickstart.js`. Voeg de volgende afhankelijkheden toe.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Dependencies)]

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)-object te maken.

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app die de intenties, entiteiten en voorbeelduitingen van het model voor natuurlijke taalverwerking bevat.

1. Maak een [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest)-methode van het [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest)-object om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt in overeenstemming gebracht met een groepering _intenties_ van uitingen van gebruikers. Een gebruiker kan een vraag stellen of een instructie maken op zoek naar een bepaalde _beoogde_ respons van een bot (of een andere clienttoepassing). Voorbeelden van intenties zijn het reserveren van een vlucht, vragen naar het weer op de plaats van bestemming en vragen naar de contactgegevens van de klantenservice.

Gebruik de methode [model. add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) met de naam van de unieke intentie, en voer de app-id, de versie-id en de nieuwe naam van de intentie door.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps aangetroffen. De entiteit extraheert informatie uit de uiting van de gebruiker, wat nodig is om aan de bedoeling van de gebruiker tegemoet te komen. Er zijn verschillende soorten [vooraf ontwikkelde](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) en aangepaste entiteiten, elk met hun eigen gegevenstransformatieobject.  Veelvoorkomende vooraf ontwikkelde entiteiten die u aan uw app kunt toevoegen, zijn [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) en [ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze **AddEntities**-methode is een eenvoudige entiteit, `Location`, gemaakt met twee rollen, de eenvoudige entiteit `Class`, de samengestelde entiteit `Flight` en er zijn verschillende vooraf ontwikkelde entiteiten toegevoegd.

Het is belangrijk te weten dat entiteiten niet met een intentie zijn gemarkeerd. Ze kunnen op veel intenties worden toegepast en dit gebeurt ook vaak. Alleen voorbeelden van uitingen van gebruikers zijn gemarkeerd voor een specifieke, enkelvoudige intentie.

Methoden voor het maken van entiteiten maken deel uit van de klasse [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Elk entiteitstype heeft een eigen gegevenstransformatieobjectmodel.

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeld van uiting toevoegen aan intentie

De app heeft voorbeelden van uitingen nodig om de intentie van een uiting te kunnen vaststellen en entiteiten te kunnen ophalen. De voorbeelden moeten gericht zijn op een specifieke, afzonderlijke intentie. Alle aangepaste entiteiten moeten zijn gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest)-objecten te maken, één object voor elke voorbeelduiting. Elk voorbeeld moet alle entiteiten markeren met een woordenlijst met naam-/waardeparen met de naam en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weergegeven in de tekst van de voorbeelduiting.

Roep [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) aan met de app-id, versie-id en de lijst met voorbeelden. De aanroep retourneert een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om er zeker van te zijn dat het is toegevoegd aan het model.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md) of [worden gepubliceerd](../luis-how-to-publish-app.md) naar de fasering of productsleuven.

Voor de methode [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) is de app-id en de versie-id vereist.

Een heel klein model, kan heel snel worden getraind, zoals deze quickstart laat zien. Voor toepassingen op productieniveau moet het trainen van de app een pollingaanroep naar de methode [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) omvatten om te bepalen of de training is geslaagd. De respons is een lijst met [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest)-objecten met een afzonderlijke status voor elk object. De training is pas voltooid als alle objecten zijn geslaagd.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Het trainen van alle modellen vergt tijd. Gebruik operationResult om de trainingsstatus te controleren.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Een Language Understanding-app publiceren

Publiceer de LUIS-app met behulp van de methode [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intenties en het extraheren van entiteiten.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Een Language Understanding-app verwijderen

Verwijder de LUIS-app met behulp van de methode [app. deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-). Hiermee verwijdert u de huidige app.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Language Understanding-apps vermelden

Een lijst ophalen met apps die zijn gekoppeld aan de Language Understanding-sleutel

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node luis_authoring_quickstart.js` in uw quickstart-bestand.

```console
node luis_authoring_quickstart.js
```

De opdrachtregeluitvoer van de toepassing is:

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
