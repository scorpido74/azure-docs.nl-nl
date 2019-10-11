---
title: Gegevens modellering in Azure Time Series Insights preview | Microsoft Docs
description: Informatie over gegevens modellering in Azure Time Series Insights preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273737"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Gegevens modellering in Azure Time Series Insights preview

In dit document wordt beschreven hoe u kunt werken met Time Series-modellen die volgen op de Azure Time Series Insights preview. Het bevat een aantal veelvoorkomende gegevens scenario's.

Lees [Azure time series Insights preview Explorer](./time-series-insights-update-explorer.md)voor meer informatie over het gebruik van de update.

## <a name="types"></a>Dergelijke

### <a name="create-a-single-type"></a>Een enkel type maken

1. Ga naar het paneel selector voor tijdreeks modellen en selecteer **typen** in het menu. Vouw het deel venster samen om de focus te richten op de typen tijd reeks modellen.

    [![Create één type](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecteer **+ Toevoegen**.
1. Voer alle details in die betrekking hebben op typen en selecteer **maken**. Met deze actie worden typen in de omgeving gemaakt.

    [![Add a-type](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Bulksgewijs uploaden van een of meer typen

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat het type Payload bevat.
1. Selecteer **Uploaden**.

    [JSON van ![Upload](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Eén type bewerken

1. Selecteer het type en selecteer **bewerken**. 
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![Edit a-type](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Een type verwijderen

1. Selecteer het type en selecteer **verwijderen**.
1. Als er geen exemplaren zijn gekoppeld aan de typen, wordt deze verwijderd.

    [![Delete a-type](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hiërarchieën

### <a name="create-a-single-hierarchy"></a>Een enkele hiërarchie maken

1. Ga naar het paneel selector voor tijdreeks modellen en selecteer **hiërarchieën** in het menu. Vouw het deel venster uit om de focus te richten op de hiërarchieën met Time Series-modellen.

    [![Select-hiërarchieën](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [![Add een hiërarchie](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecteer **+ niveau toevoegen** in het rechterdeel venster.

    [![Add een niveau](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Voer de hiërarchie Details in en selecteer **maken**.

    [![Create een niveau](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Bulksgewijs een of meer hiërarchieën uploaden

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de hiërarchie lading bevat.
1. Selecteer **Uploaden**.

    [![Bulk-upload hiërarchieën](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Eén hiërarchie bewerken

1. Selecteer de hiërarchie en selecteer **bewerken**.
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![Edit één hiërarchie](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Een hiërarchie verwijderen

1. Selecteer de hiërarchie en selecteer **verwijderen**. 
1. Als er geen exemplaren zijn gekoppeld aan de hiërarchie, wordt deze verwijderd.

    [![Delete een hiërarchie](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Exemplaren

### <a name="create-a-single-instance"></a>Een enkel exemplaar maken

1. Ga naar het paneel selector voor tijdreeks modellen en selecteer **instanties** in het menu. Vouw het deel venster uit om te focussen op de exemplaren van de time series-modellen.

    [![Create één exemplaar](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecteer **Toevoegen**.

    [![Add een exemplaar](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Voer de details van het exemplaar in, selecteer de koppeling type en hiërarchie en selecteer **maken**.

### <a name="bulk-upload-one-or-more-instances"></a>Bulk upload een of meer exemplaren

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de nettolading van de instanties bevat.

    [![Bulk een of meer exemplaren uploaden](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Selecteer **Uploaden**.

### <a name="edit-a-single-instance"></a>Eén exemplaar bewerken

1. Selecteer het exemplaar en selecteer **bewerken**. 
1. Breng de gewenste wijzigingen aan en selecteer **Opslaan**.

    [![Edit één exemplaar](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over time series-modellen [gegevens modellering](./time-series-insights-update-tsm.md).

- Lees voor meer informatie over de preview-versie [visualiseren van gegevens in de preview-versie van Azure time series Insights](./time-series-insights-update-explorer.md).

- Lees [ondersteunde JSON-vormen](./time-series-insights-send-events.md#supported-json-shapes)voor meer informatie over ondersteunde JSON-vormen.
