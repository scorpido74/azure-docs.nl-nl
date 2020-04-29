---
title: Overzicht van gegevens stroom transformatie toewijzen
description: Een overzicht van de verschillende trans formaties die beschikbaar zijn in de toewijzing van gegevens stroom
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0442c701f39fd3e84361ad0201aaf8d1840d8851
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606274"
---
# <a name="mapping-data-flow-transformation-overview"></a>Overzicht van gegevens stroom transformatie toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Hieronder ziet u een lijst van de trans formaties die momenteel worden ondersteund in de toewijzing van gegevens stroom. Klik op elke trans formaties voor meer informatie over de configuratie gegevens.

| Naam | Categorie | Beschrijving |
| ---- | -------- | ----------- |
| [Samenvoegen](data-flow-aggregate.md) | Schema wijziging | Definieer verschillende typen aggregaties, zoals som, MIN, maximum en aantal, gegroepeerd op bestaande of berekende kolommen. | 
| [Rij wijzigen](data-flow-alter-row.md) | Rij-aanpassing | Stel insert-, Delete-, update-en upsert-beleid in op rijen. |
| [Voorwaardelijk splitsen](data-flow-conditional-split.md) | Meerdere invoer/uitvoer | Route rijen met gegevens naar verschillende stromen op basis van overeenkomende voor waarden. |
| [Afgeleide kolom](data-flow-derived-column.md) | Schema wijziging | nieuwe kolommen genereren of bestaande velden wijzigen met behulp van de data flow-expressie taal. | 
| [Bestaat](data-flow-exists.md) | Meerdere invoer/uitvoer | Controleer of uw gegevens zich in een andere bron of stream bevindt. | 
| [Filter](data-flow-filter.md) | Rij-aanpassing | Een rij filteren op basis van een voor waarde. |
| [Platmaken](data-flow-flatten.md) | Schema wijziging |  Haal matrix waarden op in hiërarchische structuren zoals JSON en maak ze ongedaan in afzonderlijke rijen. |
| [Toevoegen](data-flow-join.md) | Meerdere invoer/uitvoer |  Gegevens uit twee bronnen of streams combi neren. |
| [Opzoeken](data-flow-lookup.md) | Meerdere invoer/uitvoer | Referentie gegevens uit een andere bron. |
| [Nieuwe vertakking](data-flow-new-branch.md) | Meerdere invoer/uitvoer | Meerdere sets bewerkingen en trans formaties Toep assen op dezelfde gegevens stroom. |
| [Draaitabel](data-flow-pivot.md) | Schema wijziging | Een aggregatie waarbij een of meer groeperings kolommen de afzonderlijke rijwaarden in afzonderlijke kolommen hebben getransformeerd. |
| [Selecteren](data-flow-select.md) | Schema wijziging | Alias kolommen en stroom namen en kolommen verwijderen of opnieuw rangschikken |
| [Sink](data-flow-sink.md) | - | Een eind bestemming voor uw gegevens |
| [Sorteren](data-flow-sort.md) | Rij-aanpassing | Binnenkomende rijen op de huidige gegevens stroom sorteren |
| [Bron](data-flow-source.md) | - | Een gegevens bron voor de gegevens stroom |
| [Surrogaatsleutel](data-flow-surrogate-key.md) | Schema wijziging | Een wille keurige sleutel waarde voor niet-zakelijk nummer toevoegen |
| [Réunion](data-flow-union.md) | Meerdere invoer/uitvoer | Meerdere gegevens stromen verticaal combi neren |
| [Draaitabel opheffen](data-flow-unpivot.md) | Schema wijziging | Kolommen in rijwaarden draaien |
| [Venster](data-flow-window.md) | Schema wijziging |  Definieer op venster gebaseerde aggregaties van kolommen in uw gegevens stromen. |
