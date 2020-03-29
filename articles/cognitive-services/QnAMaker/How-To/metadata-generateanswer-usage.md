---
title: Metagegevens met GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Met QnA Maker u metagegevens, in de vorm van sleutel-/waardeparen, toevoegen aan uw vraag-/antwoordsets. U resultaten filteren op gebruikersquery's en aanvullende informatie opslaan die kan worden gebruikt in vervolggesprekken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 6a8cbabfd4e47c50d2c2e6f4a23c50a931e645a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220642"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Krijg een antwoord met de GenerateAnswer API en metadata

Als u het voorspelde antwoord op de vraag van een gebruiker wilt krijgen, gebruikt u de GenerateAnswer API. Wanneer u een kennisbank publiceert, u informatie zien over het gebruik van deze API op de **pagina Publiceren.** U de API ook configureren om antwoorden te filteren op basis van metagegevenstags en de kennisbasis vanaf het eindpunt te testen met de parameter testquerytekenreeks.

Met QnA Maker u metagegevens, in de vorm van sleutel- en waardeparen, toevoegen aan uw reeks vragen en antwoorden. U deze informatie vervolgens gebruiken om resultaten te filteren op gebruikersquery's en om aanvullende informatie op te slaan die kan worden gebruikt in vervolggesprekken. Zie [Kennisbank](../Concepts/knowledge-base.md)voor meer informatie.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Vragen en antwoorden opslaan met een QnA-entiteit

Het is belangrijk om te begrijpen hoe QnA Maker de vraag- en antwoordgegevens opslaat. In de volgende afbeelding ziet u een QnA-entiteit:

![Illustratie van een QnA-entiteit](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Elke QnA-entiteit heeft een unieke en permanente ID. U de id gebruiken om updates uit te voeren voor een bepaalde QnA-entiteit.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Antwoordvoorspellingen genereren met de GenerateAnswer API

U gebruikt de [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) in uw bot of toepassing om uw kennisbank op te vragen met een gebruikersvraag, om de beste match te halen uit de vraag- en antwoordsets.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publiceren om generateanswer-eindpunt te krijgen

Nadat u uw kennisbank hebt gepubliceerd, hetzij vanuit de [QnA Maker-portal](https://www.qnamaker.ai), hetzij met behulp van de [API,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)u de details van uw GenerateAnswer-eindpunt ophalen.

Ga als eerste voor uw eindpunt:
1. Log hier [https://www.qnamaker.ai](https://www.qnamaker.ai)in
1. Selecteer in **Mijn kennisbanken** **Code bekijken** voor uw kennisbank.
    ![Schermafbeelding van mijn kennisbanken](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Haal uw eindpuntgegevens van GenerateAnswer op.

    ![Schermafbeelding van eindpuntdetails](../media/qnamaker-how-to-metadata-usage/view-code.png)

U ook uw eindpuntgegevens ophalen via het tabblad **Instellingen** van uw kennisbank.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuratie van antwoordaanvragen genereren

U belt GenerateAnswer met een HTTP POST-verzoek. Zie de [quickstarts](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)voor voorbeeldcode die laat zien hoe u GenerateAnswer aanroept.

De POST-aanvraag gebruikt:

* Vereiste [URI-parameters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Vereiste headereigenschap, `Authorization`, voor veiligheid
* Vereiste [lichaamseigenschappen.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

De URL GenerateAnswer heeft de volgende indeling:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Vergeet niet de eigenschap `Authorization` HTTP-header in `EndpointKey` te stellen met een waarde van de tekenreeks met een naloopruimte en vervolgens de eindpunttoets op de pagina **Instellingen.**

Een voorbeeld JSON lichaam ziet eruit als:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Meer informatie over [rankerType](../concepts/best-practices.md#choosing-ranker-type).

De vorige JSON vroeg alleen antwoorden aan die 30% of hoger zijn dan de drempelscore.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Eigenschappen van antwoord beantwoorden genereren

Het [antwoord](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) is een JSON-object met alle informatie die u nodig hebt om het antwoord weer te geven en de volgende beurt in het gesprek, indien beschikbaar.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

De vorige JSON reageerde met een antwoord met een score van 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>QnA Maker gebruiken met een bot in C #

Het bot framework biedt toegang tot de eigenschappen van de QnA Maker met de [getAnswer API:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

De vorige JSON vroeg alleen antwoorden aan die 30% of hoger zijn dan de drempelscore.

De ondersteuningsbot heeft [een voorbeeld](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) met deze code.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>QnA Maker gebruiken met een bot in Node.js

Het bot framework biedt toegang tot de eigenschappen van de QnA Maker met de [getAnswer API:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

De vorige JSON vroeg alleen antwoorden aan die 30% of hoger zijn dan de drempelscore.

De ondersteuningsbot heeft [een voorbeeld](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) met deze code.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Metagegevens gebruiken om antwoorden te filteren op aangepaste metagegevenstags

Als u metagegevens toevoegt, u de antwoorden filteren op deze metagegevenstags. Voeg de kolom metagegevens toe in het menu **Weergaveopties.** Voeg metadata toe aan je **+** kennisbank door het metagegevenspictogram te selecteren om een metagegevenspaar toe te voegen. Dit paar bestaat uit één sleutel en één waarde.

![Schermafbeelding van het toevoegen van metagegevens](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Resultaten filteren met strictFilters voor metagegevenstags

Overweeg de gebruikersvraag "Wanneer sluit dit hotel?", waar de bedoeling wordt geïmpliceerd voor het restaurant "Paradise."

Omdat resultaten alleen nodig zijn voor het restaurant Paradise, u een filter instellen in de GenerateAnswer-oproep op de metagegevens Restaurant Name. In het volgende voorbeeld ziet u het volgende:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Gebruik vraag- en antwoordresultaten om de gesprekscontext te behouden

Het antwoord op de GenerateAnswer bevat de bijbehorende metadata-informatie van de overeenkomende vraag- en antwoordset. U deze informatie in uw clienttoepassing gebruiken om de context van het vorige gesprek op te slaan voor gebruik in latere gesprekken.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Alleen vragen koppelen, per tekst

QnA Maker doorzoekt standaard vragen en antwoorden. Als u alleen door vragen wilt zoeken om `RankerType=QuestionOnly` een antwoord te genereren, gebruikt u de post-hoofd van het verzoek generateanswer.

U zoeken via de `isTest=false`gepubliceerde kb, met `isTest=true`behulp van, of in de test kb met behulp van.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Veelvoorkomende HTTP-fouten

|Code|Uitleg|
|:--|--|
|2xx|Geslaagd|
|400|De parameters van de aanvraag zijn onjuist, wat betekent dat de vereiste parameters ontbreken, misvormd of te groot zijn|
|400|Het lichaam van het verzoek is onjuist, wat betekent dat de JSON ontbreekt, misvormd of te groot is|
|401|Ongeldige sleutel|
|403|Verboden - u beschikt niet over de juiste machtigingen|
|404|KB bestaat niet|
|410|Deze API is afgeschaft en is niet meer beschikbaar|

## <a name="next-steps"></a>Volgende stappen

De **publicatiepagina** biedt ook informatie om [een antwoord](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) te genereren met Postbode of cURL.

> [!div class="nextstepaction"]
> [Maak een knowledge base bot](../tutorials/integrate-qnamaker-luis.md)
