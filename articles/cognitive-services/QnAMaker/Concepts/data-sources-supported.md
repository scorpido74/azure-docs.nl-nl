---
title: Gegevensbronnen ondersteund - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker worden vraag-antwoord paren automatisch geëxtraheerd uit semi-gestructureerde inhoud zoals veelgestelde vragen over, producthandleidingen, richtlijnen, voor ondersteuningsdocumenten en beleidsregels die zijn opgeslagen als webpagina's, PDF-bestanden of bestanden van Microsoft Word-document. Inhoud kan ook worden toegevoegd aan de knowledge base van gestructureerde QnA inhoudsbestanden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: diberry
ms.openlocfilehash: 4e24246ec4ed30ec93bf8e113d659bc5e3600913
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130118"
---
# <a name="data-sources-for-qna-maker-content"></a>Gegevensbronnen voor QnA Maker-inhoud

QnA Maker worden vraag-antwoord paren automatisch geëxtraheerd uit semi-gestructureerde inhoud zoals veelgestelde vragen over, producthandleidingen, richtlijnen, voor ondersteuningsdocumenten en beleidsregels die zijn opgeslagen als webpagina's, PDF-bestanden of bestanden van Microsoft Word-document. Inhoud kan ook worden toegevoegd aan de knowledge base van gestructureerde QnA inhoudsbestanden. 

## <a name="data-types"></a>Gegevenstypen

De onderstaande tabel bevat een overzicht van de typen inhoud en bestandsindelingen die worden ondersteund door de QnA Maker.

|Brontype|Inhoudstype| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC-BESTAND|Faq's<br> Product handleiding,<br> Brochures,<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Heap.|[Structured QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van product manual. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> Voor [beeld van semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Voor beeld van technisch document. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Gestructureerd QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|[Voorbeeld QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Gestructureerd QnA-bestand|[Voorbeeld chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Gegevensbronnen

Gegevens bron locaties zijn **open bare url's of bestanden**, waarvoor geen verificatie is vereist. 

Als u verificatie voor uw gegevens bron nodig hebt, kunt u de volgende methoden voor het ophalen van die gegevens in QnA Maker:

* [Down load het bestand hand matig](#download-file-from-authenticated-data-source-location) en importeer het in QnA Maker
* Bestand importeren voor geverifieerde [share point-locatie](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Bestand downloaden van de locatie van de geverifieerde gegevens bron

Als u een geauthenticeerd bestand (niet op een geauthenticeerde share point-locatie) of URL hebt, kunt u het bestand downloaden van de geverifieerde site naar uw lokale computer en vervolgens het bestand van uw lokale computer toevoegen aan de Knowledge Base.

### <a name="import-file-from-authenticated-sharepoint"></a>Bestand importeren vanuit geverifieerde share point 

[Share point-gegevens bron locaties](../How-to/add-sharepoint-datasources.md) mogen geauthenticeerde **bestanden**bevatten. Share point-resources moeten bestanden, niet webpagina's zijn. Als de URL eindigt met een webextensie, zoals **. ASPX**, wordt niet geïmporteerd in QnA Maker vanuit share point.


## <a name="faq-urls"></a>Veelgestelde vragen over URL 's

QnA Maker kunnen webpagina's met veelgestelde vragen in drie verschillende vormen ondersteunen: Pagina's met veelgestelde vragen, veelgestelde vragen pagina's met koppelingen, pagina's met veelgestelde vragen met een start pagina voor onderwerpen.

### <a name="plain-faq-pages"></a>Plain Veelgestelde vragen over pagina 's

Dit is de meest voorkomende type van de pagina met veelgestelde vragen, waarin de antwoorden onmiddellijk de vragen in dezelfde pagina volgen. 

Hieronder volgt een voorbeeld van een eenvoudige pagina met veelgestelde vragen:

![Plain Veelgestelde vragen over pagina-voorbeeld voor een knowledge base](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Veelgestelde vragen over pagina's met koppelingen 

In dit type pagina met veelgestelde vragen, vragen bij elkaar worden opgeteld en antwoorden die in andere gedeelten van dezelfde pagina of in verschillende pagina's zijn gekoppeld.

Hieronder volgt een voorbeeld van een pagina met koppelingen in de secties die zich op dezelfde pagina met veelgestelde vragen:

 ![Voorbeeld van sectie koppeling Veelgestelde vragen over de pagina voor een knowledge base](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Veelgestelde vragen over pagina's met een startpagina onderwerpen

Dit type Veelgestelde vragen over heeft een startpagina bij met de onderwerpen waarin elk onderwerp is een koppeling naar de relevante vragen en antwoorden supereenvoudig in een andere pagina. Hier wordt de gekoppelde's om op te halen van de bijbehorende vragen en antwoorden verkend, QnA Maker.

Hieronder volgt een voorbeeld van een pagina met veelgestelde vragen waar een startpagina onderwerpen koppelingen naar Veelgestelde vragen over secties in verschillende pagina's bevat. 

 ![Voorbeeld van veelgestelde vragen over de dieptekoppeling naar de pagina voor een knowledge base](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Url's voor ondersteuning

QnA Maker kunt semi-gestructureerde ondersteunings webpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe u een bepaalde taak uitvoert, hoe u een bepaald probleem opspoort en oplost en wat de aanbevolen procedures voor een bepaald proces zijn. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

> [!NOTE]
> Uitpakken voor ondersteunings artikelen is een nieuwe functie en is in vroege stadia. Het werkt het beste voor eenvoudige pagina's, die goed zijn gestructureerd en geen complexe kop-en voet teksten bevatten.

![QnA Maker ondersteunt de extractie van semi-gestructureerde webpagina's waarbij een duidelijke structuur wordt weer gegeven met hiërarchische koppen](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / DOC-bestand

QnA Maker kunt semi-gestructureerde inhoud in een PDF-bestand of document bestand verwerken, en deze converteren naar vragen en antwoorden supereenvoudig. Een goede-bestand dat goed kan worden geëxtraheerd is een waar inhoud wordt georganiseerd in een gestructureerde vorm en wordt weergegeven in de goed gedefinieerde secties. De secties kunnen verder worden onderverdeeld in subsecties of subonderwerpen. Extractie werkt het beste op documenten waarvoor een duidelijke structuur met hiërarchische koppen.

QnA Maker geeft secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen, zoals lettertype, grootte, tekenstijl nummering, kleuren, enzovoort. Semi-gestructureerde PDF-bestand of document bestanden mogelijk handleidingen, veelgestelde vragen over de richtlijnen, beleid, Brochures, folders en vele andere typen bestanden. Hieronder ziet u enkele documentbeveiligingsrisico voorbeeld van deze bestanden.

### <a name="product-manuals"></a>Producthandleidingen

Een handmatige is doorgaans richtlijnen materiaal die wordt meegestuurd met een product. Hiermee wordt de gebruiker wilt instellen, gebruikt, onderhouden en oplossen van het product. Wanneer de QnA Maker een handmatige processen, pakt deze de koppen en subkoppen als vragen en de volgende inhoud als antwoord. Bekijk een voorbeeld [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Hieronder volgt een voorbeeld van een handmatige met een index-pagina en hiërarchische inhoud

 ![Product handmatige voorbeeld voor een knowledge base](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extractie werkt het beste op de handleidingen die een tabel met inhoud en/of een indexpagina en een duidelijke structuur met hiërarchische koppen hebben.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richtlijnen, documenten en andere bestanden

Nog vele andere typen documenten kunnen ook worden verwerkt voor het genereren van QA-paren, mits ze een duidelijke structuur en indeling hebben. Deze omvatten: Brochures, richt lijnen, rapporten, White papers, weten schappelijke documenten, beleids regels, boeken, enzovoort. Bekijk een voorbeeld [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Hieronder volgt een voorbeeld van een semi-gestructureerde doc, zonder een index:

 ![Azure Blob-opslag semi-gestructureerde doc-bestand](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Gestructureerde QnA-Document

De notatie voor gestructureerde-antwoorden op beveiligingsvragen in DOC-bestand is in de vorm van wisselende vragen en antwoorden per regel één vraag per regel gevolgd door een antwoord in de volgende regel, zoals hieronder wordt weergegeven: 

```text
Question1

Answer1

Question2

Answer2
```

Hieronder volgt een voorbeeld van een gestructureerde QnA word-document:

 ![Gestructureerde QnA-document is een voorbeeld van een kennisdatabase](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Gestructureerde *TXT*, *TSV* en *XLS* bestanden

Vragen en antwoorden supereenvoudig in de vorm van gestructureerde *.txt*, *.tsv* of *xls* bestanden kunnen ook worden geüpload naar de QnA Maker te maken of uitbreiden van een kennisdatabase.  Dit kunnen bestaan uit tekst zonder opmaak of inhoud in de RTF- of HTML-code kunnen hebben. 

| Vraag  | Antwoord  | Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuele extra kolommen in het bronbestand worden genegeerd.

### <a name="example-of-structured-excel-file"></a>Voor beeld van een gestructureerd Excel-bestand

Hieronder volgt een voorbeeld van een gestructureerde QnA *xls* bestand met de HTML-inhoud:

 ![Voorbeeld van een kennisdatabase gestructureerde QnA in excel](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand, met verschillende alternatieve vragen voor één antwoord:

 ![Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nadat het bestand is geïmporteerd, bevindt het vraag-en antwoord paar zich in de Knowledge Base, zoals hieronder wordt weer gegeven:

 ![Scherm afbeelding van alternatieve vragen voor één antwoord dat in de Knowledge Base wordt geïmporteerd](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Indeling van gestructureerde gegevens via importeren

Importeren van een kennisdatabase wordt vervangen door de inhoud van de bestaande knowledge base. Invoer vereist een gestructureerde .tsv-bestand met informatie van de gegevensbron. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord paren en het kenmerk ervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Source| Meta gegevens (1 sleutel: 1 waarde) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactioneel|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redactioneel in knowledge base toevoegen

Als u geen bestaande inhoud voor het vullen van de knowledge base, kunt u vragen en antwoorden supereenvoudig redactioneel toevoegen in de QnA Maker Knowledge base. Meer informatie over het bijwerken van uw knowledge base [hier](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Overwegingen voor opmaak

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n`. Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud. 

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen. 

Hieronder ziet u de lijst met indelingen voor prijs opgaven die u in QnA Maker kunt gebruiken: 

|Doel|Indeling|Voor beeld van prijs verlaging|Rendering<br>zoals weer gegeven in de chat-bot|
|--|--|--|--|
Een nieuwe regel tussen twee zinnen.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![nieuwe lijn tussen twee zinnen opmaken](../media/qnamaker-concepts-datasources/format-newline.png)|
|Headers van H1 tot H6, het aantal `#` geeft aan welke koptekst u wilt. 1 `#` is de H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![indeling met kopteksten voor korting](../media/qnamaker-concepts-datasources/format-headers.png)<br>![notatie met de koppen H1 tot en met H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Cursief |`*text*`|`How do I create a bot with *QnA Maker*?`|![opmaken met cursief](../media/qnamaker-concepts-datasources/format-italics.png)|
|Teken reeks (vet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![indeling met sterke markering voor vet](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL voor koppeling|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![indeling voor URL (Hyper Link)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL voor de open bare afbeelding|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![indeling voor URL van open bare afbeelding ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Doorhalen|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![opmaak voor door halen](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
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
| Ja | \&lt; br\&gt; | &lt;uitproberen&gt; |
| Ja | \&lt; h3\&gt; koptekst\&lt;/h3\&gt; | &lt;H3&gt;header&lt;/H3&gt; |

CR LF (\r\n) wordt ook geconverteerd naar \n in de KB. LF (\n) wordt bewaard. Als u een escape reeks als een \t of \n wilt escapepen, kunt u back slash gebruiken, bijvoorbeeld:\\'\\\\r\\n ' en\\'\\t '

## <a name="editing-your-knowledge-base-locally"></a>Uw kennis database lokaal bewerken

Zodra een Knowledge Base is gemaakt, wordt het aanbevolen dat u wijzigingen aanbrengt in de Knowledge Base-tekst in de [QnA Maker Portal](https://qnamaker.ai)in plaats van het exporteren en opnieuw importeren via lokale bestanden. Het is echter mogelijk dat u een kennis database lokaal moet bewerken. 

Exporteer de Knowledge Base van de pagina **instellingen** en bewerk vervolgens de Knowledge Base met micro soft Excel. Als u ervoor kiest om een andere toepassing te gebruiken om het geëxporteerde TSV-bestand te bewerken, kan de toepassing syntaxis fouten veroorzaken omdat deze niet volledig TSV-compatibel is. De TSV-bestanden van micro soft Excel voeren doorgaans geen opmaak fouten uit. 

Wanneer u klaar bent met uw bewerkingen, importeert u het TSV-bestand opnieuw vanaf de pagina **instellingen** . Hiermee wordt de huidige Knowledge Base volledig vervangen door de geïmporteerde kennis database. 

## <a name="testing-your-markdown"></a>Uw prijs verlaging testen

Gebruik de **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -zelf studie om uw prijs verlaging te valideren. De zelf studie heeft een **try it** -functie voor het snel kopiëren/plakken van validatie. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een QnA Maker-service instellen](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Zie ook 

[Overzicht van QnA Maker](../Overview/overview.md)
