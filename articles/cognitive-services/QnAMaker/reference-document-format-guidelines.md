---
title: Richtlijnen voor documentindeling importeren - QnA Maker
description: Begrijpen hoe de typen URL's worden gebruikt om QnA-sets te importeren en te maken.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1e006520ce5c61f2da3f425af2332b0d41cc3881
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754903"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Richtlijnen voor geïmporteerde documenten en URL's opmaken

Bekijk deze richtlijnen voor opmaak om de beste resultaten voor uw inhoud te krijgen.

## <a name="formatting-considerations"></a>Overwegingen voor opmaak

Na het importeren van een bestand of URL converteert en slaat QnA Maker uw inhoud om in de [afwaarderingsindeling](https://en.wikipedia.org/wiki/Markdown). Het conversieproces voegt nieuwe regels toe `\n\n`in de tekst, zoals . Een kennis van de afwaarderingsvorm helpt u om de geconverteerde inhoud te begrijpen en uw knowledge base-inhoud te beheren.

Als u uw inhoud rechtstreeks in uw kennisbank toevoegt of bewerkt, gebruikt u **opmaak opmaak** om inhoud met rijke tekst te maken of wijzigt u de markeringsindeling inhoud die al in het antwoord staat. QnA Maker ondersteunt een groot deel van de markdown-indeling om rijke tekstmogelijkheden aan uw inhoud te bieden. De clienttoepassing, zoals een chatbot, ondersteunt mogelijk echter niet dezelfde set markdown-indelingen. Het is belangrijk om de weergave van antwoorden van de clienttoepassing te testen.

Bekijk een volledige lijst met [inhoudstypen en voorbeelden.](./Concepts/content-types.md#file-and-url-data-types)

## <a name="basic-document-formatting"></a>Basisdocumentopmaak

QnA Maker identificeert secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen zoals:

* tekengrootte
* Tekenstijl
* Nummering
* kleuren

## <a name="product-manuals"></a>Producthandleidingen

Een handleiding is meestal geleidingsmateriaal dat een product begeleidt. Het helpt de gebruiker bij het instellen, gebruiken, onderhouden en oplossen van het product. Wanneer QnA Maker een handleiding verwerkt, worden de koppen en onderverdelingen als vragen en de daaropvolgende inhoud als antwoorden geëxtraheerd. Zie [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)een voorbeeld.

Hieronder vindt u een voorbeeld van een handleiding met een indexpagina en hiërarchische inhoud

 ![Producthandleiding voorbeeld voor een kennisbank](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extractie werkt het beste bij handleidingen met een inhoudsopgave en/of een indexpagina en een duidelijke structuur met hiërarchische koppen.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richtlijnen, documenten en andere bestanden

Veel andere soorten documenten kunnen ook worden verwerkt om QA-paren te genereren, op voorwaarde dat ze een duidelijke structuur en lay-out hebben. Deze omvatten: Brochures, richtlijnen, rapporten, white papers, wetenschappelijke artikelen, beleid, boeken, enz. Zie [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)een voorbeeld.

Hieronder is een voorbeeld van een semi-gestructureerd document, zonder een index:

 ![Azure Blob-opslag semi-gestructureerd document](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Gestructureerd QnA-document

Het formaat voor gestructureerde vraag-antwoorden in DOC-bestanden, is in de vorm van afwisselend vragen en antwoorden per regel, een vraag per regel gevolgd door het antwoord in de volgende regel, zoals hieronder weergegeven:

```text
Question1

Answer1

Question2

Answer2
```

Hieronder vindt u een voorbeeld van een gestructureerd QnA-worddocument:

 ![Gestructureerd QnA-document voorbeeld voor een kennisbank](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Gestructureerde *TXT-,* *TSV-* en *XLS-bestanden*

QnA's in de vorm van gestructureerde *.txt,.tsv-* of *.xls-bestanden* kunnen ook worden geüpload naar QnA Maker om een kennisbank te creëren of uit te breiden. *.txt*  Dit kunnen platte tekst zijn, of inhoud hebben in RTF of HTML.

| Vraag  | Antwoord  | Metagegevens (1 toets: 1 waarde) |
|-----------|---------|-------------------------|
| Vraag1 | Antwoord1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Vraag2 | Antwoord2 |      `Key:Value`           |

Eventuele extra kolommen in het bronbestand worden genegeerd.

### <a name="example-of-structured-excel-file"></a>Voorbeeld van gestructureerd Excel-bestand

Hieronder vindt u een voorbeeld van een gestructureerd QnA *.xls-bestand,* met HTML-inhoud:

 ![Structured QnA excel voorbeeld voor een kennisbank](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Voorbeeld van alternatieve vragen voor één antwoord in Excel-bestand

Hieronder vindt u een voorbeeld van een gestructureerd QnA *.xls-bestand,* met verschillende alternatieve vragen voor één antwoord:

 ![Voorbeeld van alternatieve vragen voor één antwoord in Excel-bestand](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nadat het bestand is geïmporteerd, bevindt het vraag-en-antwoordpaar zich in de kennisbank zoals hieronder wordt weergegeven:

 ![Schermafbeelding van alternatieve vragen voor enkel antwoord geïmporteerd in kennisbank](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Gestructureerde gegevensindeling via importeren

Het importeren van een kennisbank vervangt de inhoud van de bestaande kennisbank. Voor importeren is een gestructureerd .tsv-bestand vereist dat gegevensbrongegevens bevat. Met deze informatie kan QnA Maker de vraagbeantwoordingsparen groeperen en deze toeschrijven aan een bepaalde gegevensbron.

| Vraag  | Antwoord  | Bron| Metagegevens (1 toets: 1 waarde) |
|-----------|---------|----|---------------------|
| Vraag1 | Antwoord1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Vraag2 | Antwoord2 | Redactionele|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Documentopmaak met meerdere draaien

* Gebruik koppen en subkoppen om hiërarchie aan te duiden. U bijvoorbeeld h1 om de bovenliggende QnA en h2 aan te duiden om de QnA aan te duiden die als prompt moet worden genomen. Gebruik kleine kopgrootte om de volgende hiërarchie aan te duiden. Gebruik geen stijl, kleur of een ander mechanisme om structuur in uw document te impliceren, QnA Maker haalt de multi-turn prompts niet uit.
* Het eerste teken van de rubriek moet worden gekapitaliseerd.
* Beëindig een kop niet met `?`een vraagteken.

**Voorbeelddocumenten**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Voordelen (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-voordelen (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Volgende stappen

Bekijk een volledige lijst met [inhoudstypen en voorbeelden](./Concepts/content-types.md#file-and-url-data-types)