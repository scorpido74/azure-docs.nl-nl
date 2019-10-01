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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 3d4251613ac2a00ddc56d5e573b49ced01adf61c
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703096"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

Language Understanding (LUIS) is een API-cloudservice die aangepaste machine-learning-intelligence toepast op tekst in natuurlijke spreektaal van een gebruiker om daar de algemene betekenis en relevante detailinformatie uit te destilleren. 

Een clienttoepassing voor LUIS is een gesprekstoepassing waarin een gebruiker in natuurlijke taal communiceert om een taak te voltooien. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.  

![Conceptafbeelding van drie clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptafbeelding van drie clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>LUIS gebruiken in een chatbot

<a name="Accessing-LUIS"></a>

Zodra de LUIS-app is gepubliceerd, verzendt een client toepassing uitingen (tekst) naar de LUIS natuurlijke taal verwerkings eindpunt- [API][endpoint-apis] en ontvangt de resultaten als JSON-antwoorden. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptafbeeldingen van de werking van LUIS met een chatbot om tekst van de gebruiker te voorspellen met behulp van natuurlijke taalverwerking (NLP)](./media/luis-overview/luis-overview-process-2.png "Conceptafbeeldingen van de werking van LUIS met een chatbot om tekst van de gebruiker te voorspellen met behulp van natuurlijke taalverwerking (NLP)")

|Stap|Action|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|LUIS past het leermodel toe op de tekst in natuurlijke taal en levert intelligente kennis over de invoer van de gebruiker. LUIS retourneert een antwoord in JSON-indeling, met 'HRContact' (HR-contactpersoon) als belangrijkste intentie. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens, zoals de entiteit Type contactpersoon, kunnen worden geëxtraheerd.|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen zijn gebaseerd op een beslissingsstructuur in de code van het botframework en aanroepen naar andere services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Een LUIS-app bevat een specifiek domeinmodel voor natuurlijke taal. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](luis-how-to-use-prebuilt-domains.md) bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepaste entiteiten** LUIS biedt u verschillende manieren om uw eigen aangepaste intenties en entiteiten te definiëren, zoals machine-learned-entiteiten, specifieke of letterlijke entiteiten, of een combinatie van machine-learned en letterlijke entiteiten.

## <a name="build-the-luis-model"></a>Het LUIS-model opbouwen
U bouwt het model op met de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087)-API's of met de LUIS-portal.

Om te beginnen definieert u categorieën gebruikersintenties, ook wel kortweg  **[intenties](luis-concept-intent.md)** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md)** van gebruikers vereist. Elke uiting kan verschillende gegevens bevatten. Deze moeten worden geëxtraheerd met **[entiteiten](luis-concept-entity-types.md)** . 

|Voorbeeld van een uiting van een gebruiker|Intentie|Entiteiten|
|-----------|-----------|-----------|
|"Een vlucht naar __Seattle__ boeken?"|BookFlight|Seattle|
|"Wanneer is uw winkel __open__?"|StoreHoursAndLocation|open|
|"Een vergadering om __13:00 uur__ met __Bob__ van Distributie plannen"|ScheduleMeeting|13:00 uur, Bob|

## <a name="query-prediction-endpoint"></a>Eindpunt queryvoorspelling

Nadat het model is gemaakt en gepubliceerd naar het eindpunt, worden uitingen door de clienttoepassing verzonden naar de gepubliceerde API voor voorspelling van het [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356). Het model wordt door de API op de tekst toegepast voor analyse. De API reageert met de voorspellingsresultaten in JSON-indeling.  

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens kunnen worden geëxtraheerd, zoals de entiteit **Type contactpersoon**. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Voorspellingen van het model verbeteren

Nadat een LUIS-model is gepubliceerd en daadwerkelijke uitingen van gebruikers ontvangt, zijn er verschillende methoden waarmee u de nauwkeurigheid van voorspellingen kunt verbeteren: [actief leren](luis-concept-review-endpoint-utterances.md) van eindpunt-uitingen, [woordgroepenlijsten](luis-concept-feature.md) voor het opnemen van woorden in een domein, en [patronen](luis-concept-patterns.md) om het aantal benodigde uitingen te verminderen.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling
LUIS biedt hulpprogramma's, versiebeheer en samenwerking met andere LUIS-auteurs voor integratie in de volledige levenscyclus voor ontwikkeling op het niveau van de clienttoepassing en het taalmodel. 

## <a name="implementing-luis"></a>LUIS implementeren
LUIS kan als REST-API worden geïntegreerd met elk product, elke service en elk framework waarmee een HTTP-aanvraag wordt gemaakt. De volgende lijst bevat de belangrijkste Microsoft-producten en services die gebruikmaken van LUIS.

De populairste clienttoepassing voor LUIS is:
* [Web-app-bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - hiermee maakt u snel een met LUIS compatibele chatbot voor communicatie met een gebruiker via tekstinvoer. Maakt gebruik van [bot Framework][bot-framework] versie [4. x](https://github.com/Microsoft/botbuilder-dotnet) voor een complete bot-ervaring.

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [Luis cli](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Het NPM-pakket biedt ontwerpen en voor spellingen met als een zelfstandig opdracht regel programma of als import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool) - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* Met [QnA Maker][qnamaker] kunt u verschillende typen tekst combi neren in een vraag-en antwoord Knowledge Base.
* [Spellingcontrole-API voor Bing](../bing-spell-check/proof-text.md) - hiermee kunt u tekst corrigeren vóór het maken van voorspellingen. 
* [Spraakservice](../Speech-Service/overview.md) - hiermee kunt u gesproken taalaanvragen converteren naar tekst. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - hiermee kunt u sneller bot-gesprekken met LUIS bouwen.
* [Project Personality Chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) - hiermee kunt u bot-smalltalk afhandelen.

Voorbeelden die gebruikmaken van LUIS:
* GitHub-opslagplaats voor [AI met gespreksfuncties](https://github.com/Microsoft/AI).
* Azure-voorbeelden van [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Volgende stappen

Een nieuwe LUIS-app met een [vooraf gedefinieerd](luis-get-started-create-app.md) of [aangepast](luis-quickstart-intents-only.md) domein maken. [Een query uitvoeren voor het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md) van een openbare IoT-app.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
