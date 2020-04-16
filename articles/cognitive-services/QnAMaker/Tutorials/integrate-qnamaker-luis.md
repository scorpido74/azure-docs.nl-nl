---
title: LUIS en QnAMaker - botintegratie
titleSuffix: Azure Cognitive Services
description: Naarmate uw QnA Maker-kennisbank groter wordt, wordt het moeilijk om deze als één monolithische set te onderhouden. Splits de kennisbasis op in kleinere, logische brokken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402708"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Gebruik een bot met QnA Maker en LUIS om uw kennisbank te distribueren
Naarmate uw QnA Maker-kennisbank groter wordt, wordt het moeilijk om deze als één monolithische set te onderhouden. Splits de kennisbasis op in kleinere, logische brokken.

Hoewel het eenvoudig is om meerdere kennisbases in QnA Maker te maken, heb je wat logica nodig om de binnenkomende vraag naar de juiste kennisbank te leiden. U dit doen met LUIS.

In dit artikel wordt gebruik gebruikt van de Bot Framework v3 SDK. Zie [Meerdere LUIS- en QnA-modellen gebruiken](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)als u geïnteresseerd bent in de Bot Framework v4 SDK-versie van deze informatie.

## <a name="architecture"></a>Architectuur

![Afbeelding met architectuur van QnA Maker met taalbegrip](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

De voorgaande afbeelding laat zien dat QnA Maker eerst de bedoeling van de binnenkomende vraag krijgt van een LUIS-model. QnA Maker gebruikt die intentie vervolgens om de vraag naar de juiste QnA Maker-kennisbank te leiden.

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Meld u aan bij het [LUIS-portaal.](https://www.luis.ai/)
1. [Een app maken](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Voeg een intentie toe](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) voor elke QnA Maker-kennisbank. De voorbeelduitingen moeten overeenkomen met vragen in de kennisbanken van QnA Maker.
1. [Train de LUIS-app](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) en [publiceer de LUIS-app](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Noteer in de sectie **Beheren** uw LUIS-app-id, LUIS-eindpuntsleutel en [aangepaste domeinnaam](../../cognitive-services-custom-subdomains.md). U zult deze waarden later nodig hebben.

## <a name="create-qna-maker-knowledge-bases"></a>Maak kennisbanken van QnA Maker

1. Meld u aan bij [QnA Maker](https://qnamaker.ai).
1. [Maak](https://www.qnamaker.ai/Create) een kennisbank voor elke intentie in de LUIS-app.
1. Test en publiceer de kennisbanken. Wanneer u elk onderdeel publiceert, noteer dan de id, de naam van de resource (het aangepaste subdomein vóór _.azurewebsites.net/qnamaker)_ en de eindpuntsleutel voor autorisatie. U zult deze waarden later nodig hebben.

    In dit artikel wordt ervan uitgegaan dat de kennisbases allemaal zijn gemaakt in hetzelfde Azure QnA Maker-abonnement.

    ![Schermafbeelding van http-aanvraag voor QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web-app-bot

1. [Maak een 'Basic'-bot met Azure Bot-service,](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0)die automatisch een LUIS-app bevat. Selecteer de programmeertaal C#.

1. Nadat u de web-app bot hebt gemaakt, selecteert u in de Azure-portal de web-app-bot.
1. Selecteer **toepassingsinstellingen**in de navigatie van de web-app botservice . Blader vervolgens omlaag naar het gedeelte **Toepassingsinstellingen** met beschikbare instellingen.
1. Wijzig de **LuisAppId** in de waarde van de LUIS-app die in de vorige sectie is gemaakt. Selecteer vervolgens **Opslaan**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>De code in het BasicLuisDialog.cs-bestand wijzigen
1. Selecteer **Build** in de sectie Bot Beheer van de **Build**navigatie met de web-app bot in de Azure-portal.
2. Selecteer **Online codeeditor openen**. Er wordt een nieuw browsertabblad geopend met de online bewerkingsomgeving.
3. Selecteer in de sectie **WWWROOT** de map **Dialoogvensters** en open **BasicLuisDialog.cs**.
4. Afhankelijkheden toevoegen aan de **BasicLuisDialog.cs** bovenkant van het BasicLuisDialog.cs-bestand:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Voeg de volgende klassen toe om het antwoord van QnA Maker te deserialiseren:

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Voeg de volgende klasse toe om een HTTP-aanvraag in te dienen bij de QnA Maker-service. De waarde van de **kopautorisatie** bevat `EndpointKey`het woord , met een spatie die het woord volgt. Het JSON-resultaat wordt gedeserialiseerd in de voorgaande klassen en het eerste antwoord wordt geretourneerd.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Wijzig `BasicLuisDialog` de klasse. Elke LUIS-intentie moet een methode hebben die is versierd met **LuisIntent.** De parameter voor de decoratie is de werkelijke LUIS-intentienaam. De methodenaam die is _versierd, moet_ de luis-intentienaam zijn voor leesbaarheid en onderhoud, maar hoeft niet hetzelfde te zijn bij ontwerp of runtime.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Bouw de bot
1. Klik in de codeeditor met de rechtermuisknop op **build.cmd**en selecteer **Uitvoeren vanuit Console**.

    ![Schermafbeelding van de optie Uitvoeren van console in de codeeditor](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. De codeweergave wordt vervangen door een terminalvenster dat de voortgang en resultaten van de build weergeeft.

    ![Schermafbeelding van de consolebuild](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Test de bot
Selecteer in de Azure-portal **Testen in webchat** om de bot te testen. Typ berichten uit verschillende intenties om het antwoord te krijgen van de bijbehorende kennisbank.

![Schermafbeelding van webchattest](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Integreer uw kennisbank met een agent in Power Virtual Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
