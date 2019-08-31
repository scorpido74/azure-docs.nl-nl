---
title: Knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: Een kennisdatabase QnA Maker bestaat uit een reeks vraag/antwoord (QnA)-sleutelparen en optionele metagegevens die zijn gekoppeld aan elk paar QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78b04a06b71590a79180d1ae367e7d059e1b84a3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195238"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Wat is er een basis voor kennis van QnA Maker?

Een kennisdatabase QnA Maker bestaat uit een reeks vraag/antwoord (QnA)-sleutelparen en optionele metagegevens die zijn gekoppeld aan elk paar QnA.

## <a name="key-knowledge-base-concepts"></a>Kennisbank van belangrijke concepten

* **Vragen over** -een vraag tekst bevat die het dichtst bij de aanvraag voor een gebruiker. 
* **Antwoorden** -antwoord is het antwoord dat wordt geretourneerd als een gebruikersquery met de bijbehorende vraag overeenkomt.  
* **Metagegevens** -metagegevens zijn labels die zijn gekoppeld aan een paar QnA en worden weergegeven als sleutel-waardeparen. Tags voor meta gegevens worden gebruikt voor het filteren van QnA-paren en het beperken van de set waarover overeenkomende query's worden uitgevoerd.

Een enkele QnA, vertegenwoordigd door een numerieke ID van de QnA heeft meerdere varianten van een vraag (alternatieve vragen) dat alle aan een enkel antwoord toewijzen. Daarnaast kunnen aan elk van deze combi natie meerdere meta gegevens velden zijn gekoppeld: één sleutel en één waarde.

![QnA Maker knowledge bases](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Inhoud Knowledge base-indeling

Wanneer u inhoud met opmaak in een knowledge base opnemen, probeert de QnA Maker aan de inhoud naar markdown converteren. Lezen [dit](https://aka.ms/qnamaker-docs-markdown-support) blog om te begrijpen van de markdown indelingen onbegrijpelijk voor de meeste chat-clients.

Velden van de metagegevens bestaan uit de sleutel / waarde-paren, gescheiden door een dubbele punt **(Product: Shredder)** . Zowel de sleutel en de waarde moet alleen tekst. De metagegevenssleutel mag geen spaties bevatten. Meta gegevens bieden ondersteuning voor slechts één waarde per sleutel.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hoe QnA Maker een gebruikers query verwerkt om het beste antwoord te selecteren

De getrainde en [gepubliceerde](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker Knowledge Base ontvangt een gebruikers query, vanuit een bot of andere client toepassing, op de [GenerateAnswer-API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Het volgende diagram illustreert het proces wanneer de query van de gebruiker wordt ontvangen.

![Het volgorde proces voor een gebruikers query](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Classificatie proces

Het proces wordt uitgelegd in de volgende tabel:

|Stap|Doel|
|--|--|
|1|De client toepassing verzendt de query van de gebruiker naar de [GenerateAnswer-API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker voor het verwerken van de gebruikers query met taal detectie, spellingen en woord afbreekers.|
|3|Deze voor verwerking wordt gebruikt om de gebruikers query voor de beste Zoek resultaten te wijzigen.|
|4|Deze gewijzigde query wordt verzonden naar Azure search index, waarbij het `top` aantal resultaten wordt ontvangen. Als het juiste antwoord niet in deze resultaten voor komt, verhoogt u `top` de waarde enigszins. Over het algemeen is de waarde `top` 10 voor Works in 90% van de query's.|
|5|QnA Maker past geavanceerde parametrisatie toe om de juistheid van de opgehaalde Azure Search resultaten voor de gebruikers query te bepalen. |
|6|Het getrainde rangorde model gebruikt de functie Score uit stap 5 om de Azure Search resultaten te rangschikken.|
|7|De nieuwe resultaten worden weer gegeven in de rang orde van de client toepassing.|
|||

De functies die worden gebruikt, zijn, maar zijn niet beperkt tot semantiek op woord niveau, urgentie op term niveau in een verzameling en diep opgedane semantische modellen om de gelijkenis en relevantie tussen twee teken reeksen te bepalen.

## <a name="http-request-and-response-with-endpoint"></a>HTTP-aanvraag en-antwoord met een eind punt
Wanneer u uw Knowledge Base publiceert, maakt de service een op REST gebaseerd HTTP- **eind punt** dat kan worden geïntegreerd in uw toepassing, meestal een chat-bot. 

### <a name="the-user-query-request-to-generate-an-answer"></a>De aanvraag van de gebruikers query voor het genereren van een antwoord

Een **gebruikers query** is de vraag of de eind gebruiker de Knowledge Base vraagt, zoals, `How do I add a collaborator to my app?`. De query is vaak in een natuurlijke taal of enkele tref woorden die de vraag vertegenwoordigen, zoals, `help with collaborators`. De query wordt naar uw kennis verzonden vanuit een HTTP- **aanvraag** in uw client toepassing.

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

### <a name="the-response-from-a-call-to-generate-answer"></a>Het antwoord van een aanroep voor het genereren van een antwoord

Het HTTP- **antwoord** is het antwoord dat is opgehaald uit de Knowledge Base, op basis van de beste overeenkomst voor een bepaalde gebruikers query. Het antwoord bevat het antwoord en de Voorspellings Score. Als u om meer dan één bovenste antwoord hebt gevraagd, krijgt `top` u met de eigenschap meer dan een bovenste antwoord, elk met een score. 

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
Een kennisdatabase is de opslagplaats van vragen en antwoorden die zijn gemaakt, onderhouden en via de QnA Maker gebruikt. Elke laag QnA Maker kan worden gebruikt voor meerdere Knowledge bases.

Een kennisdatabase heeft twee statussen - Test en gepubliceerd. 

De **test kennis basis** is de versie die wordt bewerkt, opgeslagen en getest op nauw keurigheid en volledige reacties. De eindgebruiker van uw toepassing/chatbot niet van invloed op wijzigingen in de test knowledge base. De test kennis basis staat bekend als `test` in de HTTP-aanvraag. 

De **gepubliceerde kennis database** is de versie die wordt gebruikt in uw chat-bot/-toepassing. De actie van de publicatie van een kennisdatabase wordt de inhoud van de Test knowledge base geplaatst in de gepubliceerde versie van de knowledge base. Omdat de gepubliceerde knowledge base de versie die gebruikmaakt van de toepassing via het eindpunt is, moet zorgvuldig worden uitgevoerd om ervoor te zorgen dat de inhoud correct en goed geteste is. De gepubliceerde kennis database staat bekend als `prod` in de HTTP-aanvraag. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ontwikkelingslevenscyclus van een kennisdatabase](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zie ook

[Overzicht van QnA Maker](../Overview/overview.md)

Knowledge Base maken en bewerken met: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET-SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Antwoord genereren met: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET-SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
