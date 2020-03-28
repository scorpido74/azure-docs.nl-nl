---
title: 'Model krijgen met REST-oproep in C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 96129b9141b4759fd61b539fa08354f02af3af7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80151044"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding - Authoring resource 32 tekensleutel en url voor het schrijven van eindpunten. Maken met de [Azure-portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent-app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) vanuit de GitHub-repository die gebruik maakt van de GitHub-opslagplaats voor cognitieve services.
* De LUIS toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt. De standaard-id is '0.1'.
* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio-code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een nieuwe consoletoepassing die is gericht op de `model-with-rest`C#-taal, met een project- en mapnaam van .

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Installeer vereiste afhankelijkheden met de volgende dotnet CLI-opdrachten.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Overschrijf Program.cs met de volgende code:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
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
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
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

1. Bouw de consoletoepassing.

    ```console
    dotnet build
    ```

1. Voer de consoletoepassing uit. De console-uitvoer geeft dezelfde JSON weer die u eerder in het browservenster hebt gezien.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)
