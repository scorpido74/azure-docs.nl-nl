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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371904"
---
Gebruik de tekstwaardeclientbibliotheek voor taalverstaan (LUIS) voor .NET om:

* Krijg voorspelling per sleuf

[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Voorspelling runtime Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS) portal account - [Maak er een gratis](https://www.luis.ai)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

Zoekt u meer documentatie?

 * [SDK-naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Instellen

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

Maak met uw sleutel en de naam van uw resource twee omgevingsvariabelen voor verificatie:

* `LUIS_PREDICTION_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `LUIS_ENDPOINT_NAME`- De resourcenaam die aan uw sleutel is gekoppeld.

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

---

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

Installeer in de toepassingsmap de luis-voorspellingsruntimeclientbibliotheek (Language Understanding) voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De Luis-voorspellingsruntimeclient (Language Understanding) is een [LUISRuntimeClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) dat zich verifieert naar Azure, dat uw resourcesleutel bevat.

Zodra de client is gemaakt, gebruikt u deze client om toegang te krijgen tot functionaliteit, waaronder:

* Voorspelling door [fasering of productsleuf](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Voorspelling per [versie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de luis-voorspellingsclientbibliotheek (Language Understanding) voor .NET:

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open in de projectmap het *Program.cs* bestand in uw voorkeurseditor of IDE. Vervang de `using` bestaande code `using` door de volgende richtlijnen:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Variabelen maken voor de sleutel-, naam- en app-id:

    Een variabele voor het beheren van uw `LUIS_PREDICTION_KEY`voorspellingssleutel die is opgehaald uit een omgevingsvariabele met de naam . Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

    Maak een variabele om `LUIS_ENDPOINT_NAME`de naam van uw resource vast te houden.

    Maak een variabele voor de app-id als een omgevingsvariabele met de naam `LUIS_APP_ID`. Stel de omgevingsvariabele in op de openbare IoT-app:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Maak een [Object ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) met uw sleutel en gebruik het met uw eindpunt om een [LUISRuntimeClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Krijg voorspelling van runtime

Voeg de volgende methode toe om de aanvraag te maken aan de voorspellingsruntime.

De uiting van de gebruiker maakt deel uit van het object [PredictionRequest.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)

De **GetSlotPredictionAsync-methode** heeft verschillende parameters nodig, zoals de app-id, de sleufnaam, het voorspellingsverzoekobject om aan de aanvraag te voldoen. De andere opties, zoals verbose, toon alle intenties en logboek zijn optioneel.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden aan elkaar te koppelen om de voorspelling te krijgen.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `dotnet run` uit met de opdracht uit uw toepassingsmap.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met uw voorspellingen, ruim t u het werk van deze quickstart op door het program.cs bestand en de submappen te verwijderen.
