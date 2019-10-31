---
title: Gegevens modellering in Azure Time Series Insights preview | Microsoft Docs
description: Informatie over gegevens modellering in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 81c2c2af78f5f066e1b27e14fa774df04d7c5868
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063985"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Gegevens modellering in Azure Time Series Insights preview

In dit artikel wordt beschreven hoe u kunt werken met Time Series-model in Azure Time Series Insights preview. Het bevat een aantal veelvoorkomende gegevens scenario's.

Lees [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md)voor meer informatie over het gebruik van de update.

## <a name="types"></a>Dergelijke

### <a name="create-a-single-type"></a>Een enkel type maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **typen** in het menu. Vouw het deel venster samen om de focus te richten op de typen tijd reeks modellen.

    [![deel venster typen](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecteer **+ Toevoegen**.
1. Voer alle gegevens in die betrekking hebben op typen en selecteer **maken**. Met deze actie worden typen in de omgeving gemaakt.

    [![selecties voor het toevoegen van een type](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Bulksgewijs uploaden van een of meer typen

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat het type Payload bevat.
1. Selecteer **Uploaden**.

    [![selecties bulksgewijs uploaden van een of meer typen](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Eén type bewerken

1. Selecteer het type en selecteer **bewerken**. 
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![selecties voor het bewerken van een type](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Een type verwijderen

1. Selecteer het type en selecteer **verwijderen**.
1. Als er geen exemplaren zijn gekoppeld aan de typen, wordt deze verwijderd.

    [knop verwijderen ![](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hiërarchieën

### <a name="create-a-single-hierarchy"></a>Een enkele hiërarchie maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **hiërarchieën** in het menu. Vouw het deel venster uit om de focus te richten op de time series model-hiërarchieën.

    [deel venster hiërarchieën ![](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [knop toevoegen ![](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecteer **+ niveau toevoegen** in het rechterdeel venster.

    [knop ' niveau toevoegen ' ![](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Voer de hiërarchie Details in en selecteer **maken**.

    [![Details van Hieararchy en de knop maken](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Bulksgewijs een of meer hiërarchieën uploaden

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de hiërarchie lading bevat.
1. Selecteer **Uploaden**.

    [![selecties voor het bulksgewijs uploaden van hiërarchieën](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Eén hiërarchie bewerken

1. Selecteer de hiërarchie en selecteer **bewerken**.
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![selecties voor het bewerken van één hiërarchie](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Een hiërarchie verwijderen

1. Selecteer de hiërarchie en selecteer **verwijderen**. 
1. Als er geen exemplaren zijn gekoppeld aan de hiërarchie, wordt deze verwijderd.

    [knop verwijderen ![](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Exemplaren

### <a name="create-a-single-instance"></a>Een enkel exemplaar maken

1. Ga naar het deel venster voor het selecteren van de tijdreeks model en selecteer **instanties** in het menu. Vouw het deel venster samen om de focus te richten op de exemplaren van het time series-model.

    [![deel venster instanties](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecteer **Toevoegen**.

    [![selecties voor het toevoegen van een exemplaar](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Voer de details van het exemplaar in, selecteer de koppeling type en hiërarchie en selecteer **maken**.

### <a name="bulk-upload-one-or-more-instances"></a>Bulk upload een of meer exemplaren

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de nettolading van de instanties bevat.

    [![selecties voor bulksgewijs uploaden van een of meer instanties](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selecteer **Uploaden**.

### <a name="edit-a-single-instance"></a>Eén exemplaar bewerken

1. Selecteer het exemplaar en selecteer **bewerken**. 
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![selecties voor het bewerken van één exemplaar](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Lees [gegevens modellering](./time-series-insights-update-tsm.md)voor meer informatie over het time series-model.

- Lees voor meer informatie over de preview-versie [visualiseren van gegevens in de preview-versie van Azure time series Insights](./time-series-insights-update-explorer.md).

- Lees [ondersteunde JSON-vormen](./time-series-insights-send-events.md#supported-json-shapes)voor meer informatie over ondersteunde JSON-vormen.
