---
title: Overzicht van gegevens stroom transformatie toewijzen
description: Een overzicht van de verschillende trans formaties die beschikbaar zijn in de toewijzing van gegevens stroom
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086923"
---
# <a name="mapping-data-flow-transformation-overview"></a>Overzicht van gegevens stroom transformatie toewijzen

Hieronder ziet u een lijst van de trans formaties die momenteel worden ondersteund in de toewijzing van gegevens stroom. Klik op elke trans formaties voor meer informatie over de configuratie gegevens.

| Naam | Category | Beschrijving |
| ---- | -------- | ----------- |
| [Vatting](data-flow-aggregate.md) | Schema wijziging | Definieer verschillende typen aggregaties, zoals som, MIN, maximum en aantal, gegroepeerd op bestaande of berekende kolommen. | 
| [Rij wijzigen](data-flow-alter-row.md) | Rij-aanpassing | Stel insert-, Delete-, update-en upsert-beleid in op rijen. |
| [Voorwaardelijke splitsing](data-flow-conditional-split.md) | Meerdere invoer/uitvoer | Route rijen met gegevens naar verschillende stromen op basis van overeenkomende voor waarden. |
| [Afgeleide kolom](data-flow-derived-column.md) | Schema wijziging | nieuwe kolommen genereren of bestaande velden wijzigen met behulp van de data flow-expressie taal. | 
| [Reeds](data-flow-exists.md) | Meerdere invoer/uitvoer | Controleer of uw gegevens zich in een andere bron of stream bevindt. | 
| [Filterwebonderdelen](data-flow-filter.md) | Rij-aanpassing | Een rij filteren op basis van een voor waarde. |
| [Voeg](data-flow-flatten.md) | Schema wijziging |  Haal matrix waarden op in hiërarchische structuren zoals JSON en maak ze ongedaan in afzonderlijke rijen. |
| [Koppelen](data-flow-join.md) | Meerdere invoer/uitvoer |  Gegevens uit twee bronnen of streams combi neren. |
| [Unmp](data-flow-lookup.md) | Meerdere invoer/uitvoer | Referentie gegevens uit een andere bron. |
| [Nieuwe vertakking](data-flow-new-branch.md) | Meerdere invoer/uitvoer | Meerdere sets bewerkingen en trans formaties Toep assen op dezelfde gegevens stroom. |
| [Belang](data-flow-pivot.md) | Schema wijziging | Een aggregatie waarbij een of meer groeperings kolommen de afzonderlijke rijwaarden in afzonderlijke kolommen hebben getransformeerd. |
| [Selecteren](data-flow-select.md) | Schema wijziging | Alias kolommen en stroom namen en kolommen verwijderen of opnieuw rangschikken |
| [Tenen](data-flow-sink.md) | - | Een eind bestemming voor uw gegevens |
| [Acties](data-flow-sort.md) | Rij-aanpassing | Binnenkomende rijen op de huidige gegevens stroom sorteren |
| [Bron](data-flow-source.md) | - | Een gegevens bron voor de gegevens stroom |
| [Surrogaat sleutel](data-flow-surrogate-key.md) | Schema wijziging | Een wille keurige sleutel waarde voor niet-zakelijk nummer toevoegen |
| [Réunion](data-flow-union.md) | Meerdere invoer/uitvoer | Meerdere gegevens stromen verticaal combi neren |
| [UNPIVOT](data-flow-unpivot.md) | Schema wijziging | Kolommen in rijwaarden draaien |
| [Openen](data-flow-window.md) | Schema wijziging |  Definieer op venster gebaseerde aggregaties van kolommen in uw gegevens stromen. |
