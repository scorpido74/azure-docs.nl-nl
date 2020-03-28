---
title: Wat is Language Understanding (LUIS)?
description: Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: 98fb936422f8e23e728efea19fa2cd75d90fac57
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053358"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.

Een clienttoepassing voor LUIS is een gesprekstoepassing waarin een gebruiker in natuurlijke taal communiceert om een taak te voltooien. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.

![Conceptueel beeld van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptueel beeld van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>LUIS gebruiken in een chatbot

<a name="Accessing-LUIS"></a>

Als de LUIS-app is gepubliceerd, worden uitingen (tekst) door een clienttoepassing verzonden naar de [API][endpoint-apis] van het eindpunt voor verwerking van natuurlijke taal in LUIS en worden de resultaten ontvangen als JSON-antwoorden. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptuele beelden van LUIS die met Chatbot werkt om gebruikerstekst te voorspellen met natuurlijke taalbegrip (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptuele beelden van LUIS die met Bot chat werkt om gebruikerstekst met natuurlijk taalbegrip te voorspellen (NLP")

|Stap|Actie|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|LUIS stelt u in staat om uw aangepaste taalmodellen te maken om intelligentie toe te voegen aan uw toepassing. Machine-aangeleerde taalmodellen nemen de ongestructureerde invoertekst van de gebruiker en retourneert een JSON-opgemaakte reactie, met een topintentie, `HRContact`. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Het kan ook gegevens extraheren, zoals de entiteit _Contacttype._|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen beslissingsstructuur in de bot-frameworkcode en oproepen naar andere services omvatten. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Uw LUIS-app bevat een domeinspecifiek natuurlijk taalmodel. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepast model** LUIS biedt u verschillende manieren om uw eigen aangepaste modellen te identificeren, waaronder intents en entiteiten. Entiteiten omvatten door machines geleerde entiteiten, specifieke of letterlijke entiteiten en een combinatie van machine-geleerd en letterlijk.

## <a name="build-the-luis-model"></a>Het LUIS-model opbouwen
Bouw het model [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) met de ontwerp-API's of met de [LUIS-portal](https://www.luis.ai).

Om te beginnen definieert u categorieën gebruikersintenties, ook wel kortweg **[intenties](luis-concept-intent.md)** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elke utterance kan gegevens bevatten die moeten worden geëxtraheerd.

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|13:00 uur, Bob|

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

Nadat uw app is getraind en gepubliceerd naar het eindpunt, [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) stuurt de clienttoepassing uitingen naar de voorspellingseindpunt-API. De API past de app toe op de utterance voor analyse en reageert met de voorspellingsresultaten in een JSON-indeling.

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Het kan ook gegevens extraheren, zoals de volgende entiteit **Contacttype** en het algehele sentiment.

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

Nadat uw LUIS-app is gepubliceerd en echte uitingen van gebruikers heeft ontvangen, biedt LUIS [actief leren](luis-concept-review-endpoint-utterances.md) van eindpuntuitingen om de nauwkeurigheid van de voorspelling te verbeteren.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling
LUIS biedt tools, versiebeheer en samenwerking met andere LUIS-auteurs om te integreren in de volledige [ontwikkelingslevenscyclus.](luis-concept-app-iteration.md)

## <a name="implementing-luis"></a>LUIS implementeren
Language Understanding (LUIS), als REST API, kan worden gebruikt met elk product, service of framework met een HTTP-aanvraag. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Gebruikt [Bot Framework][bot-framework] versie [4.x](https://github.com/Microsoft/botbuilder-dotnet) voor een complete botervaring.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [LUIS CLI LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt authoring en voorspelling met als een stand-alone command-line tool of als import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een command-line tool die helpt bij het beheren van taalmodellen voor uw bot.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - een geïntegreerde ontwikkeltool voor ontwikkelaars en multidisciplinaire teams om bots en conversationele ervaringen te bouwen met het Microsoft Bot Framework

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* [QnA Maker][qnamaker] - hiermee kunnen verschillende soorten tekst worden gecombineerd in een kennisdatabase met vragen en antwoorden.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst.
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* [Bot framework - Bot monsters](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw](whats-new.md)
* Een nieuwe LUIS-app met een [vooraf gedefinieerd](luis-get-started-create-app.md) of [aangepast](luis-quickstart-intents-only.md) domein maken.
* [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app.
* [Bronnen voor ontwikkelaars](developer-reference-resource.md) voor LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
