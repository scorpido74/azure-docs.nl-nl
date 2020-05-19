---
title: Wat is Language Understanding (LUIS)?
description: Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: d09aaabeec1d22132843ba98472e1cd89ba95815
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592811"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.

Een clienttoepassing voor LUIS is een gesprekstoepassing waarin een gebruiker in natuurlijke taal communiceert om een taak te voltooien. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.

![Conceptuele afbeelding van 3 client toepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptuele afbeelding van 3 client toepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>LUIS gebruiken in een chatbot

<a name="Accessing-LUIS"></a>

Als de LUIS-app is gepubliceerd, worden uitingen (tekst) door een clienttoepassing verzonden naar de [API][endpoint-apis] van het eindpunt voor verwerking van natuurlijke taal in LUIS en worden de resultaten ontvangen als JSON-antwoorden. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptuele installatie kopie van LUIS met behulp van de chat-bot om gebruikers tekst te voors pellen met natuurlijk language Standing (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptuele afbeelding van LUIS werken met de chat-bot om gebruikers tekst te voors pellen met natuurlijk taal begrip (NLP")

|Stap|Actie|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|Met LUIS kunt u uw aangepaste taal modellen maken om intelligentie toe te voegen aan uw toepassing. Door de computer geleerde taal modellen nemen de niet-gestructureerde invoer tekst van de gebruiker en retourneert een JSON-indelings antwoord met een hoofd intentie `HRContact` . Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Het kan ook gegevens extra heren, zoals de entiteit _type contact_ .|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen beslissings structuur bevatten in de bot-Framework code en aanroepen naar andere services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Uw LUIS-app bevat een domein-specifiek natuurlijke taal model. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepast model** LUIS biedt verschillende manieren om uw eigen aangepaste modellen te identificeren, inclusief intenties en entiteiten. Entiteiten zijn onder andere door machines geleerde entiteiten, specifieke of letterlijke entiteiten en een combi natie van door de machine geleerd en letterlijke waarde.

## <a name="build-the-luis-model"></a>Het LUIS-model opbouwen
Bouw het model met behulp van de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087) -api's of met de [Luis-Portal](https://www.luis.ai).

Om te beginnen definieert u categorieën gebruikersintenties, ook wel kortweg **[intenties](luis-concept-intent.md)** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elk utterance kan gegevens leveren die moeten worden geëxtraheerd.

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13:00 uur, Bob|

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

Nadat uw app is getraind en gepubliceerd naar het eind punt, verzendt de client toepassing uitingen naar de API voor prediction- [eind punten](https://go.microsoft.com/fwlink/?linkid=2092356) . De API past de app toe op de utterance voor analyse en reageert met de Voorspellings resultaten in een JSON-indeling.

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Het kan ook gegevens extra heren, zoals de volgende entiteit van het **type contact** en de algemene sentiment.

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

Nadat de LUIS-app is gepubliceerd en echte gebruikers uitingen ontvangt, biedt LUIS [actief leren](luis-concept-review-endpoint-utterances.md) van eind punt uitingen om de nauw keurigheid van de voor spelling te verbeteren.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling
LUIS biedt hulpprogram ma's, versie beheer en samen werking met andere LUIS-auteurs die kunnen worden geïntegreerd in de [levens cyclus](luis-concept-app-iteration.md)van de volledige ontwikkeling.

## <a name="implementing-luis"></a>LUIS implementeren
Language Understanding (LUIS REST API) kan worden gebruikt in combi natie met elk product, elke service of elk Framework met een HTTP-aanvraag. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Maakt gebruik van [bot Framework][bot-framework] versie [4. x](https://github.com/Microsoft/botbuilder-dotnet) voor een complete bot-ervaring.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [Luis cli](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt ontwerpen en voor spellingen met als een zelfstandig opdracht regel programma of als import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdracht regel programma waarmee u taal modellen voor uw bot kunt beheren.
* [Bot Framework-Composer](https://github.com/microsoft/BotFramework-Composer) : een geïntegreerd hulp programma voor ontwikkel aars en meerdere disciplinaire teams om bots en gespreks ervaringen te bouwen met het micro soft bot Framework

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* [QnA Maker][qnamaker] - hiermee kunnen verschillende soorten tekst worden gecombineerd in een kennisdatabase met vragen en antwoorden.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst.
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* [Bot-Framework-bot-voor beelden](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw](whats-new.md)
* Een nieuwe LUIS-app met een [vooraf gedefinieerd](luis-get-started-create-app.md) of [aangepast](luis-quickstart-intents-only.md) domein maken.
* [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app.
* [Bronnen voor ontwikkel aars](developer-reference-resource.md) voor Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
