---
title: Knowledge Base-QnA Maker
titleSuffix: Azure Cognitive Services
description: Een QnA Maker Knowledge Base bestaat uit een set vraag-en antwoord-paren (QnA) en optionele meta gegevens die zijn gekoppeld aan elk QnA-paar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794903"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Wat is een QnA Maker Knowledge Base?

Een QnA Maker Knowledge Base bestaat uit een set vraag-en antwoord-paren (QnA) en optionele meta gegevens die zijn gekoppeld aan elk QnA-paar.

## <a name="key-knowledge-base-concepts"></a>Basis concepten van belang rijke kennis

* **Vragen**: een vraag bevat tekst die het beste een gebruikers query weergeeft. 
* **Antwoorden**: een antwoord is het antwoord dat wordt geretourneerd wanneer een gebruikers query overeenkomt met de bijbehorende vraag.  
* **Meta gegevens**: meta gegevens zijn tags die zijn gekoppeld aan een QnA paar en worden weer gegeven als sleutel-waardeparen. Tags voor meta gegevens worden gebruikt voor het filteren van QnA-paren en het beperken van de set waarover overeenkomende query's worden uitgevoerd.

Eén QnA, vertegenwoordigd door een numerieke QnA-ID, heeft meerdere varianten van een vraag (alternatieve vragen) die allemaal zijn toegewezen aan één antwoord. Daarnaast kunnen aan elk van deze combi natie meerdere meta gegevens velden zijn gekoppeld: één sleutel en één waarde.

![Knowledge Base van QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Inhouds indeling Knowledge Base

Wanneer u opgemaakte inhoud opneemt in een Knowledge Base, QnA Maker probeert de inhoud te converteren naar prijs verlaging. Lees [deze blog](https://aka.ms/qnamaker-docs-markdown-support) voor meer informatie over de uitprijs notaties die worden herkend door de meeste chat-clients.

Meta gegevens velden bestaan uit sleutel-waardeparen, gescheiden door een dubbele punt, zoals product: versnipperer. Zowel de sleutel als de waarde moet alleen tekst zijn. De meta gegevens sleutel mag geen spaties bevatten. Meta gegevens bieden ondersteuning voor slechts één waarde per sleutel.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hoe QnA Maker een gebruikers query verwerkt om het beste antwoord te selecteren

De getrainde en [gepubliceerde](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Knowledge Base ontvangt een gebruikers query, vanuit een bot of andere client toepassing, op de [GenerateAnswer-API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Het volgende diagram illustreert het proces wanneer de query van de gebruiker wordt ontvangen.

![Het volgorde proces voor een gebruikers query](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Classificatie proces

Het proces wordt uitgelegd in de volgende tabel.

|Stap|Doel|
|--|--|
|1|De client toepassing verzendt de query van de gebruiker naar de [GenerateAnswer-API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker de gebruikers query met taal detectie, spellingen en woord afbreekers verwerkt.|
|3|Deze voor verwerking wordt gebruikt om de query van de gebruiker voor de beste Zoek resultaten te wijzigen.|
|4|Deze gewijzigde query wordt verzonden naar een Azure Cognitive Search-index, die het `top` aantal resultaten ontvangt. Als het juiste antwoord niet in deze resultaten voor komt, verhoogt u de waarde van `top` enigszins. Over het algemeen geldt een waarde van 10 voor `top` van 90% van de query's.|
|5|QnA Maker past geavanceerde parametrisatie toe om de juistheid van de opgehaalde Zoek resultaten voor de gebruikers query te bepalen. |
|6|Het getrainde rangorde model gebruikt de functie Score uit stap 5 om de resultaten van Azure Cognitive Search te rangschikken.|
|7|De nieuwe resultaten worden weer gegeven in de rang orde van de client toepassing.|
|||

De functies die worden gebruikt, zijn, maar zijn niet beperkt tot semantiek op woord niveau, urgentie op term niveau in een verzameling en diep opgedane semantische modellen om de gelijkenis en relevantie tussen twee teken reeksen te bepalen.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-aanvraag en-antwoord met een eind punt
Wanneer u uw Knowledge Base publiceert, maakt de service een op REST gebaseerd HTTP-eind punt dat kan worden geïntegreerd in uw toepassing, meestal een chat-bot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>De aanvraag van de gebruikers query voor het genereren van een antwoord

Een gebruikers query is de vraag of de eind gebruiker de Knowledge Base vraagt, bijvoorbeeld `How do I add a collaborator to my app?`. De query is vaak in een natuurlijke taal indeling of enkele tref woorden die de vraag vertegenwoordigen, zoals `help with collaborators`. De query wordt naar uw Knowledge Base verzonden vanuit een HTTP-aanvraag in uw client toepassing.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

U bepaalt het antwoord door eigenschappen zoals [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)en [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)in te stellen.

Gebruik [gespreks context](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) met [multi-turn-functionaliteit](../how-to/multiturn-conversation.md) om de conversatie te laten verfijnen om de vragen en antwoorden op te lossen, om het juiste en laatste antwoord te vinden.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Het antwoord van een aanroep om een antwoord te genereren

Het HTTP-antwoord is het antwoord dat is opgehaald uit de Knowledge Base, op basis van de beste overeenkomst voor een bepaalde gebruikers query. Het antwoord bevat het antwoord en de Voorspellings Score. Als u om meer dan één hoofd antwoord met de eigenschap `top` wordt gevraagd, krijgt u meer dan een bovenste antwoord, elk met een score. 

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

### <a name="test-and-production-knowledge-base"></a>Test-en productie Knowledge Base
Een kennis database is de opslag plaats van vragen en antwoorden die zijn gemaakt, onderhouden en worden gebruikt via QnA Maker. Elke QnA Maker laag kan worden gebruikt voor meerdere kennis grondslagen.

Een kennis database heeft twee statussen: *testen* en *gepubliceerd*.

De *test kennis basis* is de versie die wordt bewerkt, opgeslagen en getest op nauw keurigheid en volledige reacties. Wijzigingen die zijn aangebracht in de test Knowledge Base, hebben geen invloed op de eind gebruiker van uw toepassing of chat-bot. De test kennis basis staat bekend als `test` in de HTTP-aanvraag. 

De *gepubliceerde kennis database* is de versie die wordt gebruikt in uw chat-bot of-toepassing. De actie voor het publiceren van een Knowledge Base heeft de inhoud van de test Knowledge Base in de gepubliceerde versie van de Knowledge Base. Omdat de gepubliceerde kennis basis de versie is die de toepassing via het eind punt gebruikt, moet u ervoor zorgen dat de inhoud juist en goed is getest. De gepubliceerde kennis basis staat bekend als `prod` in de HTTP-aanvraag.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontwikkelings levenscyclus van een Knowledge Base](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zie ook

[Overzicht van QnA Maker](../Overview/overview.md)

Een Knowledge Base maken en bewerken met: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET-SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Een antwoord genereren met: 
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET-SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
