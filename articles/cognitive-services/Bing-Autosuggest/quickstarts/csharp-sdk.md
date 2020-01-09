---
title: 'Snelstartgids: Bing Automatische suggesties-client bibliotheek voor .NET | Microsoft Docs'
description: Ga aan de slag met de Bing Automatische suggesties-client bibliotheek voor .NET en krijg Zoek suggesties op basis van gedeeltelijke query reeksen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473925"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Snelstartgids: Bing Automatische suggesties-client bibliotheek voor .NET

Ga aan de slag met de Bing Automatische suggesties-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.

Gebruik de Bing Automatische suggesties-client bibliotheek voor .NET om Zoek suggesties op basis van gedeeltelijke query teken reeksen op te halen.

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [voorbeeld code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-an-azure-resource"></a>Een Azure-resource maken

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

>[!NOTE]
> De eind punten voor niet-proef resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weer gegeven. Zie [aangepaste subdomein namen voor Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)voor meer informatie en een volledige lijst met regionale eind punten. 

Met uw sleutel en eind punt van de resource die u hebt gemaakt, maakt u twee omgevings variabelen voor verificatie:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: de resource sleutel voor het verifiëren van uw aanvragen.
* `AUTOSUGGEST_ENDPOINT`: het resource-eind punt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Volg de instructies voor uw besturings systeem.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevings variabele hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `bing-autosuggest-quickstart`. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
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

Open het *Program.cs* -bestand in de map van het project in uw voorkeurs editor of IDE. Voeg de volgende `using`-instructies toe:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Maak in de klasse `Program` variabelen voor het Azure-eind punt en de sleutel van uw resource. Als u de omgevings variabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

Voeg in de `Main` methode van de toepassing de volgende methode-aanroepen toe, die u later wilt definiëren.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de Bing Automatische suggesties-client bibliotheek voor .NET in de toepassingsmap met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Bing Automatische suggesties-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een automatische suggestie aanvraag verzenden](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor uw Bing automatische suggesties sleutel, met de naam `AUTOSUGGEST_SUBSCRIPTION_KEY`en één voor het eind punt met de naam `AUTOSUGGEST_ENDPOINT`.


In een nieuwe asynchrone methode maakt u een exemplaar van een client met het eind punt en de sleutel. Maak een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) -object met uw sleutel en gebruik het met uw eind punt om een [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) -object te maken.

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Een automatische suggestie aanvraag verzenden

In dezelfde methode gebruikt u de methode [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query naar Bing te verzenden. Herhaal vervolgens het antwoord op de [suggesties](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) en druk het eerste voor stel af.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit de toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie over Bing Automatische suggesties](../tutorials/autosuggest.md)

## <a name="see-also"></a>Zie ook

- [Wat is Bing Automatische suggesties?](../get-suggested-search-terms.md)
- [Bing Automatische suggesties DotNet-verwijzing](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
