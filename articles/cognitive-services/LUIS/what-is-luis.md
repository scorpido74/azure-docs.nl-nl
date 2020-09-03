---
title: Wat is Language Understanding (LUIS)?
description: 'Language Understanding (LUIS): een API-cloudservice waarbij machine learning wordt toegepast op natuurlijke spreektaal om betekenissen te voorspellen en informatie te extraheren.'
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, natural language understanding, nlu, ai conversation, conversational ai, ai chatbot, chatbot maker, LUIS, nlp ai, luis ai, azure luis, understanding natural language
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 98d801f6a34feb40d56215f8b6257a68ec628e10
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320049"
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

## <a name="natural-language-understanding-nlu"></a>NLU (natuurlijk taalbegrip)

[LUIS biedt kunstmatige intelligentie (AI)](artificial-intelligence.md) in de vorm van NLU, een subset van NLP (natuurlijke taalverwerking).

Uw LUIS-app bevat een domein-specifiek model voor natuurlijke taal. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepast model** LUIS biedt u een aantal manieren om uw eigen aangepaste modellen e identificeren, inclusief intenties en entiteiten. Entiteiten zijn machine learning-entiteiten, specifieke of letterlijke entiteiten, en een combinatie van machine learning en letterlijk.

Leer meer over [NLP](artificial-intelligence.md) en het specifieke gebied van NLU voor LUIS.

## <a name="step-1-design-and-build-your-model"></a>Stap 1: Uw model ontwerpen en bouwen

Ontwerp uw model met categorieën gebruikersintenties, ook wel **[intenties](luis-concept-intent.md)** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elke uiting kan verschillende gegevens bevatten. Deze moeten worden geëxtraheerd met [machine learning-entiteiten](luis-concept-entity-types.md#effective-machine-learned-entities).

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13:00 uur, Bob|

U bouwt het model op met de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087)-API's of met de [**LUIS-portal**](https://www.luis.ai), of beide. Leer meer over bouwen met behulp van de [portal](get-started-portal-build-app.md) en de [SDK-clientbibliotheken](azure-sdk-quickstart.md).

## <a name="step-2-get-the-query-prediction"></a>Stap 2: De queryvoorspelling ophalen

Nadat het model van uw app is getraind en gepubliceerd naar het eindpunt, worden uitingen door een clienttoepassing (zoals een chatbot) verzonden naar de voorspellende [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356)-API. De API past het model toe op de uiting om deze te analyseren en reageert met de voorspellingsresultaten in JSON-indeling.

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens kunnen worden geëxtraheerd, zoals de volgende **Contact Type**-entiteit en het algemene gevoel.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Stap 3: Voorspellingen van het model verbeteren

Nadat uw LUIS-app is gepubliceerd en uitingen van echte gebruikers ontvangt, zorgt LUIS voor [actief leren](luis-concept-review-endpoint-utterances.md) van eindpuntuitingen om de nauwkeurigheid van de voorspellingen te verbeteren. Beoordeel deze suggesties als onderdeel van uw gewone onderhoud in de levenscyclus van de ontwikkeling.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Levenscyclus van ontwikkeling en hulpprogramma's
LUIS biedt hulpprogramma's, versiebeheer en samenwerking met andere LUIS-auteurs voor integratie in de volledige [ontwikkelingscyclus](luis-concept-app-iteration.md).

Language Understanding (LUIS) kan als REST-API worden geïntegreerd met elk product, elke service en elk framework met een HTTP-aanvraag. LUIS biedt voor diverse belangrijke programmeertalen ook clientbibliotheken (SDK's). Lees meer over de aangeboden [resources voor ontwikkelaars](developer-reference-resource.md).

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt creatie en voorspelling met een zelfstandig opdrachtregelhulpprogramma of een import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.

## <a name="integrate-with-a-bot"></a>Integratie met een bot

Gebruik de [Azure Bot-service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) met het [Microsoft Bot Framework](https://dev.botframework.com/) om een chatbot te bouwen en te implementeren. Gebruik het grafische interfaceprogramma [Composer](https://docs.microsoft.com/composer/) of [actieve voorbeelden van bots](https://github.com/microsoft/BotBuilder-Samples) die voor de belangrijkste botscenario's zijn ontworpen, om te ontwerpen en te ontwikkelen.

## <a name="integrate-with-other-cognitive-services"></a>Integreren met andere Cognitive Services

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* Met [QnA Maker][qnamaker] kunnen verschillende soorten tekst worden gecombineerd in een kennisdatabase met vragen en antwoorden.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst.

LUIS biedt functionaliteit van Text Analytics als onderdeel van uw bestaande LUIS-resources. Deze functionaliteit biedt onder andere [stemmingsanalyse](luis-how-to-publish-app.md#configuring-publish-settings) en [extractie van sleutelwoorden](luis-reference-prebuilt-keyphrase.md) via de vooraf gebouwde keyPhrase-entiteit.

## <a name="learn-with-the-quickstarts"></a>Leren met behulp van quickstarts

Gebruik de praktische quickstarts in de [portal](get-started-portal-build-app.md) en de [SDK-clientbibliotheken](azure-sdk-quickstart.md) om meer te leren over LUIS.


## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw](whats-new.md) bij de service en de documentatie
* [Plan uw app](luis-how-plan-your-app.md) met [intenties](luis-concept-intent.md) en [entiteiten](luis-concept-entity-types.md).
* [Voer een query uit op het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md).
* [Resources voor ontwikkelaars](developer-reference-resource.md) voor LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
