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
ms.openlocfilehash: 671d6a18ca4c6b9ae15453d23d8188fbb4f7d957
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369416"
---
Gebruik de creatieclientbibliotheek van LUIS (Language Understanding) voor .NET voor het volgende:

* Een app maken
* Intenties, entiteiten en voorbeelden van uitingen toevoegen
* Kenmerken toevoegen, zoals een lijst met woordgroepen
* App trainen en publiceren

[Referentiemateriaal](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [Creatiepakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C#-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, [maakt u een Language Understanding-creatieresource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u [maakt](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) om de toepassing te verbinden met Language Understanding-creatie. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code. U kunt de gratis prijscategorie (`F0`) gebruiken om de service te proberen.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht dotnet `new` om een nieuwe console-app te maken met de naam `language-understanding-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Wijzig uw map in de zojuist gemaakte app-map.

1. U kunt de toepassing maken met:

    ```dotnetcli
    dotnet build
    ```

    De build-uitvoer mag geen waarschuwingen of fouten bevatten.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de creatie-clientbibliotheek van Language Understanding (LUIS) voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.


## <a name="object-model"></a>Objectmodel

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [maken](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [verwijderen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publiceren](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Voorbeelden van uitingen: [toevoegen per batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), [verwijderen per id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Kenmerken: [lijsten met woordgroepen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) beheren
* Model: [intenties](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) en entiteiten beheren
* Patroon: [patronen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet) beheren
* Trainen: de app [trainen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) en de [trainingsstatus](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet) uitvragen
* [Versies](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet): beheren door middel van klonen, exporteren en verwijderen


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de creatie-clientbibliotheek van Language Understanding (LUIS) voor .NET:

* [Een app maken](#create-a-luis-app)
* [Entiteiten toevoegen](#create-entities-for-the-app)
* [Intents toevoegen](#create-intent-for-the-app)
* [Voorbeelden van uitingen toevoegen](#add-example-utterance-to-intent)
* [De app trainen](#train-the-app)
* [De app publiceren](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Vervang de bestaande `using`-code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak een variabele waarin de bewerkingssleutel en het creatie-eindpunt wordt bewaard.

    [!code-csharp[Authorization to resource key](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=Variables)]

1. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)-object te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Maak een LUIS-app die de intenties, entiteiten en voorbeelduitingen van het model voor natuurlijke taalverwerking bevat.

1. Maak een [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). De naam en taalcultuur zijn vereiste eigenschappen.

1. Roep de methode [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) aan. De respons is de app-id.

    [!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt in overeenstemming gebracht met een groepering _intenties_ van uitingen van gebruikers. Een gebruiker kan een vraag stellen of een instructie maken op zoek naar een bepaalde _beoogde_ respons van een bot (of een andere clienttoepassing). Voorbeelden van intenties zijn het reserveren van een vlucht, vragen naar het weer op de plaats van bestemming en vragen naar de contactgegevens van de klantenservice.

Maak een [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) met de naam van de unieke intentie en geef de app-id, versie-id en ModelCreateObject door aan de methode [model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet). De respons is de intentie-id.

[!code-csharp[Create intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps aangetroffen. De entiteit extraheert informatie uit de uiting van de gebruiker, wat nodig is om aan de bedoeling van de gebruiker tegemoet te komen. Er zijn verschillende soorten [vooraf ontwikkelde](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) en aangepaste entiteiten, elk met hun eigen gegevenstransformatieobject.  Veelvoorkomende vooraf ontwikkelde entiteiten die u aan uw app kunt toevoegen, zijn [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) en [ordinal](../luis-reference-prebuilt-ordinal.md).

Met deze **AddEntities**-methode is een eenvoudige entiteit, `Location`, gemaakt met twee rollen, de eenvoudige entiteit `Class`, de samengestelde entiteit `Flight` en er zijn verschillende vooraf ontwikkelde entiteiten toegevoegd.

Het is belangrijk te weten dat entiteiten niet met een intentie zijn gemarkeerd. Ze kunnen op veel intenties worden toegepast en dit gebeurt ook vaak. Alleen voorbeelden van uitingen van gebruikers zijn gemarkeerd voor een specifieke, enkelvoudige intentie.

Methoden voor het maken van entiteiten maken deel uit van de klasse [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet). Elk entiteitstype heeft een eigen gegevenstransformatieobjectmodel, dat gewoonlijk het woord `model` bevat in de naamruimte [Modellen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet).

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeld van uiting toevoegen aan intentie

De app heeft voorbeelden van uitingen nodig om de intentie van een uiting te kunnen vaststellen en entiteiten te kunnen ophalen. De voorbeelden moeten gericht zijn op een specifieke, afzonderlijke intentie. Alle aangepaste entiteiten moeten zijn gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet)-objecten te maken, één object voor elke voorbeelduiting. Elk voorbeeld moet alle entiteiten markeren met een woordenlijst met naam-/waardeparen met de naam en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weergegeven in de tekst van de voorbeelduiting.

Roep [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) aan met de app-id, versie-id en de lijst met voorbeelden. De aanroep retourneert een lijst met resultaten. U moet het resultaat van elk voorbeeld controleren om er zeker van te zijn dat het is toegevoegd aan het model.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringBatchAddUtterancesForIntent)]

De methoden **CreateUtterance** en **CreateLabel** zijn een hulpmiddel bij het maken van objecten.

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md) of [worden gepubliceerd](../luis-how-to-publish-app.md) naar de fasering of productsleuven.

Voor de methode [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) is de app-id en de versie-id vereist.

Een heel klein model, kan heel snel worden getraind, zoals deze quickstart laat zien. Voor toepassingen op productieniveau moet het trainen van de app een pollingaanroep naar de methode [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) omvatten om te bepalen of de training is geslaagd. De respons is een lijst met [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet)-objecten met een afzonderlijke status voor elk object. De training is pas voltooid als alle objecten zijn geslaagd.

[!code-csharp[Train the app's version](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Een Language Understanding-app publiceren

Publiceer de LUIS-app met behulp van de methode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet). Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intenties en het extraheren van entiteiten.

[!code-csharp[Create entities](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/authoring/authoring-with-sdk.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt opschonen, kunt u de LUIS-app verwijderen. Voor het verwijderen van de app wordt de methode [Apps. DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) gebruikt. U kunt de app ook verwijderen vanuit de [LUIS-portal](https://www.luis.ai).