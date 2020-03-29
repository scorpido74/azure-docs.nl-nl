---
title: Vraag de kennisbank op - QnA Maker
description: Er moet een kennisbank worden gepubliceerd. Eenmaal gepubliceerd, wordt de knowledge base opgevraagd op het eindpunt voor runtime voorspelling met behulp van de generateAnswer API.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220719"
---
# <a name="query-the-knowledge-base-for-answers"></a>De kennisbank opvragen voor antwoorden

Er moet een kennisbank worden gepubliceerd. Eenmaal gepubliceerd, wordt de knowledge base opgevraagd op het eindpunt voor runtime voorspelling met behulp van de generateAnswer API. De query bevat de vraagtekst en andere instellingen om QnA Maker te helpen de best mogelijke overeenkomst met een antwoord te selecteren.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hoe QnA Maker een gebruikersquery verwerkt om het beste antwoord te selecteren

De getrainde en [gepubliceerde](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker-kennisbank ontvangt een gebruikersquery, van een bot of andere clienttoepassing, bij de [GenerateAnswer API.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) In het volgende diagram wordt het proces geïllustreerd wanneer de gebruikersquery wordt ontvangen.

![Het classificatiemodelproces voor een gebruikersquery](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Rankerproces

Het proces wordt uitgelegd in de volgende tabel.

|Stap|Doel|
|--|--|
|1|De clienttoepassing stuurt de gebruikersquery naar de [GenerateAnswer API.](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)|
|2|QnA Maker verwerkt de gebruikersquery vooraf met taaldetectie, spellers en woordbrekers.|
|3|Deze voorbewerking wordt gebruikt om de gebruikersquery te wijzigen voor de beste zoekresultaten.|
|4|Deze gewijzigde query wordt verzonden naar een Azure `top` Cognitive Search Index, die het aantal resultaten ontvangt. Als het juiste antwoord niet in deze resultaten `top` staat, verhoogt u de waarde iets. Over het algemeen, een `top` waarde van 10 voor werken in 90% van de query's.|
|5|QnA Maker gebruikt syntactische en semantische gebaseerde featurisatie om de gelijkenis tussen de gebruikersquery en de opgehaalde QnA-resultaten te bepalen.|
|6|Het machine-geleerde rankermodel gebruikt de verschillende functies, vanaf stap 5, om de betrouwbaarheidsscores en de nieuwe rangschikkingsvolgorde te bepalen.|
|7|De nieuwe resultaten worden in volgorde teruggegeven aan de clienttoepassing.|
|||

Functies die worden gebruikt, zijn onder andere, maar zijn niet beperkt tot semantiek op woordniveau, belangrijkheid op termenniveau in een corpus en diep geleerde semantische modellen om de gelijkenis en relevantie tussen twee tekenreeksen te bepalen.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-aanvraag en -antwoord met eindpunt
Wanneer u uw kennisbank publiceert, maakt de service een OP RUST gebaseerd HTTP-eindpunt dat kan worden geïntegreerd in uw toepassing, meestal een chatbot.

### <a name="the-user-query-request-to-generate-an-answer"></a>Het verzoek van de gebruiker om een antwoord te genereren

Een gebruikersquery is de vraag die de eindgebruiker stelt `How do I add a collaborator to my app?`aan de kennisbank, zoals . De query is vaak in een natuurlijke taalindeling of een `help with collaborators`paar zoekwoorden die de vraag vertegenwoordigen, zoals . De query wordt verzonden naar uw kennisbank vanuit een HTTP-aanvraag in uw clienttoepassing.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

U bepaalt het antwoord door eigenschappen in te stellen zoals [scoreThreshold,](./confidence-score.md#choose-a-score-threshold) [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)en [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Gebruik [gesprekscontext](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) met [multi-turn functionaliteit](../how-to/multiturn-conversation.md) om het gesprek te houden om de vragen en antwoorden te verfijnen, om het juiste en definitieve antwoord te vinden.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Het antwoord van een oproep om een antwoord te genereren

Het HTTP-antwoord is het antwoord dat wordt opgehaald uit de kennisbank, op basis van de beste overeenkomst voor een bepaalde gebruikersquery. Het antwoord omvat het antwoord en de voorspellingsscore. Als u om meer dan één `top` hoogste antwoord met het bezit vroeg, krijgt u meer dan één hoogste antwoord, elk met een score.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Betrouwbaarheidsscore](./confidence-score.md)
