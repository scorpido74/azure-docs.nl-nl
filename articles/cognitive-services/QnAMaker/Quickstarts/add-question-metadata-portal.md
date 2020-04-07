---
title: 'Snelstart: Vragen en antwoorden toevoegen in QnA Maker portal'
description: Deze quickstart laat zien hoe u vraag- en antwoordsets toevoegen met metagegevens, zodat uw gebruikers het juiste antwoord op hun vraag kunnen vinden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756681"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Snelstart: Voeg vragen toe en beantwoord met QnA Maker portal

Zodra een kennisbank is gemaakt, voegt u vraag- en antwoordsets (QnA) toe met metadata om het antwoord te filteren. De vragen in de volgende tabel gaan over Azure-servicelimieten, maar elk heeft te maken met een andere Azure-service.

<a name="qna-table"></a>

|Instellen|Vragen|Antwoord|Metagegevens|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Zodra metagegevens aan een QnA-paar zijn toegevoegd, kan de clienttoepassing:

* Vraag antwoorden aan die alleen overeenkomen met bepaalde metagegevens.
* Ontvang alle antwoorden, maar na het verwerken van de antwoorden, afhankelijk van de metadata voor elk antwoord.


## <a name="prerequisites"></a>Vereisten

* De [vorige quickstart voltooien](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Meld u aan bij de QnA Maker-portal

1. Meld u aan bij de [QnA Maker portal.](https://www.qnamaker.ai)

1. Selecteer uw bestaande kennisbank uit de [vorige quickstart.](../how-to/create-knowledge-base.md)

## <a name="add-additional-alternatively-phrased-questions"></a>Aanvullende vragen met alternatieve zinnen toevoegen

De huidige kennisbank heeft de QnA Maker probleemoplossing QnA paren. Deze sets zijn gemaakt toen de URL tijdens het maken aan de kennisbasis werd toegevoegd.

Toen deze URL werd geïmporteerd, werd slechts één vraag met één antwoord gemaakt. Voeg in deze procedure aanvullende vragen toe.

1. Gebruik op de pagina **Bewerken** het tekstvak zoeken boven de vraag- en antwoordsets om de vraag te vinden`How large a knowledge base can I create?`

1. Selecteer **in de** kolom Vraag de optie **+ Alternatieve formulering toevoegen** en voeg vervolgens elke nieuwe formulering toe, in de volgende tabel.

    |Alternatieve formulering|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecteer **Opslaan en trainen** om de kennisbank om te scholen.

1. Selecteer **Testen**en voer vervolgens een vraag in die dicht bij een van de nieuwe alternatieve formuleringen ligt, maar niet precies dezelfde formulering is:

    `What GB size can a knowledge base be?`

    Het juiste antwoord wordt geretourneerd in markdown-indeling:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Als u **Inspect** selecteert onder het geretourneerde antwoord, ziet u meer antwoorden op de vraag, maar niet met hetzelfde hoge niveau van vertrouwen.

    Voeg niet elke mogelijke combinatie van alternatieve formuleringtoe. Wanneer u het [actieve leren](../how-to/improve-knowledge-base.md)van QnA Maker inschakelt, vindt dit de alternatieve formuleringen die uw kennisbank het beste helpen om aan de behoeften van uw gebruikers te voldoen.

1. Selecteer Opnieuw **testen** om het testvenster te sluiten.

## <a name="add-metadata-to-filter-the-answers"></a>Metagegevens toevoegen om de antwoorden te filteren

Door metagegevens toe te voegen aan een vraag- en antwoordset kan uw clienttoepassing gefilterde antwoorden aanvragen. Dit filter wordt toegepast voordat de [eerste en tweede rankers](../concepts/query-knowledge-base.md#ranker-process) worden toegepast.

1. Voeg de tweede vraag- en antwoordset, zonder de metagegevens, toe vanaf de [eerste tabel in deze quickstart](#qna-table)en ga vervolgens verder met de volgende stappen.

1. Selecteer **Weergaveopties**en selecteer **Metagegevens weergeven**.

1. Voor het QnA-paar dat u zojuist hebt toegevoegd, `service` selecteert u `search` **Metadatatags toevoegen**en voegt u vervolgens de naam en de waarde van . Het ziet er `service:search`als volgt uit: .

1. Voeg nog een metadatatag toe met naam van `link_in_answer` en waarde van `false`. Het ziet er `link_in_answer:false`als volgt uit: .

1. Zoek naar het eerste antwoord `How large a knowledge base can I create?`in de tabel, .

1. Metagegevensparen toevoegen voor dezelfde twee metagegevenstags:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Je hebt nu twee vragen met dezelfde metadata tags met verschillende waarden.

1. Selecteer **Opslaan en trainen** om de kennisbank om te scholen.

1. Selecteer **Publiceren** in het bovenste menu om naar de publicatiepagina te gaan.
1. Selecteer de knop **Publiceren** om de huidige kennisbasis naar het eindpunt te publiceren.
1. Nadat de kennisbank is gepubliceerd, ga je door naar de volgende quickstart om te leren hoe je een antwoord uit je kennisbank genereren.

## <a name="what-did-you-accomplish"></a>Wat heb je bereikt?

U hebt uw kennisbasis bewerkt om meer vragen te ondersteunen en hebt naam/waardeparen verstrekt om het filteren te ondersteunen tijdens het zoeken naar het bovenste antwoord of de postprocessing, nadat het antwoord of de antwoorden zijn geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet doorgaat naar de volgende quickstart, verwijdert u de QnA Maker- en Bot-frameworkbronnen in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Antwoord ophalen met behulp van Postman of cURL](get-answer-from-knowledge-base-using-url-tool.md)