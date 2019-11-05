---
title: Een Knowledge Base testen-QnA Maker
titleSuffix: Azure Cognitive Services
description: Het testen van uw QnA Maker Knowledge Base is een belang rijk onderdeel van een iteratief proces om de nauw keurigheid van de reacties die worden geretourneerd, te verbeteren. U kunt de Knowledge Base testen via een verbeterde chat-interface, waarmee u ook bewerkingen kunt uitvoeren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491206"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Test uw Knowledge Base in QnA Maker

Het testen van uw QnA Maker Knowledge Base is een belang rijk onderdeel van een iteratief proces om de nauw keurigheid van de reacties die worden geretourneerd, te verbeteren. U kunt de Knowledge Base testen via een verbeterde chat-interface, waarmee u ook bewerkingen kunt uitvoeren.

## <a name="interactively-test-in-qna-maker-portal"></a>Interactief testen in QnA Maker Portal

1. Open uw kennis database door de naam ervan te selecteren op de pagina **mijn Knowledge bases** .
1. Als u toegang wilt krijgen tot het toetsen paneel testen, selecteert u **testen** in het bovenste paneel van de toepassing.
1. Voer een query in het tekstvak in en selecteer ENTER.
1. Het best overeenkomende antwoord van de Knowledge Base wordt geretourneerd als antwoord.

### <a name="clear-test-panel"></a>Test paneel wissen

Als u alle ingevoerde test query's en de bijbehorende resultaten wilt wissen uit de test console, selecteert u **opnieuw beginnen** in de linkerbovenhoek van het test paneel.

### <a name="close-test-panel"></a>Test paneel sluiten

Als u het test paneel wilt sluiten, selecteert u de knop **testen** opnieuw. Terwijl het test paneel is geopend, kunt u de inhoud van de Knowledge Base niet bewerken.

### <a name="inspect-score"></a>Score controleren

U kunt de details van het test resultaat bekijken in het deel venster inspecteren.

1.  Selecteer in het deel venster voor het testen van de schuif regelaar op **controleren** voor meer informatie over die reactie.

    ![Reacties controleren](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Het deel venster inspectie wordt weer gegeven. Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt het resultaat van de geselecteerde utterance weer gegeven.

### <a name="correct-the-top-scoring-answer"></a>Het bovenste Score antwoord corrigeren

Als het bovenste Score antwoord onjuist is, selecteert u het juiste antwoord in de lijst en selecteert u **opslaan en trainen**.

![Het bovenste Score antwoord corrigeren](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

U kunt alternatieve formulieren van een vraag toevoegen aan een bepaald antwoord. Typ de alternatieve antwoorden in het tekstvak en klik op ENTER om ze toe te voegen. Selecteer **opslaan en trainen** om de updates op te slaan.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Een nieuw antwoord toevoegen

U kunt een nieuw antwoord toevoegen als een van de bestaande antwoorden die overeenkomen onjuist is of als het antwoord niet aanwezig is in de Knowledge Base (er is geen goede overeenkomst gevonden in de KB). 

Klik aan de onderkant van de lijst met antwoorden op het tekstvak om een nieuw antwoord in te voeren en druk op ENTER om het te voegen. 

Selecteer **opslaan en trainen** om dit antwoord te behouden. Er is nu een nieuwe vraag-antwoord paar toegevoegd aan uw Knowledge Base. 

> [!NOTE]
> Alle wijzigingen in uw kennis database worden alleen opgeslagen wanneer u op de knop **opslaan en trainen** drukt.

### <a name="test-the-published-knowledge-base"></a>De gepubliceerde kennis database testen

U kunt de gepubliceerde versie van de Knowledge Base testen in het test venster. Zodra u de KB hebt gepubliceerd, selecteert u het vak **gepubliceerde KB** en verzendt u een query om de resultaten van de gepubliceerde KB op te halen.

![Testen op basis van een gepubliceerde KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch test met hulp programma

Gebruik het hulp programma voor batch tests wanneer u het volgende wilt doen:

* beste antwoord en score voor een set vragen bepalen
* het verwachte antwoord voor een set met vragen valideren

Batch tests worden meegeleverd met het hulp programma voor batch tests. Dit hulp programma is beschikbaar als een [zip-uitvoerbaar bestand](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) dat kan worden gedownload of als [ C# bron code](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[Naslag documentatie over het hulp programma](../reference-tsv-format-batch-testing.md) is onder andere:

* het opdracht regel voorbeeld van het hulp programma
* de indeling voor bestanden voor TSV-invoer en-uitvoer 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase publiceren](./publish-knowledge-base.md)
