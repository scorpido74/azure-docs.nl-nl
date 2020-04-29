---
title: 'Snelstartgids: vragen en antwoorden toevoegen in QnA Maker Portal'
description: Deze Quick Start laat zien hoe u vraag-en antwoord sets kunt toevoegen met meta gegevens zodat uw gebruikers het juiste antwoord op hun vraag kunnen vinden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756681"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Snelstartgids: vragen toevoegen en antwoord geven met QnA Maker Portal

Nadat u een Knowledge Base hebt gemaakt, voegt u QnA-sets (vraag en antwoord) met meta gegevens toe om het antwoord te filteren. De vragen in de volgende tabel zijn informatie over de limieten voor Azure-Services, maar moeten allemaal met een andere Azure-service.

<a name="qna-table"></a>

|Instellen|Vragen|Antwoord|Metagegevens|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Zodra meta gegevens worden toegevoegd aan een QnA-paar, kan de client toepassing het volgende doen:

* Vraag antwoorden die alleen met bepaalde meta gegevens overeenkomen.
* Ontvang alle antwoorden, maar pas na het proces de antwoorden af, afhankelijk van de meta gegevens voor elk antwoord.


## <a name="prerequisites"></a>Vereisten

* De [vorige Snelstartgids](./create-publish-knowledge-base.md) volt ooien

## <a name="sign-in-to-the-qna-maker-portal"></a>Meld u aan bij de QnA Maker Portal

1. Meld u aan bij de [QnA Maker Portal](https://www.qnamaker.ai).

1. Selecteer uw bestaande Knowledge Base in de [vorige Snelstartgids](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Aanvullende vragen met een alternatieve formule toevoegen

De huidige Knowledge Base heeft de QnA Maker het oplossen van problemen met QnA-paren. Deze sets zijn gemaakt toen de URL werd toegevoegd aan de Knowledge Base tijdens het maken van het proces.

Wanneer deze URL is geïmporteerd, is er slechts één vraag gemaakt met één antwoord. In deze procedure voegt u aanvullende vragen toe.

1. Gebruik op de pagina **bewerken** het tekstvak Zoeken boven de vraag-en-antwoord sets om de vraag te vinden`How large a knowledge base can I create?`

1. Selecteer in de kolom **vraag** **+ alternatieve formule ring** toevoegen en voeg vervolgens elke nieuwe formule ring toe, zoals in de volgende tabel.

    |Alternatieve formule ring|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecteer **opslaan en trainen** om de Knowledge Base opnieuw te trainen.

1. Selecteer **testen**en voer vervolgens een vraag in die zich op een van de nieuwe alternatieve frasen bevindt, maar dit is niet precies dezelfde formule:

    `What GB size can a knowledge base be?`

    Het juiste antwoord wordt geretourneerd in de indeling voor verkoop prijs:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Als u **controleren** selecteert onder het geretourneerde antwoord, ziet u dat er meer antwoorden aan de vraag zijn voldaan, maar niet met hetzelfde hoge vertrouwens niveau.

    Voeg niet elke mogelijke combi natie van alternatieve formule ring toe. Wanneer u het [actieve leer proces](../how-to/improve-knowledge-base.md)van QnA Maker inschakelt, vindt u hier de alternatieve frasen die het beste helpt uw kennis basis te voldoen aan de behoeften van uw gebruikers.

1. Selecteer **testen** opnieuw om het test venster te sluiten.

## <a name="add-metadata-to-filter-the-answers"></a>Meta gegevens toevoegen om de antwoorden te filteren

Door meta gegevens toe te voegen aan een vraag-en-antwoordset kan uw client toepassing gefilterde antwoorden aanvragen. Dit filter wordt toegepast voordat de [eerste en tweede rang orde](../concepts/query-knowledge-base.md#ranker-process) worden toegepast.

1. Voeg de tweede vraag en antwoordset, zonder de meta gegevens, toe uit de [eerste tabel in deze Snelstartgids](#qna-table)en ga daarna verder met de volgende stappen.

1. Selecteer **weergave opties**en selecteer vervolgens **meta gegevens weer geven**.

1. Voor het QnA-paar dat u zojuist hebt toegevoegd, selecteert u **Tags voor meta gegevens toevoegen**en vervolgens de `service` naam `search`en de waarde van toevoegen. Dit ziet er als volgt `service:search`uit:.

1. Voeg een andere meta gegevens code met `link_in_answer` de naam en `false`waarde van toe. Dit ziet er als volgt `link_in_answer:false`uit:.

1. Zoek het eerste antwoord in de tabel, `How large a knowledge base can I create?`.

1. Voeg Meta gegevensparen toe voor dezelfde twee meta gegevenslabels:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    U hebt nu twee vragen met dezelfde meta gegevenslabels met verschillende waarden.

1. Selecteer **opslaan en trainen** om de Knowledge Base opnieuw te trainen.

1. Selecteer **publiceren** in het bovenste menu om naar de pagina publiceren te gaan.
1. Selecteer de knop **publiceren** om de huidige Knowledge Base naar het eind punt te publiceren.
1. Nadat de Knowledge Base is gepubliceerd, gaat u door naar de volgende Snelstartgids voor meer informatie over het genereren van een antwoord uit uw Knowledge Base.

## <a name="what-did-you-accomplish"></a>Wat hebt u gedaan?

U hebt uw Knowledge Base bewerkt ter ondersteuning van meer vragen en opgegeven naam/waarde-paren om filters te ondersteunen tijdens het zoeken naar het bovenste antwoord of postprocessing, nadat het antwoord of de antwoorden zijn geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verdergaat met de volgende Snelstartgids, verwijdert u de QnA Maker-en bot Framework-resources in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Antwoord ophalen met behulp van Postman of cURL](get-answer-from-knowledge-base-using-url-tool.md)