---
title: 'Snelstartgids: Bing Automatische suggesties-client bibliotheek voor Go | Microsoft Docs'
description: Ga aan de slag met de Bing Automatische suggesties-client bibliotheek voor Go en krijg Zoek suggesties op basis van gedeeltelijke query reeksen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474086"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Snelstartgids: Bing Automatische suggesties-client bibliotheek voor Go

Ga aan de slag met de Bing Automatische suggesties-client bibliotheek voor go. Volg deze stappen om de bibliotheek te installeren en onze voor beelden voor basis taken uit te proberen. 

Gebruik de Bing Automatische suggesties-client bibliotheek voor Go om Zoek suggesties op basis van gedeeltelijke query reeksen op te halen.

[Referentie documentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [bron code](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) van de bibliotheek | [voorbeeld code](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Instellen

### <a name="create-an-azure-resource"></a>Een Azure-resource maken 

Begin met het gebruik van de Bing Automatische suggesties-client bibliotheek door een Azure-resource te maken. Kies het resource type hieronder dat het meest geschikt is voor u:

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
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

### <a name="create-a-new-go-project"></a>Een nieuw go-project maken

Maak in een console venster (cmd, Power shell, Terminal, bash) een nieuwe werk ruimte voor uw Go-project en navigeer ernaar. Uw werk ruimte bevat drie mappen: 

* **src** -deze map bevat bron code en pakketten. Alle pakketten die met de `go get` opdracht worden geïnstalleerd, worden hier opgeslagen.
* **pakket** -deze map bevat de gecompileerde go package-objecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin** : deze map bevat de binaire uitvoer bare bestanden die worden gemaakt wanneer u `go install`uitvoert.

> [!TIP]
> Meer informatie over de structuur van een [Go-werk ruimte](https://golang.org/doc/code.html#Workspaces). Deze hand leiding bevat informatie over het instellen van `$GOPATH` en `$GOROOT`.

We gaan een werk ruimte maken met de naam `my-app` en de vereiste submappen voor `src`, `pkg`en `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>De client bibliotheek voor Go installeren

Nu gaan we de client bibliotheek installeren voor Go: 

```bash
$ go get -u <library-location-or-url>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Uw Go-toepassing maken

Vervolgens maakt u een bestand met de naam `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Open `sample-app.go` en voeg de naam van het pakket toe en importeer de volgende bibliotheken:

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

Maak een functie met de naam `main`. Vervolgens maakt u omgevings variabelen voor de Bing Automatische suggesties sleutel en het eind punt.

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

Deze code voorbeelden laten zien hoe u basis taken kunt volt ooien met behulp van de Bing Automatische suggesties-client bibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Een API-aanvraag verzenden](#send-an-api-request)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE] 
> In deze Quick Start wordt ervan uitgegaan dat u [een omgevings variabele hebt gemaakt](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de automatische suggestie van Bing, met de naam `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`en één voor het eind punt met de naam `BING_AUTOSUGGEST_ENDPOINT`.

In de functie `main()` maakt u een client met uw eind punt en sleutel. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Een API-aanvraag verzenden

In dezelfde methode gebruikt u de methode [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query naar Bing te verzenden. Herhaal vervolgens het antwoord op de [suggesties](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) en druk het eerste voor stel af.

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

Voer uw Go-toepassing uit met de opdracht `go run [arguments]` vanuit de map van uw toepassing.

```Go
go run sample-app.go
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
- [Naslaghandleiding Bing Automatische suggesties-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
