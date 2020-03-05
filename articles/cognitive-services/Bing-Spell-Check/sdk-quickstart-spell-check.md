---
title: 'Snelstartgids: spelling controleren met de Bing Spellingcontrole SDK voorC#'
titleSuffix: Azure Cognitive Services
description: Aan de slag met de Bing Spellingcontrole REST API om spelling en grammatica te controleren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 1cda7032d5bfe58e9f8bcbdb8b18dd597a691441
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273533"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Snelstartgids: spelling controleren met de Bing Spellingcontrole SDK voorC#

Gebruik deze quickstart om te beginnen met de Bing Spellingcontrole-SDK voor C#. Hoewel de Bing Spellingcontrole een REST-API heeft die compatibel is met de meeste programmeertalen, biedt de SDK een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Afhankelijkheden van de toepassing

* Een versie van [Visual Studio 2017 of hoger](https://visualstudio.microsoft.com/downloads/).
* [NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) voor de Bing Spellingcontrole

Als u de Bing Spellingcontrole SDK wilt toevoegen aan uw project, selecteert u **NuGet-pakketten beheren** in **Solution Explorer** in Visual Studio. Voeg het pakket `Microsoft.Azure.CognitiveServices.Language.SpellCheck` toe. Dit pakket installeert ook de volgende afhankelijkheden:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>De toepassing maken en initialiseren

1. Maak een nieuwe C#-console-oplossing in Visual Studio. Voeg daarna de volgende `using`-instructie toe.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Maak een nieuwe klasse. Maak vervolgens een asynchrone functie met de naam `SpellCheckCorrection()` die een abonnementssleutel vereist en de aanvraag van de spellingcontrole verstuurt.

3. Maak een instantie van de client door een nieuw `ApiKeyServiceClientCredentials`-object te maken. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>De aanvraag verzenden en het antwoord lezen

1. Voer in de hierboven gemaakte functie de volgende stappen uit. Verstuur de aanvraag van de spellingcontrole met de client. Voeg de te controleren tekst toe aan de `text`-parameter en stel de modus in op `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Haal het eerste resultaat van de spellingcontrole op, als dat beschikbaar is. Druk het eerste verkeerd gespelde woord (token) af, het type token en het aantal suggesties.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. De eerste voorgestelde correctie ophalen, indien aanwezig. De Score van het voor stel en het voorgestelde woord afdrukken. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Uw project bouwen en uitvoeren. Als u Visual Studio gebruikt, drukt u op **F5** om fouten in het bestand op te sporen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app met één pagina maken](tutorials/spellcheck.md)

- [Wat is de Bing Spellingcontrole-API?](overview.md)
- [Naslag C# gids voor Bing spellingcontrole SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
