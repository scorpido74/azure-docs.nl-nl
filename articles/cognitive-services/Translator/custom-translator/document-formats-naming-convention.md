---
title: Documentindelingen en naamgevingsconventies - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Dit is een handleiding over documentformaten en naamgevingsconventie in Aangepaste vertaler. Dit concept helpt bij het beheren van documenten namen beter abd voorkomen naamgeving conflicten.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595846"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Documentindelingen en naamgevingsconventierichtlijnen

Elk bestand dat wordt gebruikt voor aangepaste vertaling moet ten minste **vier** tekens lang zijn.

Deze tabel bevat alle ondersteunde bestandsindelingen die u gebruiken om uw vertaalsysteem te bouwen:

| Indeling            | Extensies   | Beschrijving                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . Xlf. XLIFF | Een parallelle documentindeling, export van vertaalgeheugensystemen. De gebruikte talen worden gedefinieerd in het bestand.                                                                                                                                                              |
| Tmx               | . Tmx         | Een parallelle documentindeling, export van vertaalgeheugensystemen. De gebruikte talen worden gedefinieerd in het bestand.                                                                                                                                                              |
| Zip               | . Zip         | ZIP is een archiefbestandsindeling.                                                                                                                                                                                                        |
| Locstudio (Locstudio)         | . Lcl         | Een Microsoft-indeling voor parallelle documenten                                                                                                                                                                                                                                      |
| Microsoft Word    | . Docx        | Microsoft Word-document                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . Pdf         | Draagbaar Adobe Acrobat-document                                                                                                                                                                                                                                                |
| HTML              | . Html. Htm  | HTML-document                                                                                                                                                                                                                                                                  |
| Tekstbestand         | . Txt         | UTF-16 of UTF-8 gecodeerde tekstbestanden. De bestandsnaam mag geen Japanse tekens bevatten.                                                                                                                                                                                        |
| Uitgelijnd tekstbestand | . Uitlijnen       | De `.ALIGN` extensie is een speciale extensie die u gebruiken als u weet dat de zinnen in het documentpaar perfect zijn uitgelijnd. Als u `.ALIGN` een bestand opgeeft, zal Aangepaste vertaler de zinnen niet voor u uitlijnen. |
| Excel-bestand        | . Xlsx        | Excel-bestand (2013 of hoger). De eerste regel/rij van de spreadsheet moet taalcode zijn.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Woordenboekindelingen

Voor woordenboeken ondersteunt Custom Translator alle bestandsindelingen die worden ondersteund voor trainingssets. Als u een Excel-woordenboek gebruikt, moet de eerste regel/rij van de spreadsheet taalcodes zijn.

## <a name="zip-file-formats"></a>Zip-bestandsindelingen

Documenten kunnen worden gegroepeerd in één zip-bestand en worden geüpload. De aangepaste vertaler ondersteunt zip-bestandsindelingen (ZIP, GZ en TGZ).

Elk document in het zip-bestand met de extensie TXT, HTML, HTM, PDF, DOCX, ALIGN moet deze naamgevingsconventie volgen:

{documentnaam} \_{taalcode} waarbij {documentnaam} de naam van uw document is, {taalcode} is de ISO LanguageID (twee tekens), die aangeeft dat het document zinnen in die taal bevat. Er moet een underscore (_) zijn voordat de taalcode.

Als u bijvoorbeeld twee parallelle documenten binnen een zip uploadt voor een Engels naar Spaans systeem, moeten de bestanden de naam 'data_en' en 'data_es' krijgen.

Vertaalgeheugenbestanden (TMX, XLF, XLIFF, LCL, XLSX) zijn niet verplicht om de specifieke taalnaamgevingsconventie te volgen.  

## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [project](workspace-and-project.md#what-is-a-custom-translator-project) om ze te maken en te beheren.
