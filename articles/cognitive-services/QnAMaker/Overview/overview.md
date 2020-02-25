---
title: Wat is QnA Maker-service?
titleSuffix: Azure Cognitive Services
description: QnA Maker is een op de cloud gebaseerde NLP-service waarmee u eenvoudig een natuurlijk gesprek slaag maakt over uw gegevens. Het kan worden gebruikt om het meest geschikte antwoord te vinden voor een bepaalde invoer in natuurlijke taal, vanuit uw aangepaste Knowledge Base (KB) aan informatie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 91a63e87e937eab4c02d2b86026aa09472cb15ef
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561087"
---
# <a name="what-is-the-qna-maker-service"></a>Wat is de QnA Maker-service?

QnA Maker is een NLP-service (in de cloud gebaseerde natuurlijke taal verwerking) waarmee eenvoudig een natuurlijk gesprek slaag wordt gemaakt over uw gegevens. Het kan worden gebruikt om het meest geschikte antwoord te vinden voor een bepaalde invoer in natuurlijke taal, vanuit uw aangepaste Knowledge Base (KB) aan informatie.

Een client toepassing voor QnA Maker is een wille keurige conversatie toepassing die communiceert met een gebruiker in natuurlijke taal om een vraag te beantwoorden. Voorbeelden van clienttoepassingen zijn socialemedia-apps, chatbots en met spraak bestuurde bureaubladtoepassingen.

## <a name="when-to-use-qna-maker"></a>Wanneer moet ik QnA Maker gebruiken?

* **Wanneer u statische informatie hebt** : gebruik QnA Maker als u statische informatie hebt in uw Knowledge Base van antwoorden. Deze kennis database is aangepast aan uw behoeften, die u hebt gemaakt met documenten zoals [pdf's en url's](../concepts/content-types.md).
* **Als u hetzelfde antwoord op een aanvraag, vraag of opdracht wilt opgeven** wanneer verschillende gebruikers dezelfde vraag verzenden, wordt hetzelfde antwoord geretourneerd.
* **Als u statische gegevens op basis van meta gegevens wilt filteren** , voegt u labels voor [META](../how-to/metadata-generateanswer-usage.md) gegevens toe om extra filter opties te bieden die relevant zijn voor de gebruikers van uw client toepassing en de informatie. Algemene informatie over meta gegevens omvat [Chit-Chat](../how-to/chit-chat-knowledge-base.md), inhouds type of-indeling, inhouds doel en de versheid van inhoud.
* **Wanneer u een bot-conversatie met statische informatie wilt beheren** , neemt uw kennis basis de conversatie tekst of opdracht van de gebruiker en beantwoordt deze. Als het antwoord deel uitmaakt van een vooraf bepaalde conversatie stroom, die in uw Knowledge Base wordt weer gegeven in de [context van meerdere scha kelen](../how-to/multiturn-conversation.md), kan de bot deze stroom eenvoudig bieden.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>QnA Maker Knowledge Base gebruiken in een chat-bot

Zodra een QnA Maker Knowledge Base is gepubliceerd, stuurt een client toepassing een vraag naar uw Knowledge Base-eind punt en ontvangt de resultaten als een JSON-antwoord. Een gemeen schappelijke client toepassing voor QnA Maker is een chat-bot.

![Een vraag stellen aan een bot en antwoord krijgen van inhoud van kennis basis](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Stap|Bewerking|
|:--|:--|
|1|De client toepassing verstuurt de _vraag_ van de gebruiker (tekst in hun eigen woorden), "Hoe kan ik op een programmatische manier mijn Knowledge Base bijwerken?" aan uw Knowledge Base-eind punt.|
|2|QnA Maker maakt gebruik van de getrainde kennis basis om het juiste antwoord te geven en eventuele vervolg vragen die kunnen worden gebruikt voor het verfijnen van de zoek opdracht naar het beste antwoord. QnA Maker retourneert een reactie in JSON-indeling.|
|3|De client toepassing gebruikt het JSON-antwoord om beslissingen te nemen over het volgen van de conversatie. Deze beslissingen kunnen bevatten hoe het beste antwoord wordt weer gegeven en dat er meer opties worden gepresenteerd om de zoek opdracht te verfijnen. |
|||

## <a name="what-is-a-knowledge-base"></a>Wat is een kennis database?

QnA Maker [uw inhoud importeert](../concepts/knowledge-base.md) in een Knowledge Base met vraag-en antwoord sets. Het import proces haalt informatie over de relatie tussen de onderdelen van uw gestructureerde en semi-gestructureerde inhoud om relaties tussen de vraag-en antwoord sets te impliceren. U kunt deze vraag-en antwoord sets bewerken of nieuwe sets toevoegen.

De inhoud van de vraag en de antwoordset bevat:
* Alle andere vormen van de vraag
* Meta gegevenslabels die worden gebruikt voor het filteren van antwoord keuzes tijdens de zoek opdracht
* Vragen om de zoek verfijning voort te zetten

![Voorbeeld vraag en antwoord met meta gegevens](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Nadat u uw Knowledge Base hebt gepubliceerd, verzendt een client toepassing de vraag van een gebruiker naar uw eind punt. Uw QnA Maker-service verwerkt de vraag en reageert met het beste antwoord.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Maken, beheren en publiceren naar een bot zonder code

De QnA Maker Portal biedt de volledige ontwerp ervaring op basis van kennis. U kunt documenten in hun huidige vorm importeren in uw Knowledge Base. Deze documenten (zoals een veelgestelde vragen, een werk blad of een webpagina) worden omgezet in vraag-en antwoord sets. Elke set wordt geanalyseerd op opvolgings prompts en verbonden met andere sets. De uiteindelijke _verkoop_ vorm biedt ondersteuning voor uitgebreide presentaties, waaronder afbeeldingen en koppelingen.

Zodra uw kennis database is bewerkt, publiceert u de Knowledge Base naar een werkende [Azure web app-bot](https://azure.microsoft.com/services/bot-service/) zonder dat u code hoeft te schrijven. Test uw bot in het [Azure Portal](https://portal.azure.com) of down load en blijf de ontwikkeling.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>De zoek kwaliteit en classificatie bieden het best mogelijke antwoord

Het systeem van QnA Maker is een gelaagde classificatie benadering. De gegevens worden opgeslagen in azure Search, die ook fungeert als de eerste laag met lagen. De belangrijkste resultaten van Azure Search worden vervolgens door gegeven via het herwaarderings model van QnA Maker NLP om de uiteindelijke resultaten en betrouwbaarheids score te produceren.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker verbetert het conversatie proces

QnA Maker biedt vragen over meerdere schakelingen en actief leren om u te helpen bij het verbeteren van uw basis vraag-en-antwoord sets.

Met **vragen over meerdere schakelingen** krijgt u de mogelijkheid om vraag-en antwoord paren te verbinden. Met deze verbinding kan de client toepassing een antwoord op het hoogste niveau geven en kunnen er meer vragen worden gesteld om de zoek opdracht te verfijnen voor een eind antwoord.

Nadat de Knowledge Base vragen heeft ontvangen van gebruikers op het gepubliceerde eind punt, wordt in QnA Maker **actief leren** toegepast op deze praktijk vragen om wijzigingen in uw kennis basis te suggereren om de kwaliteit te verbeteren.

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling

QnA Maker biedt ontwerp, training en publicatie samen met samenwerkings machtigingen voor integratie in de levens cyclus van de volledige ontwikkeling.

> [!div class="mx-imgBorder"]
> ![conceptuele afbeelding van de ontwikkelings cyclus](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hoe kan ik starten?

**Stap 1**: maak een QnA Maker-resource in de [Azure Portal](https://portal.azure.com).

**Stap 2**: Maak een Knowledge Base in de [QnA Maker](https://www.qnamaker.ai) Portal. Voeg [bestanden en url's](../concepts/content-types.md) toe om de Knowledge Base te maken.

**Stap 3**: Publiceer uw Knowledge Base en test vanuit uw aangepaste eind punt met behulp van [krul of postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Stap 4**: vanuit uw client toepassing kunt u het eind punt van uw kennis database programmatisch aanroepen. De client toepassing verwerkt het JSON-antwoord om het beste antwoord op de gebruiker weer te geven.

## <a name="next-steps"></a>Volgende stappen
QnA Maker biedt alles wat u nodig hebt om uw aangepaste Knowledge Base te maken, beheren en implementeren.

> [!div class="nextstepaction"]
> [Bekijk de meest recente wijzigingen](../whats-new.md)
