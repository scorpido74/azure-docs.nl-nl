---
title: Wat is Language Understanding (LUIS)?
description: Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: 231a6580a6776b82173865744e9e8757c2fa08f1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538034"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren.

Een clienttoepassing voor LUIS is een gesprekstoepassing waarin een gebruiker in natuurlijke taal communiceert om een taak te voltooien. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.

![Conceptuele afbeelding van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptuele afbeelding van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>LUIS gebruiken in een chatbot

<a name="Accessing-LUIS"></a>

Als de LUIS-app is gepubliceerd, worden uitingen (tekst) door een clienttoepassing verzonden naar de [API][endpoint-apis] van het eindpunt voor verwerking van natuurlijke taal in LUIS en worden de resultaten ontvangen als JSON-antwoorden. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptuele afbeelding van LUIS die met Chat bot werkt om gebruikerstekst te voorspellen met begrip van natuurlijk taalgebruik (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptuele afbeelding van LUIS die met Chat bot werkt om gebruikerstekst te voorspellen met begrip van natuurlijk taalgebruik (NLP)")

|Stap|Bewerking|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|Met LUIS kunt u uw eigen taalmodellen maken om intelligentie toe te voegen aan uw toepassing. Taalmodellen op basis van machine learning nemen de ongestructureerde invoertekst en retourneren een antwoord in JSON-indeling met een top-intentie, `HRContact`. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens, zoals de _Contact Type_, kunnen worden geëxtraheerd.|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen zijn gebaseerd op een beslissingsstructuur in de code van het botframework en aanroepen naar andere services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Uw LUIS-app bevat een domein-specifiek model voor natuurlijke taal. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepast model** LUIS biedt u een aantal manieren om uw eigen aangepaste modellen e identificeren, inclusief intenties en entiteiten. Entiteiten zijn machine learning-entiteiten, specifieke of letterlijke entiteiten, en een combinatie van machine learning en letterlijk.

## <a name="build-the-luis-model"></a>Het LUIS-model opbouwen
U bouwt het model op met de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087)-API's of met de [LUIS-portal](https://www.luis.ai).

Om te beginnen definieert u categorieën gebruikersintenties, ook wel kortweg **[intenties](luis-concept-intent.md)** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elke uiting kan gegevens bevatten die moeten worden geëxtraheerd.

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13:00 uur, Bob|

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

Nadat uw app is getraind en gepubliceerd naar het eindpunt, worden uitingen door de clienttoepassing verzonden naar de voorspellende [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356)-API. De API past het model toe op de uiting om deze te analyseren en reageert met de voorspellingsresultaten in JSON-indeling.

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens kunnen worden geëxtraheerd, zoals de volgende **Contact Type**-entiteit en het algemene gevoel.

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

Nadat uw LUIS-app is gepubliceerd en uitingen van echte gebruikers ontvangt, zorgt LUIS voor [actief leren](luis-concept-review-endpoint-utterances.md) van eindpuntuitingen om de nauwkeurigheid van de voorspellingen te verbeteren.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling
LUIS biedt hulpprogramma's, versiebeheer en samenwerking met andere LUIS-auteurs voor integratie in de volledige [ontwikkelingscyclus](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>LUIS implementeren
Language Understanding (LUIS) kan als REST-API worden geïntegreerd met elk product, elke service en elk framework met een HTTP-aanvraag. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Deze maakt gebruik van [Bot Framework][bot-framework] versie [4.x](https://github.com/Microsoft/botbuilder-dotnet) voor een complete bot-ervaring.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt creatie en voorspelling met een zelfstandig opdrachtregelhulpprogramma of een import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.
* [Bot Framework-Composer](https://github.com/microsoft/BotFramework-Composer): een geïntegreerd hulpprogramma voor ontwikkelaars en multi-disciplinaire teams om bots en gesprekservaringen te bouwen met het Microsoft Bot Framework

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* Met [QnA Maker][qnamaker] kunnen verschillende soorten tekst worden gecombineerd in een kennisdatabase met vragen en antwoorden.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst.
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* [Bot-framework: bot-voorbeelden](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Volgende stappen

* [Nieuwe functies](whats-new.md)
* Een nieuwe LUIS-app met een [vooraf gedefinieerd](luis-get-started-create-app.md) of [aangepast](luis-quickstart-intents-only.md) domein maken.
* [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app.
* [Resources voor ontwikkelaars](developer-reference-resource.md) voor LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
