---
title: Model krijgen met REST-gesprek in Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 8d180eeffdbc41db6fa0e636daf7702faad47fcc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368410"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding - Authoring resource 32 tekensleutel en url voor het schrijven van eindpunten. Maken met de [Azure-portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent-app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) vanuit de GitHub-repository die gebruik maakt van de GitHub-opslagplaats voor cognitieve services.
* De LUIS toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt. De standaard-id is '0.1'.
* [Ga](https://golang.org/) programmeertaal
* [Visual Studio-code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een nieuw bestand met de naam `predict.go`. Voeg de volgende code toe:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Vervang de waarden `YOUR-` die beginnen met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32 teken authoring toets.|
    |`YOUR-ENDPOINT`| Uw URL-eindpunt voor het ontwerpen. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`. U stelt uw resourcenaam in toen u de resource maakte.|
    |`YOUR-APP-ID`| Uw LUIS-app-id. |

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-portal in de sectie Beheren op de pagina **Azure-bronnen.** De app-id is beschikbaar in dezelfde sectie Beheren, op de pagina **Toepassingsinstellingen.**

1. Voer de volgende opdracht in om het Go-bestand samen te stellen met een opdrachtprompt in dezelfde map als waar u het bestand hebt gemaakt:

    ```console
    go build model.go
    ```

1. Voer de Go-toepassing uit vanaf de opdrachtregel door de volgende tekst in te voeren in de opdrachtprompt:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)