---
title: Ontwerp kennisbank - QnA Maker
description: Een QnA Maker knowledge base bestaat uit een set vraag-en-antwoordsets (QnA) en optionele metadata die aan elk QnA-paar zijn gekoppeld.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844312"
---
# <a name="question-and-answer-set-concepts"></a>Concepten voor vraag- en antwoordreeksen

Een kennisbank bestaat uit vraag en antwoord (QnA) sets.  Elke set heeft één antwoord en een set bevat alle informatie die aan dat _antwoord_is gekoppeld. Een antwoord kan losjes lijken op een databaserij of een instantie van gegevensstructuur.

## <a name="question-and-answer-sets"></a>Vraag-en-antwoord-sets

De **vereiste** instellingen in een vraag-en-antwoord (QnA) set zijn:

* een **vraag** - tekst van de gebruiker query, gebruikt om Machine-learning QnA Maker's, om af te stemmen met de tekst van de vraag van de gebruiker met verschillende formulering, maar hetzelfde antwoord
* het **antwoord** - het antwoord van de set is het antwoord dat wordt geretourneerd wanneer een gebruikersquery wordt gekoppeld aan de bijbehorende vraag

Elke set wordt vertegenwoordigd door een **ID**.

De **optionele** instellingen voor een set zijn:

* **Alternatieve vormen van de vraag** - dit helpt QnA Maker terug te keren het juiste antwoord voor een breder scala van vraag formuleringen
* **Metagegevens:** Metagegevens zijn tags die zijn gekoppeld aan een QnA-paar en worden weergegeven als sleutelwaardeparen. Metagegevenstags worden gebruikt om QnA-paren te filteren en de set te beperken waarover querymatching wordt uitgevoerd.
* **Multi-turn prompts**, gebruikt om een multi-turn gesprek voort te zetten

![Kennisbanken van QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redactioneel toe te voegen aan de kennisbank

Als u geen reeds bestaande inhoud hebt om de kennisbasis te vullen, u QnA-sets redactioneel toevoegen in de QnA Maker-portal. Hier leest u hoe u uw kennisbank [kunt bijwerken.](../How-To/edit-knowledge-base.md)

## <a name="editing-your-knowledge-base-locally"></a>Uw kennisbank lokaal bewerken

Zodra een kennisbank is gemaakt, wordt aanbevolen dat u bewerkingen aanbrengt in de knowledge base-tekst in de [QnA Maker-portal,](https://qnamaker.ai)in plaats van lokale bestanden te exporteren en opnieuw te importeren. Er kunnen echter momenten zijn dat u een kennisbank lokaal moet bewerken.

Exporteer de kennisbank vanaf de pagina **Instellingen en** bewerk de kennisbank met Microsoft Excel. Als u ervoor kiest een andere toepassing te gebruiken om uw geëxporteerde bestand te bewerken, kan de toepassing syntaxisfouten introduceren omdat deze niet volledig compatibel is met TSV. De TSV-bestanden van Microsoft Excel leiden over het algemeen niet tot opmaakfouten.

Zodra u klaar bent met uw bewerkingen, importeert u het TSV-bestand opnieuw van de pagina **Instellingen.** Dit zal de huidige kennisbank volledig vervangen door de geïmporteerde kennisbank.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Knowledge base lifecycle in QnA Maker](./development-lifecycle-knowledge-base.md)