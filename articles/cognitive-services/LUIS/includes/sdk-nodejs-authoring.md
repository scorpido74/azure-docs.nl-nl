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
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372201"
---
Gebruik de tekstbibliotheek (Language Understanding) voor Node.js om:

* Maak een app.
* Intents, entiteiten en voorbeelduitingen toevoegen.
* Voeg functies toe, zoals een woordenlijst.
* Train en publiceer een app.
* App verwijderen

[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Authoring Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [Runtime Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Vereisten

* Tekst voor het maken van taalinformatie: [er een maken in de Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>Uw startersleutel voor taalbegrip (LUIS)

Haal uw [startsleutel](../luis-how-to-azure-subscription.md#starter-key) op door een LUIS-ontwerpbron te maken. Bewaar uw sleutel en het eindpunt van de sleutel voor de volgende stap.

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

Maak met uw sleutel en de regio voor de sleutel twee omgevingsvariabelen voor verificatie:

* `LUIS_AUTHORING_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `LUIS_AUTHORING_ENDPOINT`- Het eindpunt dat aan uw sleutel is gekoppeld.

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>De NPM-bibliotheek voor LUIS-auteurmaken installeren

Installeer in de toepassingsmap de afhankelijkheden met de volgende opdracht:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objectmodel

De tekstclient voor taalbegrip (LUIS) is een [LUISAuthoringClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) dat is geverifieerd naar Azure, dat uw ontwerpsleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* Apps - [toevoegen,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-) [verwijderen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publiceren](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Voorbeelduitingen - [toevoegen per batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), verwijderen per [id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Functies - [woordgroeplijsten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-) beheren
* Model - intents en [entiteiten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) beheren
* Patroon - [patronen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) beheren
* Train - [train](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) de app en poll voor [trainingsstatus](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versies](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - beheren met klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek voor het maken van taalinformatie (LUIS) voor Node.js:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van utterances toevoegen](#add-example-utterance-to-intent)
* [Train de app](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)
* [De app verwijderen](#delete-a-language-understanding-app)
* [Apps aanbieden](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak een nieuw tekstbestand in de `luis_authoring_quickstart.js`gewenste editor of IDE met de naam . Voeg vervolgens de volgende afhankelijkheden toe.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [object CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) te maken.

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app met het NLP-model (Natural Language Processing) met intents, entiteiten en voorbeelduitingen.

1. Maak de [add-methode van](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) een [AppsOperation-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt uitgelijnd met een groepering van de intenties voor _gebruikersuitingen._ Een gebruiker kan een vraag stellen of een verklaring afleggen op zoek naar een bepaald _bedoeld_ antwoord van een bot (of andere clienttoepassing). Voorbeelden van intenties zijn het boeken van een vlucht, vragen over het weer in een bestemmingsstad en vragen stellen over contactgegevens voor klantenservice.

Gebruik de [methode model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) met de naam van de unieke intentie en geef vervolgens de app-id, versie-id en nieuwe intentienaam door.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps gevonden. De entiteit haalt informatie uit de gebruiker uiting, die nodig is om de intentie van de gebruiker volledig te verwerken. Er zijn verschillende soorten [vooraf gebouwde](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) en aangepaste entiteiten, elk met hun eigen DTO-modellen (Data Transformation Object).  Algemene vooraf gebouwde entiteiten die aan uw app moeten worden toegevoegd, omvatten [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografieV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Met **deze** add_entities `Location` methode een eenvoudige `Class` entiteit gemaakt `Flight` met twee rollen, een eenvoudige entiteit, een samengestelde entiteit en voegt u verschillende vooraf gebouwde entiteiten toe.

Het is belangrijk om te weten dat entiteiten niet zijn gemarkeerd met een intentie. Ze kunnen en meestal van toepassing op vele bedoelingen. Alleen voorbeelduitingen van gebruikers zijn gemarkeerd voor een specifieke, enkele intentie.

Creatiemethoden voor entiteiten maken deel uit van de klasse [Model.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) Elk entiteitstype heeft zijn eigen DTO-model (Data Transformation Object).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeldutterance toevoegen aan intentie

Om de intentie van een utterance te bepalen en entiteiten te extraheren, heeft de app voorbeelden van uitingen nodig. De voorbeelden moeten zich richten op een specifieke, enkelvoudige intentie en moeten alle aangepaste entiteiten markeren. Vooraf gebouwde entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject-objecten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) te maken, één object voor elke voorbeeldutterance. Elk voorbeeld moet alle entiteiten markeren met een woordenboek met naam/waardeparen van entiteitsnaam en entiteitswaarde. De entiteitswaarde moet precies zijn zoals deze wordt weergegeven in de tekst van de voorbeeldutterance.

Oproep [voorbeelden.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) met de app-id, versie-ID en de lijst met voorbeelden. De oproep reageert met een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om te controleren of het aan het model is toegevoegd.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container,](../luis-container-howto.md)of [gepubliceerd](../luis-how-to-publish-app.md) in de staging of product slots.

De [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) methode heeft de app ID en de versie-ID nodig.

Een zeer klein model, zoals deze quickstart shows, zal zeer snel trainen. Voor toepassingen op productieniveau moet de app een pollingcall bevatten naar de [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) methode om te bepalen wanneer en of de training is geslaagd. Het antwoord is een lijst met [ModelTrainingInfo-objecten](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) met een aparte status voor elk object. Alle objecten moeten succesvol zijn om de training als voltooid te laten worden beschouwd.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Het trainen van alle modellen kost tijd. Gebruik de operationResult om de trainingsstatus te controleren.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Een app Voor taalbegrip publiceren

Publiceer de LUIS-app met de [methode app.publish.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) Hiermee publiceert u de huidige getrainde versie naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intentie en entiteitsextractie.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Een app voor taalbegrip verwijderen

Verwijder de LUIS-app met de methode [app.deleteMethod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) Hiermee wordt de huidige app verwijderd.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Apps voor taalverstaan weergeven

Een lijst met apps downloaden die zijn gekoppeld aan de toets Taalbegrip

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `node luis_authoring_quickstart.js` uit met de opdracht voor uw quickstartbestand.

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
