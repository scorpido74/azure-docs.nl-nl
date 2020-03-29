---
title: Importeren uit gegevensbronnen - QnA Maker
description: Een QnA Maker knowledge base bestaat uit een set vraag-en-antwoordsets (QnA) en optionele metadata die aan elk QnA-paar zijn gekoppeld.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457818"
---
# <a name="importing-from-data-sources"></a>Importeren uit gegevensbronnen

Een kennisbank bestaat uit vraag- en antwoordsets die worden ingebracht door openbare URL's en bestanden.

## <a name="data-source-locations"></a>Gegevensbronlocaties

Inhoud wordt vanuit een gegevensbron in een kennisbank gebracht. Gegevensbronlocaties zijn **openbare URL's of bestanden,** waarvoor geen verificatie vereist is.

[SharePoint-bestanden](../how-to/add-sharepoint-datasources.md), beveiligd met authenticatie, zijn de uitzondering. SharePoint-bronnen moeten bestanden zijn, geen webpagina's. Als de URL eindigt met een webextensie, zoals . ASPX, het zal niet importeren in QnA Maker van SharePoint.

## <a name="chit-chat-content"></a>Chit chat-inhoud

De Chit chat QnA content set wordt aangeboden als een volledige content data bron in verschillende talen en conversationele stijlen. Dit kan een uitgangspunt voor de persoonlijkheid van uw bot, en het bespaart u de tijd en de kosten van het schrijven van hen vanaf nul. Meer informatie over [het toevoegen van](../how-to/chit-chat-knowledge-base.md) deze inhoudsset aan uw kennisbank.

## <a name="structured-data-format-through-import"></a>Gestructureerde gegevensindeling via importeren

Het importeren van een kennisbank vervangt de inhoud van de bestaande kennisbank. Voor importeren `.tsv` is een gestructureerd bestand vereist dat vragen en antwoorden bevat. Met deze informatie kan QnA Maker de vraagbeantwoordingssets groeperen en toeschrijven aan een bepaalde gegevensbron.

| Vraag  | Antwoord  | Bron| Metagegevens (1 toets: 1 waarde) |
|-----------|---------|----|---------------------|
| Vraag1 | Antwoord1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Vraag2 | Antwoord2 | Redactionele|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Gestructureerde multi-turn-indeling via importeren

U de multi-turn `.tsv` gesprekken maken in een bestandsindeling. De indeling biedt u de mogelijkheid om de multi-turn gesprekken te maken door het analyseren van eerdere `.tsv` chat logs (met andere processen, niet met behulp van QnA Maker), vervolgens het bestand te maken door middel van automatisering. Importeer het bestand om de bestaande kennisbank te vervangen.

> [!div class="mx-imgBorder"]
> ![Conceptueel model van 3 niveaus van multi-turn vraag](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

De kolom voor een `.tsv`multi-turn , specifiek voor multi-turn is **Prompts**. In `.tsv`een voorbeeld , weergegeven in Excel, wordt de informatie weergegeven die moet worden opgenomen om de kinderen met meerdere beurten te definiëren:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

De **weergaveVolgorde** is numeriek en de **displayText** is tekst die geen afwaardering mag bevatten.

> [!div class="mx-imgBorder"]
> ![Voorbeeld van vragen met meerdere beurten zoals weergegeven in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exporteren als voorbeeld

Als u niet zeker weet hoe u uw `.tsv` QnA-set in het bestand weergeven:
* Gebruik dit [downloadbare voorbeeld van GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Of maak de set in de QnA Maker-portal, sla op en exporteer de kennisbank voor een voorbeeld van hoe u de set weergeven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Levenscyclus van een knowledge base-ontwikkeling](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zie ook

Gebruik de QnA Maker [Markdown-verwijzing](../reference-markdown-format.md) om u te helpen uw antwoorden op te maken.

[Overzicht van QnA Maker](../Overview/overview.md)

Maak en bewerk een kennisbank met:
* [REST-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Genereer een antwoord met:
* [REST-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
