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
ms.openlocfilehash: 3bb0b9fb8b268c3e036ffb365eb8d5b80a241269
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77371904"
---
Gebruik de voor Spellings-client bibliotheek van Language Understanding (LUIS) voor .NET voor het volgende:

* Voor spelling per sleuf ophalen

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) van de bibliotheek | [Voorspellings runtime pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [ C# voor beelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Vereisten

* Language Understanding-Portal account (LUIS): [Maak er gratis een](https://www.luis.ai) .
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

Zoekt u meer documentatie?

 * [SDK-naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Instellen

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

Maak met behulp van de sleutel en de naam van uw resource twee omgevings variabelen voor verificatie:

* `LUIS_PREDICTION_KEY`: de resource sleutel voor het verifiëren van uw aanvragen.
* `LUIS_ENDPOINT_NAME`: de resource naam die aan uw sleutel is gekoppeld.

Volg de instructies voor uw besturings systeem.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

---

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE.

1. Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app te maken met de naam `language-understanding-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Wijzig uw directory in de zojuist gemaakte app-map.

1. U kunt de toepassing samen stellen met:

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

Installeer in de toepassingsmap de Language Understanding (LUIS) prediction runtime-client bibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="object-model"></a>Object model

De Language Understanding (LUIS) prediction runtime-client is een [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure, dat de bron sleutel bevat.

Nadat de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Voor spelling van [staging of product sleuf](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Voor spelling per [versie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Language Understanding (LUIS) prediction runtime-client bibliotheek voor .NET:

* [Voor spelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Vervang de bestaande `using` code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Variabelen maken voor de sleutel, naam en App-ID:

    Een variabele voor het beheren van de Voorspellings sleutel die wordt opgehaald uit een omgevings variabele met de naam `LUIS_PREDICTION_KEY`. Als u de omgevings variabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

    Maak een variabele om de resource naam `LUIS_ENDPOINT_NAME`op te slaan.

    Maak een variabele voor de App-ID als een omgevings variabele met de naam `LUIS_APP_ID`. Stel de omgevings variabele in op de open bare IoT-app:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) -object met uw sleutel en gebruik het met uw eind punt om een [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) -object te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Voor spelling van runtime ophalen

Voeg de volgende methode toe om de aanvraag te maken voor de Voorspellings runtime.

De gebruiker utterance maakt deel uit van het [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) -object.

De methode **GetSlotPredictionAsync** heeft verschillende para meters nodig, zoals de App-ID, de naam van de sleuf, het object voor de Voorspellings aanvraag om te voldoen aan de aanvraag. De andere opties, zoals uitgebreid, alle intenten weer geven en logboeken zijn optioneel.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Hoofd code voor de voor spelling

Gebruik de volgende methode om de variabelen en methoden samen te koppelen om de voor spelling te verkrijgen.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `dotnet run` van de toepassingsmap.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de voor spelling, moet u het werk van deze Quick Start opschonen door het program.cs-bestand en de bijbehorende submappen te verwijderen.
