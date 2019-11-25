---
title: Wat is Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456565"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

Language Understanding (LUIS) is a cloud-based API service that applies custom machine-learning intelligence to natural language text to predict overall meaning, and pull out relevant, detailed information. 

For example, when a client application sends the text, `find me a wireless keyboard for $30`, LUIS responds with the following JSON object. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
In the example above, the _**intent**_ , or overall meaning of the phrase is that the user is trying to find an item. The detailed pieces of information that LUIS extracts are called _**entities**_ . In this case, the entities are the name of the item the user is looking for and the amount of money they want to spend.

Client applications use LUIS's returned JSON, the _intent_ (category), and _entities_ (extracted detailed information), to drive actions in the client application. A client application for LUIS is often a conversational application that communicates with a user in natural language to complete a task. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen. 

![Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Example use LUIS in a chat bot

<a name="Accessing-LUIS"></a>

A client application sends utterances (text) to the published LUIS natural language processing endpoint [API][endpoint-apis] and receives the results as JSON responses. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP")

|Stap|Bewerking|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|LUIS applies machine learned language models to the user's unstructured input text and returns a JSON-formatted response, with a top intent, `HRContact`. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. It can also extract data such as the _Contact Type_ entity.|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. These decisions can include a decision tree in the bot and calls to other services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Your LUIS app contains domain-specific natural language models, which work together. You can start the LUIS app with one or more prebuilt models, build your own model, or blend prebuilt models with your own custom information.

* **Prebuilt model** LUIS has many prebuilt domains that include intent and entity models that work together to complete common usage scenarios. These domains include labeled utterances that can be inspected and edited, allowing you to customize them. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken. In addition, there are prebuilt entities such as currency and number that you can use independently from the prebuilt domains.

* **Custom model** LUIS gives you several ways to build your own custom models including intents, and entities. Entities include machine-learned entities, pattern matching entities, and a combination of machine-learned and pattern matching.

## <a name="build-the-luis-app"></a>Build the LUIS app
Build the app with the [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) APIs or with the [LUIS portal](https://www.luis.ai).

The LUIS app begins with categories of input text called **[intents](luis-concept-intent.md)** . Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Each utterance can provide data that needs to be extracted. 

|Voorbeeld van een uiting van een gebruiker|Intentie|Extracted data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|13:00 uur, Bob|

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

After your app is trained and published to the endpoint, the client application sends utterances to the prediction [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) API. The API applies the app to the utterance for analysis and responds with the prediction results in a JSON format.  

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. It can also extract data such as the following **Contact Type** entity and overall sentiment. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Voorspellingen van het model verbeteren

After your LUIS app is published and receives real user utterances, LUIS provides [active learning](luis-concept-review-endpoint-utterances.md) of endpoint utterances to improve prediction accuracy. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>Iterative development lifecycle
LUIS provides tools, versioning, and collaboration with other LUIS authors to integrate into the full iterative [development life cycle](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>LUIS implementeren
Language Understanding (LUIS), as a REST API, can be used with any product, service, or framework with an HTTP request. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Uses [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) for a complete bot experience.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) The NPM package provides authoring and prediction with as either a stand-alone command line tool or as import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - an integrated development tool for developers and multi-disciplinary teams to build bots and conversational experiences with the Microsoft Bot Framework

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* [QnA Maker][qnamaker] allows several types of text to combine into a question and answer knowledge base.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* [Bot framework - Bot samples](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Volgende stappen

* [Nieuwe functies](whats-new.md)
* Author a new LUIS app with a [prebuilt](luis-get-started-create-app.md) or [custom](luis-quickstart-intents-only.md) domain
* [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app. 
* [Developer resources](developer-reference-resource.md) for LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
