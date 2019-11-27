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

Language Understanding (LUIS) is een API-service in de Cloud waarmee aangepaste informatie over machine learning wordt toegepast op tekst in natuurlijke taal om algemene betekenissen te voors pellen en relevante, gedetailleerde gegevens uit te pakken. 

Wanneer bijvoorbeeld een client toepassing de tekst verzendt, wordt `find me a wireless keyboard for $30`LUIS reageert met het volgende JSON-object. 

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
In het bovenstaande voor beeld is de _**intentie**_ of de algemene betekenis van de zin dat de gebruiker probeert een item te vinden. De gedetailleerde informatie over de LUIS-uittreksels worden _**entiteiten**_ genoemd. In dit geval zijn de entiteiten de naam van het item dat de gebruiker zoekt en de hoeveelheid geld die ze willen best Eden.

Client toepassingen gebruiken de door LUIS geretourneerde JSON, de _intentie_ (categorie) en _entiteiten_ (geëxtraheerde gedetailleerde informatie) om acties in de client toepassing te kunnen uitvoeren. Een client toepassing voor LUIS is vaak een conversatie toepassing die communiceert met een gebruiker in natuurlijke taal om een taak te volt ooien. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen. 

![Conceptuele afbeelding van 3 client toepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptuele afbeelding van 3 client toepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Voor beeld van het gebruik van LUIS in een chat-bot

<a name="Accessing-LUIS"></a>

Een client toepassing verzendt uitingen (tekst) naar de gepubliceerde LUIS natuurlijke taal verwerkings eindpunt- [API][endpoint-apis] en ontvangt de resultaten als JSON-reacties. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptuele installatie kopie van LUIS met behulp van de chat-bot om gebruikers tekst te voors pellen met natuurlijk language Standing (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptuele afbeelding van LUIS werken met de chat-bot om gebruikers tekst te voors pellen met natuurlijk taal begrip (NLP")

|Stap|Actie|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|LUIS past geleerde taal modellen van de gebruiker toe aan de niet-gestructureerde invoer tekst en retourneert een JSON-indelings antwoord met een hoofd intentie `HRContact`. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Het kan ook gegevens extra heren, zoals de entiteit _type contact_ .|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen een beslissings structuur bevatten in de bot en aanroepen naar andere services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Uw LUIS-app bevat apparaatspecifieke, natuurlijke taal modellen die met elkaar samen werken. U kunt de LUIS-app starten met een of meer vooraf ontwikkelde modellen, uw eigen model bouwen of vooraf ontwikkelde modellen met uw eigen aangepaste gegevens overvloeien.

* **Vooraf gebouwd model** LUIS heeft veel vooraf gemaakte domeinen met intent-en entiteits modellen die samen werken om veelvoorkomende gebruiks scenario's uit te voeren. Deze domeinen bevatten gelabelde uitingen die kunnen worden geïnspecteerd en bewerkt, zodat u ze kunt aanpassen. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken. Daarnaast zijn er vooraf gemaakte entiteiten, zoals valuta en nummer, die u onafhankelijk van de vooraf gemaakte domeinen kunt gebruiken.

* **Aangepast model** LUIS biedt verschillende manieren om uw eigen aangepaste modellen te bouwen, waaronder intenties en entiteiten. Entiteiten zijn onder andere door machines geleerde entiteiten, vergelijkings eenheden voor patronen en een combi natie van door de machine geleerde en patroon vergelijking.

## <a name="build-the-luis-app"></a>De LUIS-app bouwen
Bouw de app met behulp van de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087) -api's of met de [Luis-Portal](https://www.luis.ai).

De LUIS-app begint met categorieën invoer tekst met de naam **[intents](luis-concept-intent.md)** . Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elk utterance kan gegevens leveren die moeten worden geëxtraheerd. 

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|13:00 uur, Bob|

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

## <a name="iterative-development-lifecycle"></a>Cyclus voor iteratieve ontwikkeling
LUIS biedt hulpprogram ma's, versie beheer en samen werking met andere LUIS-auteurs om te integreren in de [levens cyclus](luis-concept-app-iteration.md)van de volledige iteratie. 

## <a name="implementing-luis"></a>LUIS implementeren
Language Understanding (LUIS REST API) kan worden gebruikt in combi natie met elk product, elke service of elk Framework met een HTTP-aanvraag. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Maakt gebruik van [bot Framework][bot-framework] versie [4. x](https://github.com/Microsoft/botbuilder-dotnet) voor een complete bot-ervaring.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [Luis cli](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt ontwerpen en voor spellingen met als een zelfstandig opdracht regel programma of als import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.
* [Bot Framework-Composer](https://github.com/microsoft/BotFramework-Composer) : een geïntegreerd hulp programma voor ontwikkel aars en meerdere disciplinaire teams om bots en gespreks ervaringen te bouwen met het micro soft bot Framework

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* Met [QnA Maker][qnamaker] kunt u verschillende typen tekst combi neren in een vraag-en antwoord Knowledge Base.
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* [Bot-Framework-bot-voor beelden](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Volgende stappen

* [Nieuwe functies](whats-new.md)
* Een nieuwe LUIS-app ontwerpen met een [vooraf gebouwd](luis-get-started-create-app.md) of [aangepast](luis-quickstart-intents-only.md) domein
* [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app. 
* [Bronnen voor ontwikkel aars](developer-reference-resource.md) voor Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
