---
title: 'Quick Start C# : eind punt SDK-query Voorspellings-Luis'
titleSuffix: Azure Cognitive Services
description: Gebruik de C# SDK om een uiting van een gebruiker te verzenden naar LUIS en een voorspelling te ontvangen.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d7cf46add464a089f15a5bd15a20cea7d17eed37
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488690"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>Quick Start: een query v2-eind punt C# voor voor spelling met .NET SDK

Gebruik de .NET SDK, gevonden op [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), om een uiting van een gebruiker te verzenden naar LUIS (Language Understanding) en een voorspelling van de intentie van de gebruiker te ontvangen. 

In deze quickstart wordt er een uiting van een gebruiker, zoals `turn on the bedroom light`, verzonden naar een openbare LUIS-toepassing (Language Understanding). Vervolgens wordt de voorspelling ontvangen en worden de best scorende intentie `HomeAutomation.TurnOn` en de entiteit `HomeAutomation.Room` uit de intentie weergegeven. 

## <a name="prerequisites"></a>Vereisten

* [Visual Studio Community 2017-editie](https://visualstudio.microsoft.com/vs/community/)
* C#-programmeertaal (meegeleverd met VS Community 2017)
* Id van openbare app: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> De volledige oplossing is beschikbaar in de GitHub-opslagplaats [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Zoekt u meer documentatie?

 * [SDK-naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Cognitive Services- of LUIS-sleutel (Language Understanding) verkrijgen

Als u de openbare app voor huisautomatisering wilt gebruiken, hebt u een geldige sleutel voor eindpuntvoorspellingen nodig. U kunt een Cognitive Services-sleutel (hieronder gemaakt met de Azure-CLI) gebruiken, die geldig is voor veel cognitieve services, of een `Language Understanding`-sleutel. 

Gebruik de volgende [Azure CLI-opdracht voor het maken van een Cognitive Service-sleutel](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core-project maken

Maak een .NET Core-consoleproject in Visual Studio Community 2017.

1. Open Visual Studio Community 2017.
1. Maak een nieuw project en kies in de sectie **Visual C#**  de optie **Console App (.NET Core)** .
1. Voer de projectnaam `QueryPrediction` in, behoud de andere standaardwaarden en selecteer **OK**.
    Hiermee maakt u een eenvoudig project met het primaire codebestand **Program.cs**.

## <a name="add-sdk-with-nuget"></a>SDK toevoegen met NuGet

1. In de **Solution Explorer** selecteert u in de structuurweergave het project met de naam **QueryPrediction**. Klik er met de rechtermuisknop op. Selecteer in het menu de optie **NuGet-pakketten beheren...** .
1. Selecteer **Bladeren** en voer `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime` in. Wanneer de pakketgegevens worden weergegeven, selecteert u **Installeren** om het pakket te installeren in het project. 
1. Voeg de volgende _using_-instructies toe aan het begin van **Program.cs**. Verwijder de bestaande _using_-instructie voor `System` niet. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Een nieuwe methode maken voor de voorspelling

Maak een nieuwe methode `GetPrediction` om de query te verzenden naar het eindpunt van de queryvoorspelling. De methode maakt en configureert alle benodigde objecten retourneert vervolgens een `Task` met de voorspellingsresultaten [`LuisResult`](https://docs.microsoft.com/en-us/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-python). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Referentieobject maken

Voeg de volgende code toe aan de methode `GetPrediction` om de clientreferenties te maken met uw Cognitive Service-sleutel.

Vervang `<REPLACE-WITH-YOUR-KEY>` door de regio van uw Cognitive Service-sleutel. De sleutel vindt u in de [Azure-portal](https://portal.azure.com) op de pagina Sleutels voor die resource.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding-client maken

Voeg in de methode `GetPrediction` na de voorgaande code de volgende code toe om de nieuwe referenties te maken, waarbij clientobject [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) wordt gemaakt. 

Vervang `<REPLACE-WITH-YOUR-KEY-REGION>` door de regio van uw sleutel, bijvoorbeeld`westus`. De regio van de sleutel vindt u in de [Azure-portal](https://portal.azure.com) op de overzichtspagina voor die resource.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Queryparameters instellen

Voeg in de methode `GetPrediction` na de voorgaande code de volgende code toe om de queryparameters in te stellen.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

Voeg in de methode `GetPrediction` na de voorgaande code de volgende code toe om de queryparameters in te stellen:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Voorspellingsresultaten weergeven

Wijzig de methode **Main** zodanig dat de nieuwe methode `GetPrediction` wordt aangeroepen en de voorspellingsresultaten worden geretourneerd:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Het project uitvoeren

Bouw het project in Studio en voer het project uit om de uitvoer van de query te bekijken:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Volgende stappen

Lees meer over de [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) en de [.NET-naslagdocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Zelf studie: LUIS-app bouwen om de bedoelingen van de gebruiker te bepalen](luis-quickstart-intents-only.md) 