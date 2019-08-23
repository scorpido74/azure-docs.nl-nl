---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 0c2408d8ea8fb6458761ef1d853d5dde52ac2311
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907165"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Go-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `detect-language.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>De hoofdfunctie toevoegen

In dit voor beeld wordt geprobeerd uw Translator text-abonnements sleutel en-eind punt te lezen `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` uit `TRANSLATOR_TEXT_ENDPOINT`deze omgevings variabelen: en. Als u niet bekend bent met omgevings variabelen, kunt u `subscriptionKey` en `endpoint` als teken reeksen instellen en de voorwaardelijke instructies van commentaar voorzien.

Kopieer deze code naar uw project:

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/detect?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    detect(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-detect-the-text-language"></a>Een functie maken om de taal van tekst te detecteren

U gaat nu een functie maken om de taal van een tekst te detecteren. Met deze functie wordt één argument gebruikt, uw Translator Text-abonnementssleutel.

```go
func detect(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Maak nu de URL. De URL wordt gebouwd met behulp van de methoden `Parse()` en `Query()`.

Kopieer deze code naar de functie `detect`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Meer informatie over eindpunten, routes en aanvraagparameters vindt u in [Translator Text-API 3.0: detecteren](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

## <a name="create-a-struct-for-your-request-body"></a>Een structuur maken voor de aanvraagbody

Vervolgens maakt u een anonieme structuur voor de aanvraagbody en codeert u deze met `json.Marshal()` als JSON. Voeg deze code toe aan de functie `detect`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Salve, Mondo!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>De aanvraag maken

Nu u de aanvraagbody als JSON hebt gecodeerd, kunt u de POST-aanvraag maken en de Translator Text-API aanroepen.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Als u een Cognitive Services abonnement op meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraag parameters toevoegen. Meer [informatie over verificatie met het multi-service-abonnement](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Het antwoord verwerken en afdrukken

Voeg deze code toe aan de functie `detect` om het JSON-antwoord te decoderen en het resultaat vervolgens op te maken en af te drukken.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Alles samenvoegen

Dat was het. U hebt een eenvoudig programma gemaakt dat we de Translator Text-API zullen noemen. Er is een JSON-antwoord geretourneerd. Het is nu tijd om uw programma uit te voeren:

```console
go run detect-language.go
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Voorbeeldantwoord

Zoek de afkorting van het land/de regio in deze [lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de Translator Text-API kunt doen.

> [!div class="nextstepaction"]
> [API-naslaginformatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
