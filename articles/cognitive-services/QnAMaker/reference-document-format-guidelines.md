---
title: Richt lijnen voor het importeren van document indelingen-QnA Maker
description: Begrijpen hoe de typen Url's worden gebruikt voor het importeren en maken van QnA sets.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651837"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Richt lijnen voor het opmaken van geïmporteerde documenten en Url's

Bekijk deze opmaak richtlijnen om de beste resultaten voor uw inhoud te krijgen.

## <a name="formatting-considerations"></a>Overwegingen voor opmaak

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n`. Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud.

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen.

## <a name="basic-document-formatting"></a>Elementaire document opmaak

QnA Maker herkent secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen als:

* teken grootte
* lettertype stijl
* nummeren
* kleuren

|Voorbeelden voor documenten|
|--|
||



## <a name="product-manuals"></a>Producthandleidingen

Een handmatige is doorgaans richtlijnen materiaal die wordt meegestuurd met een product. Hiermee wordt de gebruiker wilt instellen, gebruikt, onderhouden en oplossen van het product. Wanneer de QnA Maker een handmatige processen, pakt deze de koppen en subkoppen als vragen en de volgende inhoud als antwoord. Bekijk [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)een voor beeld.

Hieronder volgt een voorbeeld van een handmatige met een index-pagina en hiërarchische inhoud

 ![Product handmatige voorbeeld voor een knowledge base](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extractie werkt het beste op de handleidingen die een tabel met inhoud en/of een indexpagina en een duidelijke structuur met hiërarchische koppen hebben.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richtlijnen, documenten en andere bestanden

Nog vele andere typen documenten kunnen ook worden verwerkt voor het genereren van QA-paren, mits ze een duidelijke structuur en indeling hebben. Dit zijn onder andere: brochures, richt lijnen, rapporten, White papers, weten schappelijke documenten, beleids regels, boeken, enzovoort. Bekijk [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)een voor beeld.

Hieronder volgt een voorbeeld van een semi-gestructureerde doc, zonder een index:

 ![Azure Blob-opslag semi-gestructureerde doc-bestand](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Gestructureerde QnA-Document

De notatie voor gestructureerde-antwoorden op beveiligingsvragen in DOC-bestand is in de vorm van wisselende vragen en antwoorden per regel één vraag per regel gevolgd door een antwoord in de volgende regel, zoals hieronder wordt weergegeven:

```text
Question1

Answer1

Question2

Answer2
```

Hieronder volgt een voorbeeld van een gestructureerde QnA word-document:

 ![Gestructureerde QnA-document is een voorbeeld van een kennisdatabase](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Gestructureerde *txt*-, *TSV* -en *xls* -bestanden

QnAs in de vorm van gestructureerde *. txt*-, *TSV* -of *xls* -bestanden kunnen ook worden geüpload naar QnA Maker om een Knowledge Base te maken of uit te breiden.  Dit kunnen bestaan uit tekst zonder opmaak of inhoud in de RTF- of HTML-code kunnen hebben.

| Vraag  | Antwoord  | Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuele extra kolommen in het bronbestand worden genegeerd.

### <a name="example-of-structured-excel-file"></a>Voor beeld van een gestructureerd Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand met HTML-inhoud:

 ![Voorbeeld van een kennisdatabase gestructureerde QnA in excel](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand, met verschillende alternatieve vragen voor één antwoord:

 ![Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nadat het bestand is geïmporteerd, bevindt het vraag-en antwoord paar zich in de Knowledge Base, zoals hieronder wordt weer gegeven:

 ![Scherm afbeelding van alternatieve vragen voor één antwoord dat in de Knowledge Base wordt geïmporteerd](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Indeling van gestructureerde gegevens via importeren

Importeren van een kennisdatabase wordt vervangen door de inhoud van de bestaande knowledge base. Invoer vereist een gestructureerde .tsv-bestand met informatie van de gegevensbron. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord paren en het kenmerk ervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactioneel|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Document opmaak met meerdere scha kelen

* Gebruik koppen en subkoppen om de hiërarchie aan te duiden. U kunt bijvoorbeeld H1 de bovenliggende QnA en H2 aanduiden om de QnA aan te duiden die als prompt moet worden beschouwd. Gebruik de kleine grootte van de kop om volgende hiërarchie aan te duiden. Gebruik geen stijl, kleur of een ander mechanisme om de structuur in uw document te impliceren, QnA Maker de prompts voor meerdere schakelingen niet uit te pakken.
* Het eerste teken van de kop moet worden gekapitaliseerd.
* Beëindig geen koptekst met een vraag teken `?`.


|Voorbeelden voor documenten|
|--|
||