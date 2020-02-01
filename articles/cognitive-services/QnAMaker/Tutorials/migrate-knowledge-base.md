---
title: Knowledge bases migreren-QnA Maker
titleSuffix: Azure Cognitive Services
description: Voor het migreren van een Knowledge Base moet u vanuit één kennis database exporteren en vervolgens importeren in een andere.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902045"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migreren van een kennisdatabase met behulp van exporteren / importeren

Voor het migreren van een Knowledge Base moet u vanuit één kennis database exporteren en vervolgens importeren in een andere.

## <a name="prerequisites"></a>Vereisten

* Maak een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Een nieuwe QnA Maker- [service](../How-To/set-up-qnamaker-service-azure.md) instellen

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Een Knowledge Base migreren uit QnA Maker
1. Meld u aan bij [QnA Maker Portal](https://qnamaker.ai).
1. Selecteer de Knowledge Base van de oorsprong die u wilt migreren.

1. Selecteer op de pagina **instellingen** de optie **Knowledge Base exporteren** om een TSV-bestand te downloaden dat de inhoud van de Knowledge Base van uw oorsprong bevat: vragen, antwoorden, meta gegevens, opvolgings prompts en de namen van de gegevens bronnen waaruit ze zijn geëxtraheerd.

1. Selecteer **een Knowledge Base maken** in het bovenste menu en maak vervolgens een _lege_ Knowledge Base. Het is leeg omdat u geen Url's of bestanden gaat toevoegen wanneer u deze maakt. Deze worden toegevoegd tijdens de stap importeren na het maken.

    De Knowledge Base configureren. Stel alleen de nieuwe naam van de Knowledge Base in. Dubbele namen worden ondersteund en speciale tekens worden ook ondersteund.

    Selecteer niets uit stap 4 omdat deze waarden worden overschreven wanneer u het bestand importeert.

1. Selecteer in stap 5 **maken**.

1. Open in deze nieuwe Knowledge Base het tabblad **instellingen** en selecteer **Knowledge Base importeren**. Hiermee worden de vragen, antwoorden, meta gegevens, opvolgings aanwijzingen geïmporteerd en blijven de namen van de gegevens bronnen waarvan ze zijn geëxtraheerd.

   > [!div class="mx-imgBorder"]
   > [Knowledge Base ![importeren](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Test** de nieuwe knowledge base met behulp van het paneel Test. Meer informatie over het [uw knowledge base test](../How-To/test-knowledge-base.md).
1. **Publiceren** de knowledge base. Meer informatie over het [kennisbank publiceren](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Gebruik het eind punt in uw toepassing of bot-code. Hier te zien hoe u [maken van een QnA bot](../Tutorials/create-qna-bot.md).

    ![QnA Maker-waarden](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Op dit moment worden alle de knowledge base-inhoud - vragen, antwoorden en metagegevens, samen met de namen van de bronbestanden en de URL's, geïmporteerd naar de nieuwe knowledge base.

## <a name="chat-logs-and-alterations"></a>Chat-logboeken en-wijzigingen
Niet-hoofdletter gevoelige wijzigingen (synoniemen) worden niet automatisch geïmporteerd. Gebruik de [v4-api's](https://go.microsoft.com/fwlink/?linkid=2092179) om de wijzigingen in de nieuwe Knowledge Base te verplaatsen.

Het is niet mogelijk om chat-logboeken te migreren omdat de nieuwe Knowledge Base gebruikmaakt van Application Insights voor het opslaan van chat-Logboeken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase bewerken](../How-To/edit-knowledge-base.md)
