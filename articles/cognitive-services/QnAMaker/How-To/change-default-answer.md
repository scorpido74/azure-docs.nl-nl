---
title: Standaard antwoord ophalen-QnA Maker
description: Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979964"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Standaard antwoord voor een QnA Maker resource wijzigen

Het standaard antwoord voor een Knowledge Base is bedoeld om te worden geretourneerd als er geen antwoord wordt gevonden. Als u een client toepassing gebruikt, zoals de [Azure bot-service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), kan deze ook een afzonderlijk standaard antwoord hebben. Dit geeft aan dat er geen antwoord is met de drempel waarde voor de score.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Standaard antwoord instellen wanneer u een Knowledge Base maakt

Wanneer u een nieuwe Knowledge Base maakt, is de standaard antwoord tekst een van de instellingen. Als u ervoor kiest deze niet in te stellen tijdens het aanmaak proces, kunt u dit later doen met de volgende procedure.

## <a name="change-default-answer-in-qna-maker-portal"></a>Standaard antwoord in QnA Maker portal wijzigen

Het standaard antwoord van de Knowledge Base wordt geretourneerd als er geen antwoord wordt geretourneerd door de QnA Maker-service.

1. Meld u aan bij de [QnA Maker-Portal](https://www.qnamaker.ai/) en selecteer uw Knowledge Base in de lijst.
1. Selecteer **instellingen** in de navigatie balk.
1. Wijzig de waarde van **standaard antwoord tekst** in het gedeelte **Knowledge Base beheren** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Scherm opname van QnA Maker Portal, instellingen pagina, met standaard antwoord tekstvak gemarkeerd.":::

1. Selecteer **opslaan en trainen** om de wijziging op te slaan.

## <a name="next-steps"></a>Volgende stappen

* [Een kennisdatabase maken](../How-to/manage-knowledge-bases.md)