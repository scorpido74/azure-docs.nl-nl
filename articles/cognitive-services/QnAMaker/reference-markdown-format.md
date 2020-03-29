---
title: Markdown-indeling - QnA Maker
description: Hieronder volgt de lijst met afwaarderingsnotaties die u gebruiken in de antwoordtekst van QnA Maker.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045397"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Markdown-indeling ondersteund in antwoordtekst van QnA Maker

QnA Maker slaat antwoordtekst op als afwaardering. Er zijn vele smaken van afwaardering. Gebruik deze verwijzing om ervoor te zorgen dat de antwoordtekst correct wordt geretourneerd en weergegeven.

Gebruik de **[commonmark-zelfstudie](https://commonmark.org/help/tutorial/index.html)** om uw markdown te valideren. De tutorial heeft een **Try it-functie** voor snelle copy/paste validatie.

## <a name="supported-markdown-format"></a>Ondersteunde afwaarderingsnotatie

Hieronder volgt de lijst met afwaarderingsnotaties die u gebruiken in de antwoordtekst van QnA Maker.

|Doel|Indeling|Voorbeeld voor Markdown|Rendering<br>zoals weergegeven in chatbot|
|--|--|--|--|
Een nieuwe lijn tussen 2 zinnen.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![nieuwe regel opmaken tussen twee zinnen](./media/qnamaker-concepts-datasources/format-newline.png)|
|Kopteksten van h1 naar h6, het aantal `#` geeft aan welke koptekst. 1 `#` is de h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![opmaak met afwaarderingskoppen](./media/qnamaker-concepts-datasources/format-headers.png)<br>![opmaak met markdownheaders H1 tot en met H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Cursief |`*text*`|`How do I create a bot with *QnA Maker*?`|![formaat met cursief](./media/qnamaker-concepts-datasources/format-italics.png)|
|Sterk (vet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formaat met sterke markering voor vet](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL voor koppeling|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![opmaak voor URL (hyperlink)](./media/qnamaker-concepts-datasources/format-url.png)|
|*URL voor openbare afbeelding|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![opmaak voor URL van openbare afbeeldingen ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Doorhalen|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![indeling voor doorhalen](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Vet en cursief|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formaat voor vet en cursief](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Vette URL voor koppeling|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![opmaak voor vette URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Cursief URL voor link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![indeling voor cursief-URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape markdown symbolen|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![indeling voor cursief-URL](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Geordende lijst|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>In het voorgaande voorbeeld wordt gebruik gemaakt van automatische nummering die is ingebouwd in de afwaardering.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>In het voorgaande voorbeeld wordt expliciete nummering gebruikt.|![opmaak voor geordende lijst](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Ongeordende lijst|`\n * item1 \n * item2`<br>of<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![opmaak voor niet-geordende lijst](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Geneste lijsten|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>U bestelde en ongeordende lijsten samen nesten. Het tabblad `\t`, geeft het inspringingsniveau van het onderliggende element aan.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![opmaak voor geneste niet-geordende lijst](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![opmaak voor geneste geordende lijst](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker verwerkt de afbeelding op geen enkele manier. Het is de rol van de clienttoepassing om de afbeelding weer te geven.

Als u inhoud wilt toevoegen met behulp van update/vervanging van knowledge base API's en de inhoud/bestand html-tags bevat, u de HTML in uw bestand behouden door ervoor te zorgen dat het openen en sluiten van de tags worden geconverteerd in de gecodeerde indeling.

| HTML behouden  | Vertegenwoordiging in de API-aanvraag  | Vertegenwoordiging in KB |
|-----------|---------|-------------------------|
| Ja | \&lt;br\&gt; | &lt;Br&gt; |
| Ja | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3-kop&gt;-h3&lt;&gt; |

Bovendien wordt CR LF(\r\n) geconverteerd naar \n in de KB. LF(\n) wordt gehouden zoals het is. Als u wilt ontsnappen aan een ontsnappingssequentie zoals een \t of\\\\\n, u backslash gebruiken, bijvoorbeeld: '\\\\r n' en '\\\\t'

## <a name="next-steps"></a>Volgende stappen

[Bestandsindelingen](reference-tsv-format-batch-testing.md)voor batchtesten controleren .
