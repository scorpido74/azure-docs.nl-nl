---
title: Integreren met andere toepassingen - QnA Maker
description: QnA Maker integreert in clienttoepassingen zoals chatbots en met andere natuurlijke taalverwerkingsservices zoals Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804184"
---
# <a name="design-knowledge-base-for-client-applications"></a>Ontwerp kennisbank voor klanttoepassingen

QnA Maker integreert in clienttoepassingen zoals chatbots en met andere natuurlijke taalverwerkingsservices zoals Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integratie met een conversationele client

QnA Maker integreert met conversationele clienttoepassingen zoals [Microsoft Bot Framework.](https://dev.botframework.com/) De tekst die naar QnA Maker wordt verzonden, hoeft niet te worden gereinigd of getransformeerd. QnA Maker accepteert natuurlijke talen en geeft het beste antwoord.

## <a name="create-a-bot-without-writing-any-code"></a>Een bot maken zonder code te schrijven

Nadat u uw kennisbasis hebt gepubliceerd, maakt u een bot op de **pagina Publiceren** door de knop **Bot maken** te selecteren. Gebruik de [botzelfstudie](../Quickstarts/create-publish-knowledge-base.md) om te leren wat er gebeurt nadat u de knop hebt geselecteerd.

## <a name="providing-multi-turn-conversations"></a>Multi-turn gesprekken bieden

Een bot client biedt het best geselecteerde antwoord uit uw kennisbank, en kan follow-up vragen als het antwoord is onderdeel van een multi-turn QnA paar. Meer informatie over [het](../how-to/multiturn-conversation.md) toevoegen van multi-turn gespreksvragen en antwoordsets aan uw kennisbank.

## <a name="natural-language-processing"></a>Natuurlijke taalverwerking

Hoewel QnA Maker vragen verwerkt die gebruikmaken van natuurlijke taalverwerking, kan het ook worden gebruikt als onderdeel van een groter systeem dat vragen van meerdere kennisbanken beantwoordt. U QnA Maker combineren met een andere Cognitive Service, Language Understanding (LUIS), om natuurlijke taalverwerking te bieden voordat u naar een specifieke kennisbank gaat. Meer informatie over wanneer en hoe [luis en QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) samen kunnen worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

Leer [ontwikkelingscyclusconcepten](development-lifecycle-knowledge-base.md) voor QnA Maker.