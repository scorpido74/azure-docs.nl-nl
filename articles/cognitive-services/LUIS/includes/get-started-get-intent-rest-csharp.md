---
title: 'Krijg voorspelling met REST call in C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733259"
---
## <a name="prerequisites"></a>Vereisten

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Een LUIS-app-id - gebruik de `df67dcdb-c37d-46af-88e1-8b97951ca1c2`openbare IoT-app-id van . De gebruikersquery die wordt gebruikt in de quickstartcode is specifiek voor die app.

## <a name="create-luis-runtime-key-for-predictions"></a>Luis-runtime-sleutel maken voor voorspellingen

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Klik [op **Taalverstaan maken** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen voor Runtime-toets in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Passend abonnement selecteren|
    |Locatie|Selecteer een nabijgelegen en beschikbare locatie|
    |Prijscategorie|`F0`- de minimale prijscategorie|
    |Resourcegroep|Een beschikbare resourcegroep selecteren|

1. Klik **op Maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, navigeert u naar de resourcepagina.
1. Verzamel geconfigureerd `endpoint` en `key`een .

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik C# (.NET Core) om het [voorspellingseindpunt](https://aka.ms/luis-apim-v3-prediction) op te vragen en een voorspellingsresultaat te krijgen.

1. Maak een nieuwe consoletoepassing die is gericht op de `predict-with-rest`C#-taal, met een project- en mapnaam van .

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Wijzig de `predict-with-rest` map die u zojuist hebt gemaakt en installeer vereiste afhankelijkheden met deze opdrachten:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Open `Program.cs` in je favoriete IDE of editor. Overschrijven `Program.cs` met de volgende code:

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

1. Vervang `YOUR-KEY` de `YOUR-ENDPOINT` waarden door uw eigen voorspellingssleutel en eindpunt.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32 karakter voorspelling sleutel.|
    |`YOUR-ENDPOINT`| Uw voorspelling URL eindpunt. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Bouw de consoletoepassing met deze opdracht:

    ```console
    dotnet build
    ```

1. Voer de consoletoepassing uit. De console-uitvoer geeft dezelfde JSON weer die u eerder in het browservenster hebt gezien.

    ```console
    dotnet run
    ```

1. Bekijk de voorspellingsrespons, die wordt geretourneerd als JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    De JSON-respons die is opgemaakt voor leesbaarheid:

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

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen toevoegen en trainen](../get-started-get-model-rest-apis.md)