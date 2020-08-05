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
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369248"
---
Gebruik de clientbibliotheek met voorspellingen van LUIS (Language Understanding) voor .NET voor het volgende:

* Voorspelling per sleuf ophalen
* Voorspelling per versie

[Referentiemateriaal](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Voorspellingsruntimepakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C#-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Vereisten

* Language Understanding (LUIS)-portalaccount - [Maak er een gratis](https://www.luis.ai)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Een LUIS-app-id: gebruik de openbare IoT-app-id van `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. De gebruikersquery die in de code van de quickstart wordt gebruikt, is specifiek voor die app.

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

Installeer in de toepassingsmap de voorspellingsruntime-clientbibliotheek van Language Understanding (LUIS) voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="object-model"></a>Objectmodel

De voorspellingsruntime-client van Language Understanding (LUIS) is een [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)-object dat wordt geverifieerd bij Azure, dat de bronsleutel bevat.

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Voorspelling per [fasering of productsleuf](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Voorspelling per [versie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt doen met de voorspellingsruntime-clientbibliotheek van Language Understanding (LUIS) voor .NET:

* [Voorspelling per sleuf](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Vervang de bestaande `using`-code door de volgende `using`-instructies:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>De client verifiëren

1. Maak variabelen voor de sleutel, de resourcenaam, de app-id en de publicatiesleuf. Stel de app-id in op de openbare IoT-app:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)-object te maken.

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Voorspelling uit runtime ophalen

Voeg de volgende methode toe om de aanvraag aan de voorspellingsruntime te maken.

De uiting van de gebruiker maakt deel uit van het [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)-object.

De **GetSlotPredictionAsync**-methode heeft verschillende parameters nodig, zoals de app-id, de naam van de sleuf en het voorspellingsaanvraagobject, om aan de aanvraag te voldoen. De andere opties, zoals verbose, show all intents en log, zijn optioneel.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Hoofdcode voor de voorspelling

Gebruik de volgende hoofdmethode om de variabelen en methoden samen te koppelen om de voorspelling op te halen.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de voorspellingen, kunt u het werk van deze quickstart opschonen door het bestand program.cs en de submappen ervan te verwijderen.
