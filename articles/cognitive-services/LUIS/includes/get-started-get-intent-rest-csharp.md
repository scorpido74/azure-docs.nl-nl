---
title: Voor spelling ophalen met REST-aanroep inC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 4cbec342bc20de35c0c62284e4e1fe1ae8b8e8a4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966989"
---
## <a name="prerequisites"></a>Vereisten

* [.NET core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID van open bare app: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Een LUIS-runtime sleutel maken voor voor spellingen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **Language Understanding** maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor de runtime sleutel in:

    |Instelling|Waarde|
    |--|--|
    |Name|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijsniveau|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. Geconfigureerde `endpoint` en een `key`verzamelen.

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik C# (.net core) om het [Voorspellings eindpunt](https://aka.ms/luis-apim-v3-prediction) op te vragen en een Voorspellings resultaat te krijgen.

1. Maak een nieuwe console toepassing die is gericht C# op de taal, met de naam van het project en de map van `predict-with-rest`.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Ga naar de map `predict-with-rest` die u zojuist hebt gemaakt en installeer de vereiste afhankelijkheden met de volgende opdrachten:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Open `Program.cs` in uw favoriete IDE of editor. Overschrijf `Program.cs` vervolgens met de volgende code:

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Vervang de `YOUR-KEY` en `YOUR-ENDPOINT` waarden door uw eigen Voorspellings sleutel en-eind punt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32-teken Voorspellings sleutel.|
    |`YOUR-ENDPOINT`| Het eind punt voor de voor Spellings-URL. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Bouw de console toepassing met deze opdracht:

    ```console
    dotnet build
    ```

1. Voer de consoletoepassing uit. De console-uitvoer bevat dezelfde JSON die u eerder hebt gezien in het browser venster.

    ```console
    dotnet run
    ```

1. Bekijk het Voorspellings antwoord dat wordt geretourneerd als JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Het JSON-antwoord dat is opgemaakt voor de Lees baarheid:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen](../get-started-get-model-rest-apis.md)