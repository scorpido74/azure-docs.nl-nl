---
title: Het testen van een knowledge base - QnA Maker
description: Testen van uw kennisdatabase QnA Maker is een belangrijk onderdeel van een iteratief proces voor het verbeteren van de nauwkeurigheid van de antwoorden die worden geretourneerd. De knowledge base via een verbeterde chat-interface waarmee ook kunt dat u wijzigingen aanbrengen, kunt u testen.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927263"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Test uw Knowledge Base in QnA Maker

Testen van uw kennisdatabase QnA Maker is een belangrijk onderdeel van een iteratief proces voor het verbeteren van de nauwkeurigheid van de antwoorden die worden geretourneerd. De knowledge base via een verbeterde chat-interface waarmee ook kunt dat u wijzigingen aanbrengen, kunt u testen.

## <a name="interactively-test-in-qna-maker-portal"></a>Interactief testen in QnA Maker Portal

1. Open uw kennis database door de naam ervan te selecteren op de pagina **mijn Knowledge bases** .
1. Als u toegang wilt krijgen tot het toetsen paneel testen, selecteert u **testen** in het bovenste paneel van de toepassing.
1. Voer een query in het tekstvak in en selecteer Enter.
1. De best overeenkomende antwoord in de kennisdatabase wordt geretourneerd als antwoord.

### <a name="clear-test-panel"></a>Deelvenster wissen testen

Als u alle ingevoerde test query's en de bijbehorende resultaten wilt wissen uit de test console, selecteert u **opnieuw beginnen** in de linkerbovenhoek van het test paneel.

### <a name="close-test-panel"></a>Deelvenster sluiten testen

Als u het test paneel wilt sluiten, selecteert u de knop **testen** opnieuw. Tijdens de Test-deelvenster geopend is, kunt u de Knowledge Base-inhoud niet bewerken.

### <a name="inspect-score"></a>Controleren van score

U kunt de details van het test resultaat bekijken in het deel venster inspecteren.

1.  Selecteer in het deel venster voor het testen van de schuif regelaar op **controleren** voor meer informatie over die reactie.

    ![Antwoorden controleren](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Het deel venster inspectie wordt weer gegeven. Het deelvenster bevat de scoring-doel, evenals de geïdentificeerde entiteiten boven. Het deelvenster toont het resultaat van de geselecteerde utterance.

### <a name="correct-the-top-scoring-answer"></a>Corrigeer de bovenkant scoring-antwoord

Als het bovenste Score antwoord onjuist is, selecteert u het juiste antwoord in de lijst en selecteert u **opslaan en trainen**.

![Corrigeer de bovenkant scoring-antwoord](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

U kunt andere vormen van een vraag toevoegen aan een bepaald antwoord. Typ de alternatieve vindt u antwoorden op in het tekstvak in en klikt u op enter om te toe te voegen. Selecteer **opslaan en trainen** om de updates op te slaan.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Een nieuw antwoord toevoegen

U kunt een nieuw antwoord toevoegen als een van de bestaande antwoorden die zijn afgestemd onjuist zijn of het antwoord niet in het knowledge base bestaat (geen goede overeenkomst gevonden in de KB).

Klik aan de onderkant van de lijst met antwoorden op het tekstvak om een nieuw antwoord in te voeren en druk op ENTER om het te voegen.

Selecteer **opslaan en trainen** om dit antwoord te behouden. Een nieuwe set met vraag-antwoord is nu is toegevoegd aan uw knowledge base.

> [!NOTE]
> Alle wijzigingen in uw kennis database worden alleen opgeslagen wanneer u op de knop **opslaan en trainen** drukt.

### <a name="test-the-published-knowledge-base"></a>De gepubliceerde kennis database testen

U kunt de gepubliceerde versie van de Knowledge Base testen in het test venster. Zodra u de KB hebt gepubliceerd, selecteert u het vak **gepubliceerde KB** en verzendt u een query om de resultaten van de gepubliceerde KB op te halen.

![Testen op basis van een gepubliceerde KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch test met hulp programma

Gebruik het hulp programma voor batch tests wanneer u het volgende wilt doen:

* beste antwoord en score voor een set vragen bepalen
* het verwachte antwoord voor een set met vragen valideren

Lees de [zelf studie](../Quickstarts/batch-testing.md) voor batch testen voor stapsgewijze instructies.

Batch tests worden meegeleverd met het hulp programma voor batch tests. Dit hulp programma is beschikbaar als een [zip-uitvoerbaar bestand](https://aka.ms/qnamakerbatchtestingtool) dat kan worden gedownload of als [ C# bron code](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Naslag documentatie over het hulp programma](../reference-tsv-format-batch-testing.md) is onder andere:

* het opdracht regel voorbeeld van het hulp programma
* de indeling voor bestanden voor TSV-invoer en-uitvoer

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase publiceren](./publish-knowledge-base.md)
