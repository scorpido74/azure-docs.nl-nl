---
title: Gegevensbronnen ondersteund - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker haalt automatisch de vraag antwoord paren op die zijn opgeslagen als webpagina's, PDF-bestanden of MS Word-document bestanden of gestructureerde QnA-inhouds bestanden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: diberry
ms.openlocfilehash: 2978ffa68814d176ea1caf485e7e4f1ba72f2597
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867564"
---
# <a name="data-sources-for-qna-maker-content"></a>Gegevensbronnen voor QnA Maker-inhoud

QnA Maker worden vraag-antwoord paren automatisch geëxtraheerd uit semi-gestructureerde inhoud zoals veelgestelde vragen over, producthandleidingen, richtlijnen, voor ondersteuningsdocumenten en beleidsregels die zijn opgeslagen als webpagina's, PDF-bestanden of bestanden van Microsoft Word-document. Inhoud kan ook worden toegevoegd aan de knowledge base van gestructureerde QnA inhoudsbestanden.

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Bestands-en URL-gegevens typen

De onderstaande tabel bevat een overzicht van de typen inhoud en bestandsindelingen die worden ondersteund door de QnA Maker.

|Brontype|Inhoudstype| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC-BESTAND|Faq's<br> Product handleiding,<br> Brochures,<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Enzovoort.|[Structured QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> Voor [beeld van product manual. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> Voor [beeld van semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld van technisch document. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Voor beeld multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Gestructureerd QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|[Voorbeeld QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Gestructureerd QnA-bestand|[Voorbeeld chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>De Knowledge Base importeren en exporteren

**TSV-en xls-bestanden**, van geëxporteerde kennis grondslagen, kunnen alleen worden gebruikt door het importeren van de bestanden van de pagina **instellingen** in de QnA Maker Portal. Ze kunnen niet worden gebruikt als gegevens bronnen tijdens het maken van een Knowledge Base of vanuit het onderdeel **bestand toevoegen** of **+ URL** toevoegen op de pagina **instellingen** .

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

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactioneel|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redactioneel in knowledge base toevoegen

Als u geen bestaande inhoud voor het vullen van de knowledge base, kunt u vragen en antwoorden supereenvoudig redactioneel toevoegen in de QnA Maker Knowledge base. Meer informatie over het bijwerken van uw knowledge base [hier](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Overwegingen voor opmaak

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n`. Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud.

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen.

Raadpleeg de voor beelden van korting in de [QnA Maker prijs informatie](../reference-markdown-format.md) voor de korting voor meer informatie.

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
