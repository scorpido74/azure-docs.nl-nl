---
title: Integratie van LUIS en QnAMaker-bot
titleSuffix: Azure Cognitive Services
description: Als uw QnA Maker Knowledge Base groter wordt, is het lastig om deze te onderhouden als één monolithische-set. Splits de Knowledge Base in kleinere, logische segmenten.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402708"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Een bot met QnA Maker en LUIS gebruiken om uw Knowledge Base te distribueren
Als uw QnA Maker Knowledge Base groter wordt, is het lastig om deze te onderhouden als één monolithische-set. Splits de Knowledge Base in kleinere, logische segmenten.

Hoewel het eenvoudig is om meerdere kennis grondslagen te maken in QnA Maker, hebt u enige logica nodig om de binnenkomende vraag te routeren naar de juiste Knowledge Base. U kunt dit doen met behulp van LUIS.

In dit artikel wordt gebruikgemaakt van de bot Framework v3 SDK. Zie [meerdere Luis-en QnA-modellen gebruiken](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)als u geïnteresseerd bent in de bot Framework v4 SDK-versie van deze informatie.

## <a name="architecture"></a>Architectuur

![Afbeelding van de architectuur van QnA Maker met Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

In de voor gaande afbeelding ziet u dat QnA Maker eerst de bedoeling van de inkomende vraag haalt uit een LUIS-model. QnA Maker gebruikt dat doel om de vraag naar de juiste QnA Maker Knowledge Base te sturen.

## <a name="create-a-luis-app"></a>Een LUIS-app maken

1. Meld u aan bij de [Luis](https://www.luis.ai/) -Portal.
1. [Een app maken](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Voeg een intentie toe](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) voor elke QnA Maker Knowledge Base. Het voor beeld uitingen moet overeenkomen met de vragen in de QnA Maker Knowledge bases.
1. [Train de Luis-app](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) en [Publiceer de Luis-app](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Noteer de LUIS-App-ID, de LUIS-eindpunt sleutel en de [aangepaste domein naam](../../cognitive-services-custom-subdomains.md)in de sectie **beheren** . U hebt deze waarden later nodig.

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker Knowledge bases maken

1. Meld u aan bij [QnA Maker](https://qnamaker.ai).
1. [Maak](https://www.qnamaker.ai/Create) een Knowledge Base voor elke intentie in de Luis-app.
1. De kennis slagen testen en publiceren. Wanneer u elk abonnement publiceert, noteert u de ID, resource naam (het aangepaste subdomein vóór _. azurewebsites.net/qnamaker_) en de autorisatie-eindpunt sleutel. U hebt deze waarden later nodig.

    In dit artikel wordt ervan uitgegaan dat de kennis bases zijn gemaakt in hetzelfde Azure QnA Maker-abonnement.

    ![Scherm opname van QnA Maker HTTP-aanvraag](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web-app-bot

1. [Maak een eenvoudige bot met Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), die automatisch een Luis-app bevat. Selecteer de programmeer taal C#.

1. Nadat u de web-app-bot hebt gemaakt, selecteert u in de Azure Portal de bot web-app.
1. Selecteer **Toepassings instellingen**in de navigatie van de web-app-service. Schuif vervolgens omlaag naar de sectie **Toepassings instellingen** van de beschik bare instellingen.
1. Wijzig de **LuisAppId** in de waarde van de Luis-app die u in de voor gaande sectie hebt gemaakt. Selecteer vervolgens **Opslaan**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>De code in het BasicLuisDialog.cs-bestand wijzigen
1. Selecteer in de sectie **bot Management** van de navigatie balk van de web-app in de Azure Portal **Build**.
2. Selecteer **online code-editor openen**. Er wordt een nieuw browser tabblad geopend met de online bewerkings omgeving.
3. Selecteer in de sectie **WWWROOT** de map **dialoog vensters** en open vervolgens **BasicLuisDialog.cs**.
4. Afhankelijkheden toevoegen aan de bovenkant van het **BasicLuisDialog.cs** -bestand:

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

5. Voeg de volgende klassen toe om de QnA Maker-reactie te deserialiseren:

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


6. Voeg de volgende klasse toe om een HTTP-aanvraag naar de QnA Maker-service te maken. U ziet dat de waarde van de **autorisatie** -header het `EndpointKey`woord, met een spatie achter het woord bevat. Het JSON-resultaat wordt gedeserialiseerd in de voor gaande klassen en het eerste antwoord wordt geretourneerd.

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


7. Wijzig de `BasicLuisDialog` klasse. Elke LUIS-intentie moet een methode hebben die is voorzien van **LuisIntent**. De para meter voor de decoratie is de werkelijke naam van de LUIS. De naam van de methode die wordt gedecoreerd, _moet_ de Luis intentie naam zijn voor lees baarheid en onderhoud, maar hoeft niet hetzelfde te zijn tijdens het ontwerp of de runtime.

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


## <a name="build-the-bot"></a>De bot bouwen
1. Klik in de code-editor met de rechter muisknop op **Build. cmd**en selecteer **uitvoeren vanuit console**.

    ![Scherm opname van uitvoeren vanaf console optie in de code-editor](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. De code weergave wordt vervangen door een Terminal venster waarin de voortgang en resultaten van de build worden weer gegeven.

    ![Scherm opname van de console-build](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>De bot testen
Selecteer in de Azure Portal **test in Web Chat** om de bot te testen. Typ berichten uit verschillende doel stellingen om het antwoord van de bijbehorende kennis database op te halen.

![Scherm afbeelding van de Web-Chat test](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Integreer uw Knowledge Base met een agent in Power Virtual agents](integrate-with-power-virtual-assistant-fallback-topic.md)
