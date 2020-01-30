---
title: Integreren met andere toepassingen-QnA Maker
description: QnA Maker kan worden geïntegreerd in client toepassingen zoals chat bots en met andere services voor natuurlijke taal verwerking, zoals Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: f75ee92f2ecd14f5c3e017aeee2340cff0c92561
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843386"
---
# <a name="design-knowledge-base-for-client-applications"></a>De Knowledge Base voor client toepassingen ontwerpen

QnA Maker kan worden geïntegreerd in client toepassingen zoals chat bots en met andere services voor natuurlijke taal verwerking, zoals Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integratie met een gesprek-client

QnA Maker integreert met conversatie-client toepassingen zoals [micro soft bot Framework](https://dev.botframework.com/). De tekst die wordt verzonden naar QnA Maker hoeft niet te worden gereinigd of getransformeerd. QnA Maker accepteert natuurlijke talen en retourneert het beste antwoord.

## <a name="create-a-bot-without-writing-any-code"></a>Een bot maken zonder code te schrijven

Nadat u uw Knowledge Base hebt gepubliceerd, maakt u een bot op de pagina **publiceren** door de knop **bot maken** te selecteren. Gebruik de [bot-zelf studie](../tutorials/create-qna-bot.md) om te leren wat er gebeurt wanneer u de knop selecteert.

## <a name="providing-multi-turn-conversations"></a>Meerdere gesp rekken bieden

Een bot-client biedt het beste geselecteerde antwoord uit uw Knowledge Base en kan opvolgings aanwijzingen geven als het antwoord deel uitmaakt van een multi-turn QnA-set. Meer informatie [over het](../how-to/multiturn-conversation.md) toevoegen van vraag-en antwoord sets voor multi-turn-gesprekken aan uw Knowledge Base.

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Hoewel QnA Maker vragen heeft over de verwerking van natuurlijke taal, kan dit ook een deel uitmaken van een groter systeem dat vragen van meerdere kennis grondslagen beantwoordt. U kunt QnA Maker combi neren met een andere cognitieve service, Language Understanding (LUIS), om natuurlijke taal verwerking te bieden voordat u een specifieke kennis database gaat ophalen. Meer informatie over wanneer en hoe u [Luis en QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) samen kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Leer de [concepten](development-lifecycle-knowledge-base.md) van de ontwikkelings cyclus voor QnA Maker.