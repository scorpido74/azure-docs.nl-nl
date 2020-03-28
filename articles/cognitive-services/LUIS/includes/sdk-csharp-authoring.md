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
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372014"
---
Gebruik de tekstbibliotheek (Language Understanding) voor .NET om:

* Een app maken
* Intents, entiteiten en voorbeelduitingen toevoegen
* Functies toevoegen, zoals een woordenlijst
* App trainen en publiceren

[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Authoring Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# Voorbeelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS) portal account - [Maak er een gratis](https://www.luis.ai)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Instellen

### <a name="get-your-language-understanding-luis-starter-key"></a>Uw startersleutel voor taalbegrip (LUIS)

Haal uw [startsleutel](../luis-how-to-azure-subscription.md#starter-key) op door een LUIS-ontwerpbron te maken. Bewaar uw sleutel en de regio van de sleutel voor de volgende stap.

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

Maak met uw sleutel en de regio voor de sleutel twee omgevingsvariabelen voor verificatie:

* `COGNITIVESERVICE_AUTHORING_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `COGNITIVESERVICE_REGION`- De regio die aan uw sleutel is gekoppeld. Bijvoorbeeld `westus`.

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of `new` Bash) de dotnetopdracht `language-understanding-quickstart`om een nieuwe console-app met de naam te maken. Met deze opdracht wordt een eenvoudig "Hello World" `Program.cs`C#-project gemaakt met één bronbestand: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Wijzig uw map in de nieuw gemaakte app-map.

1. U de toepassing bouwen met:

    ```dotnetcli
    dotnet build
    ```

    De buildoutput mag geen waarschuwingen of fouten bevatten.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de clientbibliotheek Taalbegrip (LUIS) voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.


## <a name="object-model"></a>Objectmodel

De tekstclient voor taalbegrip (LUIS) is een [LUISAuthoringClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) dat is geverifieerd naar Azure, dat uw ontwerpsleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* Apps - [maken,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) [verwijderen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publiceren](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Voorbeelduitingen - [toevoegen per batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), verwijderen per [id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Functies - [woordgroeplijsten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) beheren
* Model - intents en [entiteiten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) beheren
* Patroon - [patronen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) beheren
* Train - [train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) de app en poll voor [trainingsstatus](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Versies](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - beheren met klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de clientbibliotheek voor het maken van taalinformatie (LUIS) voor .NET:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van utterances toevoegen](#add-example-utterance-to-intent)
* [Train de app](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open in de projectmap het *Program.cs* bestand in uw voorkeurseditor of IDE. Vervang de `using` bestaande code `using` door de volgende richtlijnen:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak een variabele om de ontwerpsleutel te `COGNITIVESERVICES_AUTHORING_KEY`beheren die is opgehaald uit een omgevingsvariabele met de naam . Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

1. Maak variabelen om uw ontwerpgebied en eindpunt vast te houden. De regio van uw ontwerpsleutel is afhankelijk van waar u aan het authoreren bent. De [drie authoring regio's](../luis-reference-regions.md) zijn:

    * Australië -`australiaeast`
    * Europa -`westeurope`
    * Vs en andere regio's - `westus` (Standaard)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Maak een [Object ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app met het NLP-model (Natural Language Processing) met intents, entiteiten en voorbeelduitingen.

1. Een [ApplicationCreateObject maken](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). De naam en taalcultuur zijn vereiste eigenschappen.

1. Roep de methode [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) aan. Het antwoord is de app-id.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt uitgelijnd met een groepering van de intenties voor _gebruikersuitingen._ Een gebruiker kan een vraag stellen of een verklaring afleggen op zoek naar een bepaald _bedoeld_ antwoord van een bot (of andere clienttoepassing). Voorbeelden van intenties zijn het boeken van een vlucht, vragen over het weer in een bestemmingsstad en vragen stellen over contactgegevens voor klantenservice.

Maak een [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) met de naam van de unieke intentie en geef vervolgens de app-id, versie-id en het ModelCreateObject door aan de methode [Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) Het antwoord is de intent ID.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps gevonden. De entiteit haalt informatie uit de gebruiker uiting, die nodig is om de intentie van de gebruiker volledig te verwerken. Er zijn verschillende soorten [vooraf gebouwde](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) en aangepaste entiteiten, elk met hun eigen DTO-modellen (Data Transformation Object).  Algemene vooraf gebouwde entiteiten die aan uw app moeten worden toegevoegd, omvatten [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografieV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze methode **Addentities** is een `Location` `Class` eenvoudige entiteit `Flight` gemaakt met twee rollen, een eenvoudige entiteit, een samengestelde entiteit en voegt u verschillende vooraf gebouwde entiteiten toe.

Het is belangrijk om te weten dat entiteiten niet zijn gemarkeerd met een intentie. Ze kunnen en meestal van toepassing op vele bedoelingen. Alleen voorbeelduitingen van gebruikers zijn gemarkeerd voor een specifieke, enkele intentie.

Creatiemethoden voor entiteiten maken deel uit van de klasse [Model.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Elk entiteitstype heeft zijn eigen DTO-model (Data `model` Transformation Object), meestal met het woord in de naamruimte [Modellen.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeldutterance toevoegen aan intentie

Om de intentie van een utterance te bepalen en entiteiten te extraheren, heeft de app voorbeelden van uitingen nodig. De voorbeelden moeten zich richten op een specifieke, enkelvoudige intentie en moeten alle aangepaste entiteiten markeren. Vooraf gebouwde entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject-objecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) te maken, één object voor elke voorbeeldutterance. Elk voorbeeld moet alle entiteiten markeren met een woordenboek met naam/waardeparen van entiteitsnaam en entiteitswaarde. De entiteitswaarde moet precies zijn zoals deze wordt weergegeven in de tekst van de voorbeeldutterance.

Call [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) met de app-id, versie-ID en de lijst met voorbeelden. De oproep reageert met een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om te controleren of het aan het model is toegevoegd.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

De methoden **CreateUtterance** en **CreateLabel** zijn hulpprogramma's waarmee u objecten maken.

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container,](../luis-container-howto.md)of [gepubliceerd](../luis-how-to-publish-app.md) in de staging of product slots.

De [Train.TrainVersionAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) heeft de app-id en de versie-ID nodig.

Een zeer klein model, zoals deze quickstart shows, zal zeer snel trainen. Voor toepassingen op productieniveau moet de training van de app een pollingcall bevatten naar de [GetStatusAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) om te bepalen wanneer en of de training is geslaagd. Het antwoord is een lijst met [ModelTrainingInfo-objecten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) met een aparte status voor elk object. Alle objecten moeten succesvol zijn om de training als voltooid te laten worden beschouwd.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Een app Voor taalbegrip publiceren

Publiceer de LUIS-app met de [methode PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) Hiermee publiceert u de huidige getrainde versie naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intentie en entiteitsextractie.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `dotnet run` uit met de opdracht uit uw toepassingsmap.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt opruimen, u de LUIS-app verwijderen. Het verwijderen van de app gebeurt met de methode [Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) U de app ook verwijderen uit de [LUIS-portal.](https://www.luis.ai)