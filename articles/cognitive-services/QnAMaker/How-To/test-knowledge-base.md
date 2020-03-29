---
title: Hoe test je een kennisbank - QnA Maker
description: Het testen van uw QnA Maker kennisbank is een belangrijk onderdeel van een iteratief proces om de nauwkeurigheid van de antwoorden die worden geretourneerd te verbeteren. U de kennisbank testen via een verbeterde chat-interface waarmee u ook bewerkingen aanbrengen.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927263"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Test je kennisbank in QnA Maker

Het testen van uw QnA Maker kennisbank is een belangrijk onderdeel van een iteratief proces om de nauwkeurigheid van de antwoorden die worden geretourneerd te verbeteren. U de kennisbank testen via een verbeterde chat-interface waarmee u ook bewerkingen aanbrengen.

## <a name="interactively-test-in-qna-maker-portal"></a>Interactief testen in QnA Maker portal

1. Krijg toegang tot uw kennisbank door de naam te selecteren op de pagina **Mijn kennisbanken.**
1. Als u toegang wilt krijgen tot het deelvenster Dia-out testen, selecteert u **Testen** in het bovenste deelvenster van uw toepassing.
1. Voer een query in het tekstvak in en selecteer Enter.
1. Het best matched antwoord van de kennisbank wordt geretourneerd als antwoord.

### <a name="clear-test-panel"></a>Testpaneel wissen

Als u alle ingevoerde testquery's en hun resultaten van de testconsole wilt wissen, selecteert u **Opnieuw beginnen** in de linkerbovenhoek van het testpaneel.

### <a name="close-test-panel"></a>Testpaneel sluiten

Als u het deelvenster Testen wilt sluiten, selecteert u de knop **Testen** opnieuw. Terwijl het testpaneel is geopend, u de inhoud van de Knowledge Base niet bewerken.

### <a name="inspect-score"></a>Score inspecteren

U inspecteert de details van het testresultaat in het deelvenster Inspect.

1.  Als het deelvenster Proefdia-out is geopend, selecteert **u Inspecteren** voor meer informatie over dat antwoord.

    ![Reacties inspecteren](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Het inspectiepanel verschijnt. Het paneel bevat de intentie voor de hoogste score en alle geïdentificeerde entiteiten. Het deelvenster toont het resultaat van de geselecteerde utterance.

### <a name="correct-the-top-scoring-answer"></a>Het antwoord op de hoogste score corrigeren

Als het antwoord op de hoogste score onjuist is, selecteert u het juiste antwoord in de lijst en selecteert u **Opslaan en trainen**.

![Het antwoord op de hoogste score corrigeren](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen

U alternatieve vormen van een vraag aan een bepaald antwoord toevoegen. Typ de alternatieve antwoorden in het tekstvak en klik op Enter om ze toe te voegen. Selecteer **Opslaan en trainen** om de updates op te slaan.

![Alternatieve vragen toevoegen](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Een nieuw antwoord toevoegen

U een nieuw antwoord toevoegen als een van de bestaande antwoorden die zijn gematcht onjuist zijn of het antwoord niet bestaat in de kennisbank (geen goede overeenkomst gevonden in de KB).

Gebruik onder aan de lijst met antwoorden het tekstvak om een nieuw antwoord in te voeren en druk op enter om het toe te voegen.

Selecteer **Opslaan en trainen** om dit antwoord voort te zetten. Een nieuw vraag-antwoord paar is nu toegevoegd aan uw kennisbank.

> [!NOTE]
> Alle bewerkingen in uw kennisbank worden alleen opgeslagen wanneer u op de knop **Opslaan en trainen** drukt.

### <a name="test-the-published-knowledge-base"></a>Test de gepubliceerde kennisbank

U de gepubliceerde versie van knowledge base testen in het testvenster. Zodra u de KB hebt gepubliceerd, selecteert u het vak **Gepubliceerde KB** en verzendt u een query om resultaten van de gepubliceerde KB te krijgen.

![Test tegen een gepubliceerde KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batchtest met gereedschap

Gebruik het batchtestgereedschap wanneer u dat wilt:

* bepalen top antwoord en score voor een set van vragen
* het verwachte antwoord op reeks vragen valideren

Lees de [zelfstudie](../Quickstarts/batch-testing.md) voor batchtesten voor stapsgewijze instructies.

Batch testen is voorzien van de batch testen tool. Deze tool is beschikbaar als een [rits uitvoerbaar](https://aka.ms/qnamakerbatchtestingtool) voor download of als [C # broncode](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Referentiedocumentatie over het hulpprogramma](../reference-tsv-format-batch-testing.md) omvat:

* het opdrachtregelvoorbeeld van het gereedschap
* de indeling voor TSV-invoer- en outfilebestanden

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase publiceren](./publish-knowledge-base.md)
