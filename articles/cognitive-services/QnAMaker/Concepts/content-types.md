---
title: Inhouds typen-QnA Maker
description: Inhouds typen bevatten veel standaard gestructureerde documenten, zoals PDF, DOC en TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843433"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Inhouds typen van documenten die u aan een kennis database kunt toevoegen
Inhouds typen bevatten veel standaard gestructureerde documenten, zoals PDF, DOC en TXT.

## <a name="file-and-url-data-types"></a>Bestands-en URL-gegevens typen

De onderstaande tabel bevat een overzicht van de typen inhoud en bestandsindelingen die worden ondersteund door de QnA Maker.

|Brontype|Inhoudstype| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC-BESTAND|Faq's<br> Product handleiding,<br> Brochures,<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Heap.|[Structured QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> Voor [beeld van product manual. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> Voor [beeld van semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van technisch document. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Voor beeld multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Gestructureerd QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|[Voorbeeld QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Gestructureerd QnA-bestand|[Voorbeeld chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Als u verificatie voor uw gegevens bron nodig hebt, kunt u de volgende methoden voor het ophalen van die inhoud in QnA Maker:

* Down load het bestand hand matig en importeer het in QnA Maker
* Bestand toevoegen vanaf de geauthenticeerde [share point-locatie](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-inhoud

Er kunnen twee typen documenten worden geïmporteerd via **URL** in QnA Maker:

* Veelgestelde vragen over URL 's
* Url's voor ondersteuning

Elk type geeft een verwachte indeling aan.

## <a name="file-based-content"></a>Op bestanden gebaseerde inhoud

U kunt bestanden toevoegen aan een Knowledge Base van een open bare bron of uw lokale bestands systeem in de [QnA Maker Portal](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Richt lijnen voor inhouds indeling

QnA Maker voegt bestands-en URL-inhoud toe, waarbij de inhoud wordt geconverteerd naar QnA sets die zijn opgeslagen als prijs verlaging (`.md`). Inhoud past in een Knowledge Base waarin de inhoud is ingedeeld in een gestructureerd formulier en wordt weer gegeven in duidelijk gedefinieerde secties. De secties kunnen verder worden onderverdeeld in subsecties of subonderwerpen. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

QnA Maker herkent secties en subsecties en relaties in de inhoud op basis van visuele aanwijzingen zoals teken grootte, teken stijl, nummering, kleuren enzovoort. Semi-gestructureerde inhoud bevat hand leidingen, veelgestelde vragen, richt lijnen, beleids regels, brochures, folders en vele andere soorten bestanden.

## <a name="faq-urls"></a>Veelgestelde vragen over URL 's

Veelgestelde vragen over webpagina's in 3 verschillende vormen kunt ondersteuning voor QnA Maker: Plain Veelgestelde vragen over pagina's, veelgestelde vragen over pagina's met koppelingen, veelgestelde vragen over pagina's met een startpagina onderwerpen.


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

QnA Maker herkent secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen zoals teken grootte, teken stijl, nummering, kleuren enzovoort. Semi-gestructureerde PDF-of DOC-bestanden kunnen hand leidingen, veelgestelde vragen, richt lijnen, beleids regels, brochures, folders en vele andere typen bestanden zijn. Hieronder ziet u enkele documentbeveiligingsrisico voorbeeld van deze bestanden.

### <a name="product-manuals"></a>Producthandleidingen

Een handmatige is doorgaans richtlijnen materiaal die wordt meegestuurd met een product. Hiermee wordt de gebruiker wilt instellen, gebruikt, onderhouden en oplossen van het product. Wanneer de QnA Maker een handmatige processen, pakt deze de koppen en subkoppen als vragen en de volgende inhoud als antwoord. Bekijk een voorbeeld [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Hieronder volgt een voorbeeld van een handmatige met een index-pagina en hiërarchische inhoud

 ![Product handmatige voorbeeld voor een knowledge base](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extractie werkt het beste op de handleidingen die een tabel met inhoud en/of een indexpagina en een duidelijke structuur met hiërarchische koppen hebben.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richtlijnen, documenten en andere bestanden

Nog vele andere typen documenten kunnen ook worden verwerkt voor het genereren van QA-paren, mits ze een duidelijke structuur en indeling hebben. Dit zijn onder andere: brochures, richt lijnen, rapporten, White papers, weten schappelijke documenten, beleids regels, boeken, enzovoort. Bekijk [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)een voor beeld.

Hieronder volgt een voorbeeld van een semi-gestructureerde doc, zonder een index:

 ![Azure Blob-opslag semi-gestructureerde doc-bestand](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Structured QnA-document

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

## <a name="formatting-considerations"></a>Overwegingen voor opmaak

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n`. Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud.

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen.

Meer informatie vindt u in de documentatie over het QnA Maker van de [prijs verlaging](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Uw prijs verlaging testen

Gebruik de **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -zelf studie om uw prijs verlaging te valideren. De zelf studie heeft een **try it** -functie voor het snel kopiëren/plakken van validatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het ontwerpen en beheren van [QnA-sets (vraag en antwoord)](question-answer-set.md)