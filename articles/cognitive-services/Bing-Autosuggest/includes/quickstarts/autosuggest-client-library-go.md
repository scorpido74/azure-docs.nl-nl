---
title: Bing Automatisch voorstellen Go-clientbibliotheek snel aan de slag
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: aahi
ms.openlocfilehash: b352e785673d7c4ed3a9b346758ef0d1fa68b36d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887541"
---
Ga aan de slag met de Bing Autosuggest-clientbibliotheek voor Onderweg. Volg deze stappen om de bibliotheek te installeren en probeer onze voorbeelden uit voor basistaken. 

Gebruik de Bing Autosuggest-clientbibliotheek voor Gaan om zoeksuggesties te ontvangen op basis van gedeeltelijke querytekenreeksen.

[Voorbeeldcode van naslagdocumentatiebibliotheek](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Library source code](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Sample code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Instellen

### <a name="create-an-azure-resource"></a>Een Azure-bron maken 

Begin met het gebruik van de Bing Autosuggest-clientbibliotheek door een Azure-bron te maken. Kies het brontype hieronder dat bij u in het juiste is:

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
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

### <a name="create-a-new-go-project"></a>Een nieuw Go-project maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project en navigeer ernaar. Uw werkruimte bevat drie mappen: 

* **src** - Deze map bevat broncode en pakketten. Alle pakketten die `go get` met de opdracht zijn geïnstalleerd, bevinden zich hier.
* **pkg** - Deze directory bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben `.a` allemaal een extensie.
* **opslaglocatie** - Deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u deze uitvoert. `go install`

> [!TIP]
> Meer informatie over de structuur van een [Go-werkruimte](https://golang.org/doc/code.html#Workspaces). Deze handleiding bevat `$GOPATH` informatie `$GOROOT`voor het instellen en .

Laten we een werkruimte `my-app` maken genaamd en `src`de `pkg`vereiste `bin`submappen voor , en :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>De clientbibliotheek voor Go installeren

Laten we nu de clientbibliotheek voor Go installeren: 

```bash
$ go get -u <library-location-or-url>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Uw Go-toepassing maken

Laten we vervolgens een bestand `src/sample-app.go`maken met de naam:

```bash
$ cd src
$ touch sample-app.go
```

Open `sample-app.go` en voeg de pakketnaam toe en importeer de volgende bibliotheken:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Een functie `main`met de naam maken . Maak vervolgens omgevingsvariabelen voor uw Bing Autosuggest-toets en eindpunt.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Codevoorbeelden

In deze codevoorbeelden ziet u hoe u basistaken uitvoeren met de Bing Autosuggest-clientbibliotheek voor Onderweg:

* [De client verifiëren](#authenticate-the-client)
* [Een API-aanvraag verzenden](#send-an-api-request)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE] 
> Met deze snelstart wordt ervan uitgegaan dat u [een omgevingsvariabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) hebt gemaakt voor uw Bing-toets met de naam `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`en een voor uw eindpunt met de naam `BING_AUTOSUGGEST_ENDPOINT`.

In `main()` de functie u een klant instantiëren met uw eindpunt en sleutel. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Een API-aanvraag verzenden

Gebruik in dezelfde methode de [AutoSuggestMethodAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query naar Bing te verzenden. Vervolgens herhalen over de [suggesties](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) antwoord, en druk de eerste suggestie.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw Go-toepassing uit met de `go run [arguments]` opdracht uit uw toepassingsmap.

```Go
go run sample-app.go
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
- [Naslaghandleiding Bing Automatische suggesties-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
