---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 05355ad37183d4c14cb8f6598141292ded0386d9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906978"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw go-project met behulp van uw favoriete IDE of editor of een nieuwe map op uw bureau blad. Kopieer vervolgens dit code fragment naar uw project/map in een bestand met `get-languages.go`de naam.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>De hoofdfunctie toevoegen

We maken de hoofdfunctie voor de toepassing. U ziet dat deze bestaat uit één regel code. Dat komt doordat we één functie maken voor het ophalen en afdrukken van de lijst met ondersteunde talen voor Translator Text.

In dit voor beeld wordt geprobeerd uw Translator Text-eind punt te lezen op `TRANSLATOR_TEXT_ENDPOINT`basis van een omgevings variabele:. Als u niet bekend bent met omgevingsvariabelen, kunt u `endpoint` als tekenreeks instellen en een opmerking plaatsen in de voorwaardelijke instructie.

Kopieer deze code naar uw project:

```go
func main() {
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/languages?api-version=3.0"
    getLanguages(uri)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Een functie maken om een lijst van ondersteunde talen op te halen

U gaat nu een functie maken om een lijst van ondersteunde talen op te halen.

```go
func getLanguages(uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Maak nu de URL. De URL wordt gebouwd met behulp van de methoden `Parse()` en `Query()`.

Kopieer deze code naar de functie `getLanguages`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Meer informatie over eindpunten, routes en aanvraagparameters vindt u in [Translator Text-API 3.0: talen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>De aanvraag maken

Nu u de aanvraagbody als JSON hebt gecodeerd, kunt u de POST-aanvraag maken en de Translator Text-API aanroepen.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Als u een Cognitive Services abonnement op meerdere services gebruikt, moet u ook de `Ocp-Apim-Subscription-Region` in uw aanvraag parameters toevoegen. Meer [informatie over verificatie met het multi-service-abonnement](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Het antwoord verwerken en afdrukken

Voeg deze code toe aan de functie `getLanguages` om het JSON-antwoord te decoderen en het resultaat vervolgens op te maken en af te drukken.

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
go run get-languages.go
```

Als u uw code graag wilt vergelijken met de onze, kunt u het volledige voorbeeld vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Voorbeeldantwoord

Zoek de afkorting van het land/de regio in deze [lijst met talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u in het volgende voorbeeld kunt zien:

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de API-verwijzing voor meer informatie over wat u met de Translator Text-API kunt doen.

> [!div class="nextstepaction"]
> [API-naslaginformatie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
