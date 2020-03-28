---
title: Wat is de QnA Maker-service?
titleSuffix: Azure Cognitive Services
description: QnA Maker is een cloudgebaseerde NLP-service die eenvoudig een natuurlijke gesprekslaag over uw gegevens creëert. Het kan worden gebruikt om het meest geschikte antwoord te vinden voor een bepaalde natuurlijke taal input, van uw aangepaste knowledge base (KB) van informatie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 2863a086343b0a5d3bc69ae319dbe93d557bcb4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052932"
---
# <a name="what-is-the-qna-maker-service"></a>Wat is de QnA Maker-service?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker is een cloudgebaseerde Natural Language Processing (NLP)-service die eenvoudig een natuurlijke gesprekslaag over uw gegevens creëert. Het kan worden gebruikt om het meest geschikte antwoord te vinden voor een bepaalde natuurlijke taal input, van uw aangepaste knowledge base (KB) van informatie.

Een clienttoepassing voor QnA Maker is een conversationele toepassing die communiceert met een gebruiker in natuurlijke taal om een vraag te beantwoorden. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.

## <a name="when-to-use-qna-maker"></a>Wanneer qna maker gebruiken

* **Wanneer u statische informatie hebt** - Gebruik QnA Maker wanneer u statische informatie in uw kennisbasis van antwoorden hebt. Deze kennisbank is aangepast aan uw behoeften, die u hebt gebouwd met documenten zoals [PDF's en URL's.](../concepts/content-types.md)
* **Wanneer u hetzelfde antwoord op een verzoek, vraag of opdracht wilt geven,** wordt hetzelfde antwoord geretourneerd wanneer verschillende gebruikers dezelfde vraag indienen.
* **Wanneer u statische informatie wilt filteren op basis van meta-informatie,** voegt u [meta-tags](../how-to/metadata-generateanswer-usage.md) toe om extra filteropties te bieden die relevant zijn voor de gebruikers van uw clienttoepassing en de informatie. Algemene metadata-informatie omvat [chit-chat,](../how-to/chit-chat-knowledge-base.md)inhoudstype of -indeling, inhoudsdoel en versheid van inhoud.
* **Wanneer u een botgesprek wilt beheren dat statische informatie bevat,** neemt uw kennisbank de gesprekstekst of opdracht van een gebruiker en beantwoordt deze. Als het antwoord deel uitmaakt van een vooraf bepaalde gespreksstroom, vertegenwoordigd in uw kennisbank met [multi-turn context,](../how-to/multiturn-conversation.md)kan de bot deze stroom gemakkelijk bieden.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Gebruik QnA Maker knowledge base in een chat bot

Zodra een QnA Maker knowledge base is gepubliceerd, stuurt een clientapplicatie een vraag naar uw knowledge base endpoint en ontvangt de resultaten als JSON-antwoord. Een veelvoorkomende clienttoepassing voor QnA Maker is een chatbot.

![Stel een bot een vraag en krijg antwoord van knowledge base content](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Stap|Actie|
|:--|:--|
|1|De clientapplicatie stuurt de _vraag_ van de gebruiker (tekst in hun eigen woorden), "Hoe werk ik mijn Knowledge Base programmatisch bij?" naar uw knowledge base eindpunt.|
|2|QnA Maker gebruikt de getrainde kennisbank om het juiste antwoord en eventuele follow-up prompts te geven die kunnen worden gebruikt om de zoektocht naar het beste antwoord te verfijnen. QnA Maker retourneert een JSON-geformatteerd antwoord.|
|3|De clientapplicatie gebruikt de JSON-respons om beslissingen te nemen over hoe het gesprek kan worden voortgezet. Deze beslissingen kunnen bestaan uit het tonen van het bovenste antwoord en het presenteren van meer keuzes om de zoektocht naar het beste antwoord te verfijnen. |
|||

## <a name="what-is-a-knowledge-base"></a>Wat is een kennisbank?

QnA Maker [importeert uw content](../concepts/knowledge-base.md) in een kennisbank van vraag- en antwoordsets. Het importproces haalt informatie over de relatie tussen de delen van uw gestructureerde en semi-gestructureerde inhoud om relaties tussen de vraag- en antwoordsets te impliceren. U deze vraag- en antwoordsets bewerken of nieuwe sets toevoegen.

De inhoud van de vraag- en antwoordset omvat:
* Alle alternatieve vormen van de vraag
* Metagegevenstags die worden gebruikt om antwoordkeuzes te filteren tijdens het zoeken
* Follow-up vraagt om de zoekverfijning voort te zetten

![Voorbeeldvraag en -antwoord met metagegevens](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Nadat u uw kennisbank hebt gepubliceerd, stuurt een clienttoepassing de vraag van een gebruiker naar uw eindpunt. Uw QnA Maker-service verwerkt de vraag en reageert met het beste antwoord.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Een bot zonder code maken, beheren en publiceren

De QnA Maker portal biedt de volledige kennisbank authoring ervaring. U documenten, in hun huidige vorm, importeren in uw kennisbank. Deze documenten (zoals een faq, producthandleiding, spreadsheet of webpagina) worden omgezet in vraag- en antwoordsets. Elke set wordt geanalyseerd op vervolgprompts en verbonden met andere sets. De uiteindelijke _markdown-indeling_ ondersteunt uitgebreide presentatie, inclusief afbeeldingen en koppelingen.

Zodra uw kennisbank is bewerkt, publiceert u de kennisbank naar een werkende [Azure Web App-bot](https://azure.microsoft.com/services/bot-service/) zonder code te schrijven. Test uw bot in de [Azure-portal](https://portal.azure.com) of download en zet de ontwikkeling voort.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Zoekkwaliteit en ranking geven het best mogelijke antwoord

QnA Maker's systeem is een gelaagde ranking aanpak. De gegevens worden opgeslagen in Azure search, die ook dient als de eerste rankinglaag. De topresultaten van Azure search worden vervolgens doorgegeven via het NLP-reranking-model van QnA Maker om de uiteindelijke resultaten en betrouwbaarheidsscore te produceren.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker verbetert het gespreksproces

QnA Maker biedt multi-turn prompts en actief leren om u te helpen uw basisvraag- en antwoordsets te verbeteren.

**Multi-turn prompts** geven je de mogelijkheid om vraag- en antwoordparen met elkaar te verbinden. Deze verbinding stelt de clientapplicatie in staat om een topantwoord te geven en biedt meer vragen om de zoektocht naar een definitief antwoord te verfijnen.

Nadat de kennisbank vragen ontvangt van gebruikers op het gepubliceerde eindpunt, past QnA Maker **actief leren** toe op deze real-world vragen om wijzigingen in uw kennisbasis voor te stellen om de kwaliteit te verbeteren.

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling

QnA Maker biedt authoring, training en publicatie, samen met samenwerkingsmachtigingen om te integreren in de volledige levenscyclus van de ontwikkeling.

> [!div class="mx-imgBorder"]
> ![Conceptueel beeld van ontwikkelingscyclus](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hoe kan ik beginnen?

**Stap 1:** Een QnA Maker-bron maken in de [Azure-portal.](https://portal.azure.com)

**Stap 2**: Creëer een kennisbank in de [QnA Maker](https://www.qnamaker.ai) portal. Voeg [bestanden en URL's toe](../concepts/content-types.md) om de kennisbank te maken.

**Stap 3**: Publiceer uw kennisbestand en test vanaf uw aangepaste eindpunt met behulp van [cURL of Postbode.](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

**Stap 4**: Vanuit uw klantapplicatie, programmatisch noemen van uw kennisbank eindpunt. De clientapplicatie verwerkt de JSON-respons om het beste antwoord aan de gebruiker te laten zien.

## <a name="next-steps"></a>Volgende stappen
QnA Maker biedt alles wat u nodig hebt om uw aangepaste kennisbank te bouwen, te beheren en te implementeren.

> [!div class="nextstepaction"]
> [Bekijk de laatste wijzigingen](../whats-new.md)
