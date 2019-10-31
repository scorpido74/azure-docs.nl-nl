---
title: Ondersteunde gegevens bronnen-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker worden automatisch de vraag antwoord paren geëxtraheerd uit semi-gestructureerde inhoud zoals Veelgestelde vragen, product handleidingen, richt lijnen, ondersteunings documenten en beleids regels die zijn opgeslagen als webpagina's, PDF-bestanden of MS Word-document bestanden. Inhoud kan ook worden toegevoegd aan de Knowledge Base vanuit gestructureerde QnA-inhouds bestanden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 39746032fd012de8e0868c3bc7f810cd8e780b68
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176374"
---
# <a name="data-sources-for-qna-maker-content"></a>Gegevens bronnen voor QnA Maker inhoud

QnA Maker worden automatisch de vraag antwoord paren geëxtraheerd uit semi-gestructureerde inhoud zoals Veelgestelde vragen, product handleidingen, richt lijnen, ondersteunings documenten en beleids regels die zijn opgeslagen als webpagina's, PDF-bestanden of MS Word-document bestanden. Inhoud kan ook worden toegevoegd aan de Knowledge Base vanuit gestructureerde QnA-inhouds bestanden. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Bestands-en URL-gegevens typen

De volgende tabel bevat een overzicht van de typen inhoud en bestands indelingen die door QnA Maker worden ondersteund.

|Brontype|Inhouds type| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Faq's<br> Product handleiding,<br> brochures<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Heap.|[Structured QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van product manual. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> Voor [beeld van semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van technisch document. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Voor beeld multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Structured QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|[Voor beeld van QnA FAQ. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Structured QnA-bestand|[Voor beeld van Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>De Knowledge Base importeren en exporteren

**TSV-en xls-bestanden**, van geëxporteerde kennis grondslagen, kunnen alleen worden gebruikt door het importeren van de bestanden van de pagina **instellingen** in de QnA Maker Portal. Ze kunnen niet worden gebruikt als gegevens bronnen tijdens het maken van een Knowledge Base of vanuit het onderdeel **bestand toevoegen** of **+ URL** toevoegen op de pagina **instellingen** . 

## <a name="data-source-locations"></a>Gegevens bron locaties

Gegevens bron locaties zijn **open bare url's of bestanden**, waarvoor geen verificatie is vereist. 

Als u verificatie voor uw gegevens bron nodig hebt, kunt u de volgende methoden voor het ophalen van die gegevens in QnA Maker:

* [Down load het bestand hand matig](#download-file-from-authenticated-data-source-location) en importeer het in QnA Maker
* Bestand importeren voor geverifieerde [share point-locatie](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Bestand downloaden van de locatie van de geverifieerde gegevens bron

Als u een geauthenticeerd bestand (niet op een geauthenticeerde share point-locatie) of URL hebt, kunt u het bestand downloaden van de geverifieerde site naar uw lokale computer en vervolgens het bestand van uw lokale computer toevoegen aan de Knowledge Base.

### <a name="import-file-from-authenticated-sharepoint"></a>Bestand importeren vanuit geverifieerde share point 

[Share point-gegevens bron locaties](../How-to/add-sharepoint-datasources.md) mogen geauthenticeerde **bestanden**bevatten. Share point-resources moeten bestanden, niet webpagina's zijn. Als de URL eindigt met een webextensie, zoals **. ASPX**, wordt niet geïmporteerd in QnA Maker vanuit share point.


## <a name="faq-urls"></a>Url's voor veelgestelde vragen

QnA Maker kunnen webpagina's met veelgestelde vragen over drie verschillende formulieren ondersteunen: pagina's met veelgestelde vragen, veelgestelde vragen pagina's met koppelingen, pagina's met veelgestelde vragen met een start pagina voor onderwerpen.

### <a name="plain-faq-pages"></a>Pagina's met veelgestelde vragen

Dit is het meest voorkomende type FAQ-pagina waarin de antwoorden direct op de vragen op dezelfde pagina volgen. 

Hieronder volgt een voor beeld van een pagina met veelgestelde vragen:

![Voor beeld van een pagina met veelgestelde vragen voor een kennis database](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Pagina met veelgestelde vragen met koppelingen 

In dit type FAQ-pagina worden vragen samen geaggregeerd en worden ze gekoppeld aan antwoorden die zich in verschillende delen van dezelfde pagina of op verschillende pagina's bevinden.

Hieronder volgt een voor beeld van een pagina met veelgestelde vragen met koppelingen in secties die zich op dezelfde pagina bevinden:

 ![Pagina met veelgestelde vragen over sectie koppelingen voor een Knowledge Base](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Pagina met veelgestelde vragen met een start pagina voor onderwerpen

Dit type Veelgestelde vragen bevat een start pagina met de onderwerpen waarbij elk onderwerp een koppeling naar de relevante QnAs is op een andere pagina. QnA Maker verkent hier alle gekoppelde pagina's om de bijbehorende vragen & antwoorden uit te pakken.

Hieronder ziet u een voor beeld van een pagina met veelgestelde vragen waar een Home page van een onderwerpen koppelingen bevat naar secties met veelgestelde vragen op verschillende pagina's. 

 ![Voor beeld van een diep gaande pagina Veelgestelde vragen voor een Knowledge Base](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Url's voor ondersteuning

QnA Maker kunt semi-gestructureerde ondersteunings webpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe u een bepaalde taak uitvoert, hoe u een bepaald probleem opspoort en oplost en wat de aanbevolen procedures voor een bepaald proces zijn. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

> [!NOTE]
> Uitpakken voor ondersteunings artikelen is een nieuwe functie en is in vroege stadia. Het werkt het beste voor eenvoudige pagina's, die goed zijn gestructureerd en geen complexe kop-en voet teksten bevatten.

![QnA Maker ondersteunt de extractie van semi-gestructureerde webpagina's waarbij een duidelijke structuur wordt weer gegeven met hiërarchische koppen](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC-bestanden

QnA Maker kunt semi-gestructureerde inhoud in een PDF-of DOC-bestand verwerken en deze converteren naar QnAs. Een goed bestand dat kan worden uitgepakt, is één waar inhoud is ingedeeld in een gestructureerd formulier en wordt weer gegeven in duidelijk gedefinieerde secties. De secties kunnen verder worden onderverdeeld in subsecties of subonderwerpen. Extractie werkt het beste bij documenten met een duidelijke structuur met hiërarchische koppen.

QnA Maker herkent secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen zoals teken grootte, teken stijl, nummering, kleuren enzovoort. Semi-gestructureerde PDF-of DOC-bestanden kunnen hand leidingen, veelgestelde vragen, richt lijnen, beleids regels, brochures, folders en vele andere typen bestanden zijn. Hieronder ziet u een aantal voorbeeld typen van deze bestanden.

### <a name="product-manuals"></a>Product handleidingen

Een hand leiding is doorgaans richt lijnen die bij een product worden geleverd. Het helpt de gebruiker bij het instellen, gebruiken, onderhouden en probleem oplossing van het product. Als QnA Maker een hand matige processen verwerkt, worden de koppen en subkoppen als vragen en de volgende inhoud als antwoorden geëxtraheerd. Bekijk [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)een voor beeld.

Hieronder ziet u een voor beeld van een hand leiding met een index pagina en hiërarchische inhoud

 ![Hand matig voor beeld van product voor een Knowledge Base](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extractie werkt het beste in hand boeken met een inhouds opgave en/of een index pagina en een duidelijke structuur met hiërarchische koppen.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richt lijnen, documenten en andere bestanden

Veel andere typen documenten kunnen ook worden verwerkt voor het genereren van QA-paren, mits ze een duidelijke structuur en indeling hebben. Dit zijn onder andere: brochures, richt lijnen, rapporten, White papers, weten schappelijke documenten, beleids regels, boeken, enzovoort. Bekijk [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)een voor beeld.

Hieronder ziet u een voor beeld van een semi-Structured doc, zonder een index:

 ![Semi-Structured document voor Azure Blob Storage](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Structured QnA-document

De indeling voor gestructureerde vraag-antwoorden in document bestanden, bevindt zich in de vorm van afwisselende vragen en antwoorden per regel, één vraag per regel gevolgd door het antwoord op de volgende regel, zoals hieronder wordt weer gegeven: 

```text
Question1

Answer1

Question2

Answer2
```

Hieronder ziet u een voor beeld van een gestructureerd QnA Word-document:

 ![Voor beeld van een Structured QnA-document voor een kennis database](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Gestructureerde *txt*-, *TSV* -en *xls* -bestanden

QnAs in de vorm van gestructureerde *. txt*-, *TSV* -of *xls* -bestanden kunnen ook worden geüpload naar QnA Maker om een Knowledge Base te maken of uit te breiden.  Dit kan onbewerkte tekst zijn of kan inhoud bevatten in RTF of HTML. 

| Vraag  | Antwoord  | Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuele aanvullende kolommen in het bron bestand worden genegeerd.

### <a name="example-of-structured-excel-file"></a>Voor beeld van een gestructureerd Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand met HTML-inhoud:

 ![Gestructureerd QnA Excel-voor beeld voor een Knowledge Base](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand, met verschillende alternatieve vragen voor één antwoord:

 ![Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nadat het bestand is geïmporteerd, bevindt het vraag-en antwoord paar zich in de Knowledge Base, zoals hieronder wordt weer gegeven:

 ![Scherm afbeelding van alternatieve vragen voor één antwoord dat in de Knowledge Base wordt geïmporteerd](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Gestructureerde gegevens indeling via importeren

Als u een Knowledge Base importeert, wordt de inhoud van de bestaande Knowledge Base vervangen. Voor het importeren is een Structured TSV-bestand met gegevens bron informatie vereist. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord paren en het kenmerk ervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactionele|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redactionel toevoegen aan de Knowledge Base

Als u geen vooraf bestaande inhoud hebt om de Knowledge Base te vullen, kunt u QnAs in QnA Maker Knowledge Base toevoegen. Meer informatie over hoe u uw kennis [database kunt](../How-To/edit-knowledge-base.md)bijwerken.

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Opmaak overwegingen

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n`. Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud. 

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen. 

Hieronder ziet u de lijst met indelingen voor prijs opgaven die u in QnA Maker kunt gebruiken: 

|Doel|Indeling|Voor beeld van prijs verlaging|Rendering<br>zoals weer gegeven in de chat-bot|
|--|--|--|--|
Een nieuwe regel tussen twee zinnen.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![nieuwe lijn tussen twee zinnen opmaken](../media/qnamaker-concepts-datasources/format-newline.png)|
|Headers van H1 tot H6, het aantal `#` geeft aan welke header er is. 1 `#` is de H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![indeling met kopteksten voor korting](../media/qnamaker-concepts-datasources/format-headers.png)<br>![notatie met de koppen H1 tot en met H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Cursief |`*text*`|`How do I create a bot with *QnA Maker*?`|![opmaken met cursief](../media/qnamaker-concepts-datasources/format-italics.png)|
|Teken reeks (vet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![indeling met sterke markering voor vet](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL voor koppeling|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![indeling voor URL (Hyper Link)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL voor de open bare afbeelding|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![indeling voor URL van open bare afbeelding ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Door|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![opmaak voor door halen](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Vet en cursief|`***text***`|`How can I create a ***QnA Maker*** bot?`|![opmaak voor vet en cursief](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Vetgedrukte URL voor koppeling|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![notatie voor vetgedrukte URL](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Cursieve URL voor koppeling|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![notatie voor cursieve URL](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symbolen voor kortings symbool voor Escape|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![notatie voor cursieve URL](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Geordende lijst|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>In het voor gaande voor beeld wordt gebruikgemaakt van automatische nummering in korting.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>In het vorige voor beeld wordt gebruikgemaakt van expliciete nummering.|![indeling voor geordende lijst](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Niet-geordende lijst|`\n * item1 \n * item2`<br>of<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![indeling voor niet-geordende lijst](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Geneste lijsten|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>U kunt bestelde en niet-geordende lijsten samen nesten. Het tabblad, `\t`, geeft het inspring niveau van het onderliggende element aan.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![indeling voor geneste niet-geordende lijst](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![indeling voor geneste geordende lijst](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker de afbeelding niet op enigerlei manier verwerken. Het is de rol van de client toepassing om de installatie kopie weer te geven. 

Als u inhoud wilt toevoegen met behulp van de Knowledge Base-Api's update/vervangen en de inhoud/het bestand bevat HTML-tags, kunt u de HTML in het bestand behouden door ervoor te zorgen dat het openen en sluiten van de tags in de gecodeerde indeling wordt geconverteerd.

| HTML behouden  | Vertegenwoordiging in de API-aanvraag  | Representatie in KB |
|-----------|---------|-------------------------|
| Ja | \&lt; br\&gt; | &lt;BR&gt; |
| Ja | \&lt; h3\&gt; header\&lt;/h3\&gt; | &lt;H3&gt;header&lt;/H3&gt; |

CR LF (\r\n) wordt ook geconverteerd naar \n in de KB. LF (\n) wordt bewaard. Als u een escape reeks wilt weglaten als een \t of \n, kunt u back slash gebruiken, bijvoorbeeld: '\\\\r\\\\n ' en '\\\\t '

## <a name="editing-your-knowledge-base-locally"></a>Uw kennis database lokaal bewerken

Zodra een Knowledge Base is gemaakt, wordt het aanbevolen dat u wijzigingen aanbrengt in de Knowledge Base-tekst in de [QnA Maker Portal](https://qnamaker.ai)in plaats van het exporteren en opnieuw importeren via lokale bestanden. Het is echter mogelijk dat u een kennis database lokaal moet bewerken. 

Exporteer de Knowledge Base van de pagina **instellingen** en bewerk vervolgens de Knowledge Base met micro soft Excel. Als u ervoor kiest om een andere toepassing te gebruiken om het geëxporteerde TSV-bestand te bewerken, kan de toepassing syntaxis fouten veroorzaken omdat deze niet volledig TSV-compatibel is. De TSV-bestanden van micro soft Excel voeren doorgaans geen opmaak fouten uit. 

Wanneer u klaar bent met uw bewerkingen, importeert u het TSV-bestand opnieuw vanaf de pagina **instellingen** . Hiermee wordt de huidige Knowledge Base volledig vervangen door de geïmporteerde kennis database. 

## <a name="testing-your-markdown"></a>Uw prijs verlaging testen

Gebruik de **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -zelf studie om uw prijs verlaging te valideren. De zelf studie heeft een **try it** -functie voor het snel kopiëren/plakken van validatie. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versie beheer voor gegevens in uw Knowledge Base

Versie beheer voor gegevens wordt gegeven via de [functie voor importeren/exporteren](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) op de pagina **instellingen** . 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een QnA Maker-service instellen](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zie ook 

[Overzicht van QnA Maker](../Overview/overview.md)
