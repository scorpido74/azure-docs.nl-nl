---
title: Overzicht van gegevensstroomtransformatie toewijzen
description: Een overzicht van de verschillende transformaties die beschikbaar zijn in de kaartgegevensstroom
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 68423bee6096357add9b5d4b107c984ac67c9cab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412969"
---
# <a name="mapping-data-flow-transformation-overview"></a>Overzicht van gegevensstroomtransformatie toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Hieronder vindt u een lijst van de transformaties die momenteel worden ondersteund in het in kaart brengen van gegevensstroom. Klik op elke transformaties om de configuratiedetails te leren kennen.

| Naam | Categorie | Beschrijving |
| ---- | -------- | ----------- |
| [Samenvoegen](data-flow-aggregate.md) | Schemamodifier | Definieer verschillende typen aggregaties, zoals SOM, MIN, MAX en COUNT gegroepeerd op bestaande of berekende kolommen. | 
| [Rij wijzigen](data-flow-alter-row.md) | Rijmodifier | Regel het invoeg-, verwijder-, update- en upsert-beleid voor rijen. |
| [Voorwaardelijk splitsen](data-flow-conditional-split.md) | Meerdere ingangen/uitgangen | Rijen met gegevens naar verschillende streams routeren op basis van overeenkomende voorwaarden. |
| [Afgeleide kolom](data-flow-derived-column.md) | Schemamodifier | nieuwe kolommen genereren of bestaande velden wijzigen met de taal van de gegevensstroomexpressie. | 
| [Bestaat](data-flow-exists.md) | Meerdere ingangen/uitgangen | Controleer of uw gegevens in een andere bron of stream bestaan. | 
| [Filter](data-flow-filter.md) | Rijmodifier | Een rij filteren op basis van een voorwaarde. |
| [Platmaken](data-flow-flatten.md) | Schemamodifier |  Neem arraywaarden in hiërarchische structuren zoals JSON en rol ze uit in afzonderlijke rijen. |
| [Toevoegen](data-flow-join.md) | Meerdere ingangen/uitgangen |  Combineer gegevens uit twee bronnen of streams. |
| [Opzoeken](data-flow-lookup.md) | Meerdere ingangen/uitgangen | Referentiegegevens van een andere bron. |
| [Nieuwe vertakking](data-flow-new-branch.md) | Meerdere ingangen/uitgangen | Pas meerdere sets bewerkingen en transformaties toe op dezelfde gegevensstroom. |
| [Draaitabel](data-flow-pivot.md) | Schemamodifier | Een aggregatie waarbij een of meer groeperingskolommen zijn afzonderlijke rijwaarden hebben omgezet in afzonderlijke kolommen. |
| [Selecteren](data-flow-select.md) | Schemamodifier | Aliaskolommen en streamnamen en kolommen laten vallen of opnieuw ordenen |
| [Sink](data-flow-sink.md) | - | Een eindbestemming voor uw gegevens |
| [Sorteren](data-flow-sort.md) | Rijmodifier | Binnenkomende rijen sorteren op de huidige gegevensstroom |
| [Bron](data-flow-source.md) | - | Een gegevensbron voor de gegevensstroom |
| [Surrogaatsleutel](data-flow-surrogate-key.md) | Schemamodifier | Een verhoging van de willekeurige sleutelwaarde van niet-bedrijfsactiviteiten toevoegen |
| [Unie](data-flow-union.md) | Meerdere ingangen/uitgangen | Meerdere gegevensstromen verticaal combineren |
| [Draaitabel opheffen](data-flow-unpivot.md) | Schemamodifier | Kolommen in rijwaarden draaien |
| [Venster](data-flow-window.md) | Schemamodifier |  Definieer op venster gebaseerde aggregaties van kolommen in uw gegevensstromen. |
