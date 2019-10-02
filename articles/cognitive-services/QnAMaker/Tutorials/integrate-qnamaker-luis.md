---
title: LUIS en QnAMaker - Bot-integratie
titleSuffix: Azure Cognitive Services
description: Als uw QnA Maker knowledge base grote groeit, wordt het moeilijk te onderhouden als één monolithische ingesteld en er behoefte aan de knowledge base splitsen in kleinere logische segmenten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802794"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Gebruik bot met QnA Maker en LUIS om uw Knowledge Base te distribueren
Als uw QnA Maker knowledge base grote groeit, wordt het moeilijk te onderhouden als één monolithische ingesteld en er behoefte aan de knowledge base splitsen in kleinere logische segmenten.

Het is eenvoudig te maken van meerdere knowledge bases in QnA Maker, moet u bepaalde logica voor het routeren van de binnenkomende vraag naar de juiste knowledge base. U kunt dit doen met behulp van LUIS.

Dit artikel wordt gebruikgemaakt van Bot Framework v3 SDK. Lees dit [Bot Framework artikel](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp), als u geïnteresseerd in Bot Framework v4 SDK-versie van deze informatie bent.

## <a name="architecture"></a>Architectuur

![QnA Maker met Language Understanding architectuur](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

In dit scenario, QnA Maker eerst de bedoeling van de binnenkomende vraag opgehaald uit een LUIS-model, en gebruik vervolgens die aan deze wordt doorgestuurd naar de juiste QnA Maker knowledge base.

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Aanmelden bij de [LUIS](https://www.luis.ai/) portal.
1. [Een app maken](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Toevoegen van een doel](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) voor elke QnA Maker knowledge base. De voorbeeld-uitingen moeten overeenkomen met vragen in de QnA Maker knowledge bases.
1. [De LUIS-app trainen](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) en [Publiceer de app LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) uw LUIS-App.
1. Noteer de LUIS-App-ID, de LUIS-eindpunt sleutel en de [aangepaste domein naam](../../cognitive-services-custom-subdomains.md)in de sectie **beheren** . U nodig deze waarden later. 

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker knowledge bases maken

1. Aanmelden bij [QnA Maker](https://qnamaker.ai).
1. [Maak](https://www.qnamaker.ai/Create) een knowledge bases voor elk doel in de LUIS-app.
1. Testen en publiceren van de knowledge bases. Wanneer u elke KB publiceert, noteert u de KB-ID, resource naam (aangepast subdomein vóór _. azurewebsites.net/qnamaker_) en de autorisatie-eindpunt sleutel. U nodig deze waarden later. 

    In dit artikel wordt ervan uitgegaan dat de kB's worden gemaakt in hetzelfde Azure QnA Maker-abonnement.

    ![QnA Maker HTTP-aanvraag](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web app-Bot

1. [Maak een eenvoudige web-app-bot](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) die automatisch een Luis-app bevat. Selecteer C# programmeer taal.

1. Zodra de web-app-bot wordt gemaakt in Azure portal, selecteert u de web-app-bot.
1. Selecteer **toepassingsinstellingen** in de navigatiebalk van Web-app-bot service en schuif vervolgens omlaag naar **toepassingsinstellingen** sectie van de beschikbare instellingen.
1. Wijzig de **LuisAppId** aan de waarde van de LUIS-app gemaakt in de voorgaande sectie vervolgens selecteren **opslaan**.


## <a name="change-code-in-basicluisdialogcs"></a>De code in BasicLuisDialog.cs wijzigen
1. Uit de **Bot Management** sectie van de web-app-bot navigatie in de Azure portal, selecteer **bouwen**.
2. Selecteer **online code-editor openen**. Er wordt een nieuw browsertabblad geopend met de omgeving voor het online bewerken. 
3. In de **WWWROOT** sectie, selecteer de **dialoogvensters** Active directory, en vervolgens open **BasicLuisDialog.cs**.
4. Afhankelijkheden toevoegen aan het begin van de **BasicLuisDialog.cs** bestand:

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

5. Voeg de volgende klassen voor het deserialiseren van het antwoord QnA Maker:

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


6. Voeg de volgende klasse als u wilt maken van een HTTP-aanvraag naar de QnA Maker-service. U ziet dat de **autorisatie** waarde van de header bevat het woord `EndpointKey` met een spatie na het woord. Het JSON-resultaat is gedeserialiseerd in de voorgaande klassen en het eerste antwoord wordt geretourneerd.

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


7. De klasse BasicLuisDialog wijzigen. Elke LUIS-doel moet een methode die is voorzien van **LuisIntent**. De parameter voor de opmaak is de werkelijke naam van de LUIS-intentie. De naam van de methode die is voorzien van _moet_ overeenkomen met de intentie LUIS-naam voor de leesbaarheid en onderhoud, maar hoeft niet te overeenkomen met het ontwerp of tijdens runtime.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
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


## <a name="build-the-bot"></a>De bot bouwen
1. In de code-editor met de rechtermuisknop op `build.cmd` en selecteer **uitvoeren vanaf de Console**.

    ![uitvoeren vanaf de console](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. De weergave van de code wordt vervangen door een terminalvenster waarin de voortgang en resultaten van de build.

    ![console-build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>De bot testen
Selecteer in de Azure portal, **testen in Web Chat** voor het testen van de bot. Typ berichten van verschillende intents om op te halen van het antwoord van het betreffende knowledge base.

![chat WebTest](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maak een plan voor bedrijfscontinuïteit voor QnA Maker](../How-To/business-continuity-plan.md)
