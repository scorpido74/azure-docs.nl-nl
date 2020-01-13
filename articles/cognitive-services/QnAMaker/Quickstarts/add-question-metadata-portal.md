---
title: 'Snelstartgids: vragen en antwoorden toevoegen in QnA Maker Portal'
titleSuffix: Azure Cognitive Services
description: Deze Quick Start laat zien hoe u vraag-en antwoord sets kunt toevoegen met meta gegevens zodat uw gebruikers het juiste antwoord op hun vraag kunnen vinden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: ae5e3481d51a27b05afdb334e6e04c785a68c01a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447677"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Snelstartgids: vragen toevoegen en antwoord geven met QnA Maker Portal

Zodra een Knowledge Base is gemaakt, voegt u vraag-en antwoord sets toe met meta gegevens zodat uw gebruikers het juiste antwoord op hun vraag kunnen vinden.

Het juiste antwoord is een enkel antwoord, maar er kunnen veel manieren zijn waarop een klant de vraag naar dat ene antwoord kan vragen.

De vragen in de volgende tabel zijn bijvoorbeeld ongeveer Azure-service limieten, maar ze moeten allemaal met een andere Azure-service worden uitgevoerd.

<a name="qna-table"></a>


|Set|Vragen|Antwoord|Metagegevens|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Zodra de meta gegevens zijn toegevoegd aan een vraag-en-antwoordset, kan de client toepassing het volgende doen:

* Vraag antwoorden die alleen met bepaalde meta gegevens overeenkomen.
* Ontvang alle antwoorden, maar pas na het proces de antwoorden af, afhankelijk van de meta gegevens voor elk antwoord.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een QnA Maker-service
* Een Knowledge Base die is gemaakt in die QnA Maker service

Beide zijn in de [eerste Snelstartgids](../how-to/create-knowledge-base.md)gemaakt.

## <a name="sign-in-to-the-qna-maker-portal"></a>Meld u aan bij de QnA Maker Portal

1. Meld u aan bij de [QnA Maker Portal](https://www.qnamaker.ai).

1. Selecteer uw bestaande Knowledge Base. Als u geen Knowledge Base hebt, keert u terug naar de [vorige Snelstartgids](../how-to/create-knowledge-base.md) en voltooit u de stappen om uw Knowledge Base te maken.

## <a name="add-additional-alternatively-phrased-questions"></a>Aanvullende vragen met een alternatieve formule toevoegen

De huidige Knowledge Base, van de [vorige Snelstartgids](../how-to/create-knowledge-base.md), bevat de QnA Maker vraag en antwoord sets voor het oplossen van problemen. Deze sets zijn gemaakt toen de URL werd toegevoegd aan de Knowledge Base tijdens het maken van het proces.

Wanneer deze URL is geïmporteerd, is er slechts één vraag gemaakt met één antwoord.

In deze procedure voegt u aanvullende vragen toe.

1. Gebruik op de pagina **bewerken** het tekstvak Zoeken boven de vraag-en-antwoord sets om de vraag te vinden `How large a knowledge base can I create?`

1. Selecteer in de kolom **vraag** **+ alternatieve formule ring** toevoegen en voeg vervolgens elke nieuwe formule ring toe, zoals in de volgende tabel.

    |Alternatieve formule ring|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecteer **opslaan en trainen** om de Knowledge Base opnieuw te trainen.

1. Selecteer **testen**en voer vervolgens een vraag in die zich op een van de nieuwe alternatieve frasen bevindt, maar dit is niet precies dezelfde formule:

    `What GB size can a knowledge base be?`

    Het juiste antwoord wordt geretourneerd in de indeling voor verkoop prijs: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Als u **controleren** selecteert onder het geretourneerde antwoord, ziet u dat er meer antwoorden aan de vraag zijn voldaan, maar niet met hetzelfde hoge vertrouwens niveau.

    Voeg niet elke mogelijke combi natie van alternatieve formule ring toe. Het [actieve leer proces](../how-to/improve-knowledge-base.md)van QnA Maker inschakelen Hiermee wordt gezocht naar de alternatieve frasen die het beste helpt uw kennis basis te halen aan de behoeften van uw gebruikers.

1. Selecteer **testen** opnieuw om het test venster te sluiten.

## <a name="add-metadata-to-filter-the-answers"></a>Meta gegevens toevoegen om de antwoorden te filteren

Door meta gegevens toe te voegen aan een vraag-en-antwoordset kan uw client toepassing gefilterde antwoorden aanvragen. Dit filter wordt toegepast voordat de [eerste en tweede rang orde](../concepts/knowledge-base.md#ranker-process) worden toegepast.

1. Voeg de tweede vraag en antwoordset, zonder de meta gegevens, toe uit de [eerste tabel in deze Snelstartgids](#qna-table)en ga daarna verder met de volgende stappen.

1. Selecteer **weergave opties**en selecteer vervolgens **meta gegevens weer geven**.

1. Voor de vraag en de antwoordset die u zojuist hebt toegevoegd, selecteert u **labels voor meta gegevens toevoegen**en vervolgens de naam `service` en waarde van `search`toevoegen `service:search`.

1. Voeg nog andere meta gegevenslabels toe met de naam `link_in_answer` en waarde `false`, `link_in_answer:false`.

1. Zoek het eerste antwoord in de tabel `How large a knowledge base can I create?`.
1. Voeg Meta gegevensparen toe voor dezelfde twee meta gegevenslabels:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    U hebt nu twee vragen met dezelfde meta gegevenslabels met verschillende waarden.

1. Selecteer **opslaan en trainen** om de Knowledge Base opnieuw te trainen.

1. Selecteer **publiceren** in het bovenste menu om naar de pagina publiceren te gaan.
1. Selecteer de knop **publiceren** om de huidige Knowledge Base te publiceren op een eind punt waarop query's kunnen worden uitgevoerd.
1. Nadat de Knowledge Base is gepubliceerd, selecteert u het tabblad **krul** om een voor beeld van een krul opdracht te zien die wordt gebruikt voor het genereren van een antwoord uit de Knowledge Base.
1. Kopieer de opdracht naar een notitie blok of een andere Bewerk bare omgeving zodat u de opdracht kunt bewerken. Bewerken voor uw eigen resource naam, Knowledge Base-ID en eindpunt sleutel:

    |Vervangen|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    U ziet dat de vraag slechts één woord is, `size`, waarmee een vraag en een antwoordset kunnen worden geretourneerd. De `strictFilters` matrix vertelt u het antwoord om alleen de `qna_maker`-antwoorden te verminderen.

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Het antwoord bevat alleen het antwoord dat voldoet aan de filter criteria.

    De volgende krul reactie is opgemaakt voor de Lees baarheid:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Als er een vraag-en-antwoordset is die niet voldoet aan de zoek term maar wel aan het filter voldoet, wordt de set niet geretourneerd. In plaats daarvan wordt het algemene antwoord `No good match found in KB.` geretourneerd.

    Zorg ervoor dat u de naam en waarde-paren van uw meta gegevens binnen de vereiste limieten houdt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Antwoord krijgen met behulp van Postman of krul](get-answer-from-knowledge-base-using-url-tool.md)