---
title: Bing Autosuggest C# clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: aahi
ms.openlocfilehash: ac46eb0119ac95cf09e48823686a6c563d8d4d4a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887534"
---
Ga aan de slag met de Bing Autosuggest-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.

Gebruik de bing-clientbibliotheek voor .NET automatisch voorstellen om zoeksuggesties te ontvangen op basis van gedeeltelijke querytekenreeksen.

[Voorbeeldcode](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | van de[Sample code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs) [naslagdocumentatiebibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-an-azure-resource"></a>Een Azure-bron maken

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

>[!NOTE]
> De eindpunten voor niet-proefresources die na 1 juli 2019 zijn gemaakt, gebruiken de aangepaste subdomeinindeling hieronder. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)meer informatie en een volledige lijst met regionale eindpunten. 

Maak met behulp van uw sleutel en eindpunt van de resource die u hebt gemaakt twee omgevingsvariabelen voor verificatie:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`- De resourcesleutel voor het verifiëren van uw aanvragen.
* `AUTOSUGGEST_ENDPOINT`- Het resourceeindpunt voor het verzenden van API-aanvragen. Het zal er als volgt uitzien: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Gebruik de instructies voor uw besturingssysteem.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[macOS](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE. 

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `bing-autosuggest-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```console
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

Open in de projectmap het *program.cs* bestand in uw voorkeurseditor of IDE. Voeg de `using` volgende richtlijnen toe:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Maak `Program` in de klasse variabelen voor het Azure-eindpunt en de sleutel van uw bron. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

Voeg in de `Main` methode van de toepassing de volgende methodeaanroepen toe, die u later zult definiëren.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de clientbibliotheek Bing Autosuggest voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="code-examples"></a>Codevoorbeelden

In deze codefragmenten ziet u hoe u de volgende taken uitvoert met de Bing Autosuggest-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een verzoek voor automatisch voorstellen verzenden](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Met deze snelstart wordt ervan uitgegaan dat u [een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Bing Autosuggest-sleutel, met de naam `AUTOSUGGEST_SUBSCRIPTION_KEY`, en een voor uw eindpunt met de naam `AUTOSUGGEST_ENDPOINT`.


In een nieuwe asynchrone methode u een client instantiëren met uw eindpunt en sleutel. Maak een [ApiKeyServiceClientCredentials-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) met uw sleutel en gebruik het met uw eindpunt om een [AutosuggestClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) te maken.

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

## <a name="send-an-autosuggest-request"></a>Een verzoek voor automatisch voorstellen verzenden

Gebruik in dezelfde methode de [AutoSuggestMethodAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query naar Bing te verzenden. Vervolgens herhalen over de [suggesties](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) antwoord, en druk de eerste suggestie.

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

Voer de toepassing uit uw `dotnet run` toepassingsmap uit met de opdracht.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie over Bing Automatische suggesties](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Zie ook

- [Wat is Bing Automatische suggesties?](../../get-suggested-search-terms.md)
- [Bing Autosuggest dotnet referentie](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
