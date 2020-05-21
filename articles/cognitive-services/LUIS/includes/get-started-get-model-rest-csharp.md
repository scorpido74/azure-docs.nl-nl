---
title: 'Model met REST-aanroep in C ophalen #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: b1bf3c0d7c902a048881b5fb75783744214b073e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655524"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding: resource 32-teken sleutel en bewerkings eind punt-URL ontwerpen. Maken met de [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure cli](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) -app uit de `Azure-Samples/cognitive-services-sample-data-files` github-opslag plaats.
* De LUIS-toepassings-ID voor de geïmporteerde pizza-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt.
* [.NET Core 3,1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model via een programma wijzigen

1. Maak een nieuwe console toepassing met als doel de C#-taal, met de naam van het project en de map van `csharp-model-with-rest` .

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. Ga naar de `csharp-model-with-rest` map die u hebt gemaakt en installeer de vereiste afhankelijkheden met de volgende opdrachten:

    ```console
    cd csharp-model-with-rest
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
            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            static string appID = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            static string authoringKey = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this endpoint with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            static string endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number.
            static string appVersion = "0.1";

            static string host = String.Format("{0}luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

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
                        'text': 'order a pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'order a large pepperoni pizza',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 6,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 14,
                                'endCharIndex': 28
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 8,
                                'endCharIndex': 12
                            }
                        ]
                    },
                    {
                        'text': 'I want two large pepperoni pizzas on thin crust',
                        'intentName': 'ModifyOrder',
                        'entityLabels': [
                            {
                                'entityName': 'Order',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'FullPizzaWithModifiers',
                                'startCharIndex': 7,
                                'endCharIndex': 46
                            },
                            {
                                'entityName': 'PizzaType',
                                'startCharIndex': 17,
                                'endCharIndex': 32
                            },
                            {
                                'entityName': 'Size',
                                'startCharIndex': 11,
                                'endCharIndex': 15
                            },
                            {
                                'entityName': 'Quantity',
                                'startCharIndex': 7,
                                'endCharIndex': 9
                            },
                            {
                                'entityName': 'Crust',
                                'startCharIndex': 37,
                                'endCharIndex': 46
                            }
                        ]
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

1. Vervang de waarden die beginnen met `YOUR-` met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-APP-ID`| De ID van uw LUIS-app. |
    |`YOUR-AUTHORING-KEY`|De bewerkings sleutel voor uw 32-teken.|
    |`YOUR-AUTHORING-ENDPOINT`| Het eind punt van de ontwerp-URL. Bijvoorbeeld `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. U stelt de naam van de resource in wanneer u de resource hebt gemaakt.|

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-Portal in de sectie beheren op de pagina **Azure-resources** . De App-ID is beschikbaar in hetzelfde gedeelte beheren op de pagina **Toepassings instellingen** .

1. Bouw de consoletoepassing.

    ```console
    dotnet build
    ```

1. Voer de consoletoepassing uit. De console-uitvoer bevat dezelfde JSON die u eerder hebt gezien in het browser venster.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u de projectmap uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)
