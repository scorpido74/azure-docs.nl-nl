---
title: Importeren uit gegevens bronnen-QnA Maker
description: Een QnA Maker Knowledge Base bestaat uit een set vraag-en antwoord sets (QnA) en optionele meta gegevens die zijn gekoppeld aan elk QnA-paar.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804279"
---
# <a name="importing-from-data-sources"></a>Importeren uit gegevensbronnen

Een kennis database bestaat uit vraag-en antwoord sets in open bare Url's en bestanden.

## <a name="data-source-locations"></a>Gegevens bron locaties

Inhoud wordt in een Knowledge Base van een gegevens bron gebracht. Gegevens bron locaties zijn **open bare url's of bestanden**, waarvoor geen verificatie is vereist.

[Share Point-bestanden](../how-to/add-sharepoint-datasources.md)die zijn beveiligd met verificatie, zijn de uitzonde ring. Share point-resources moeten bestanden, niet webpagina's zijn. Als de URL eindigt met een webextensie, zoals. ASPX, wordt niet geïmporteerd in QnA Maker vanuit share point.

## <a name="chit-chat-content"></a>Chat inhoud Chit

De Chit Chat QnA-inhoudset wordt aangeboden als een volledige inhouds bron voor inhoud in verschillende talen en gespreks stijlen. Dit kan een begin punt zijn voor de persoonlijkheid van uw bot en bespaart u de tijd en kosten voor het schrijven van deze bestanden. Meer informatie [over hoe u](../how-to/chit-chat-knowledge-base.md) deze inhoud kunt toevoegen aan uw Knowledge Base.

## <a name="structured-data-format-through-import"></a>Gestructureerde gegevens indeling via importeren

Als u een Knowledge Base importeert, wordt de inhoud van de bestaande Knowledge Base vervangen. Voor het importeren is `.tsv` een gestructureerd bestand vereist dat vragen en antwoorden bevat. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord sets en het kenmerk hiervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactionele|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Indeling voor gestructureerde multi-draaiing via importeren

U kunt de multi-turn-conversaties maken `.tsv` in een bestands indeling. De indeling biedt u de mogelijkheid om de multi-turn-conversaties te maken door eerdere chat logboeken te analyseren (met andere processen, niet met QnA Maker) en `.tsv` vervolgens het bestand te maken via Automation. Importeer het bestand om de bestaande Knowledge Base te vervangen.

> [!div class="mx-imgBorder"]
> ![Conceptueel model van 3 niveaus van de multi-turn-vraag](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

De kolom voor een multi-turn `.tsv`, specifiek voor meerdere zetten, is een **prompt**. Een voor `.tsv`beeld, weer gegeven in Excel, toont de gegevens die moeten worden opgenomen om de multi-turn-kinderen te definiëren:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

De **display order** is numeriek en de **weergave** tekst is Text die geen prijs verlaging mag bevatten.

> [!div class="mx-imgBorder"]
> ![Voor beeld van een multi-turn-vraag zoals weer gegeven in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exporteren als voor beeld

Als u niet zeker weet hoe u uw QnA-paar in het `.tsv` bestand kunt vertegenwoordigen:
* Gebruik dit [Download bare voor beeld van github](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Of maak de set in de QnA Maker Portal, sla de Knowledge Base op en exporteer deze vervolgens naar een voor beeld van hoe de set wordt vertegenwoordigd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Levenscyclus van een knowledge base-ontwikkeling](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zie ook

Gebruik de QnA Maker [prijs informatie](../reference-markdown-format.md) om u te helpen bij het format teren van uw antwoorden.

[Overzicht van QnA Maker](../Overview/overview.md)

Een Knowledge Base maken en bewerken met:
* [REST-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Een antwoord genereren met:
* [REST-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
