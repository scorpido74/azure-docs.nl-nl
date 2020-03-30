---
title: Gegevensmodellering in Preview-omgevingen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over gegevensmodellering in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470748"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Gegevensmodellering in Azure Time Series Insights Preview

In dit artikel wordt beschreven hoe u met Time Series Model werken in Azure Time Series Insights Preview. Het beschrijft verschillende veelvoorkomende gegevensscenario's.

> [!TIP]
> * Lees meer over het [Preview-tijdseriemodel](time-series-insights-update-tsm.md).
> * Meer informatie over het navigeren in de preview-gebruikersinterface in [Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md).

## <a name="instances"></a>exemplaren

De Azure Time Series Insights explorer ondersteunt instance **create,** **READ,** **UPDATE**en **Delete** operations in the browser. 

Selecteer om te beginnen **de** modelweergave in de weergave Analyse **van** de tijdzoneinsights.

### <a name="create-a-single-instance"></a>Eén instantie maken

1. Ga naar het deelvenster Tijdreeksmodel selectie en selecteer **Instanties** in het menu. Alle instanties die zijn gekoppeld aan de geselecteerde Time Series Insights-omgeving worden weergegeven.

    [![Maak één instantie door eerst Instanties te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [![Voeg een instantie toe door de knop + Toevoegen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Voer de instantiedetails in, selecteer de koppeling tekst en hiërarchie en selecteer **Maken**.

### <a name="bulk-upload-one-or-more-instances"></a>Bulk uploadt een of meer exemplaren

> [!TIP]
> U uw exemplaren opslaan op uw bureaublad in JSON. Het gedownloade JSON-bestand kan vervolgens via de volgende stappen worden geüpload.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand met de payload van de instanties.

    [![Bulk uploadexemplaren via JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecteer **Uploaden**.

### <a name="edit-a-single-instance"></a>Eén instantie bewerken

1. Selecteer de instantie en selecteer het pictogram **bewerken** of **potlood**. 
1. Breng de vereiste wijzigingen aan en selecteer **Opslaan**.

    [![Bewerk één exemplaar.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Een instantie verwijderen

1. Selecteer de instantie en selecteer het pictogram **verwijderen** of **afvalbakken**.

   [![Een instantie verwijderen door Verwijderen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Verwijdering bevestigen door **Verwijderen te**selecteren .

> [!NOTE]
> Een instantie moet een veldvalidatiecontrole met succes doorstaan om te worden verwijderd.

## <a name="hierarchies"></a>Hiërarchieën

De Azure Time Series Insights explorer ondersteunt **bewerkingen**Hiërarchie MAKEN, **LEZEN,** **BIJWERKEN**en **Verwijderen** in de browser. 

Selecteer om te beginnen **de** modelweergave in de weergave Analyse **van** de tijdzoneinsights.

### <a name="create-a-single-hierarchy"></a>Eén hiërarchie maken

1. Ga naar het deelvenster Tijdreeksmodel selector en selecteer **Hiërarchieën** in het menu. Alle hiërarchieën die zijn gekoppeld aan de geselecteerde Time Series Insights-omgeving worden weergegeven.

    [![Maak een hiërarchie door het deelvenster.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecteer **+ Toevoegen**.

    [![Hiërarchie + Knop Toevoegen.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecteer **+ Niveau toevoegen** in het rechterdeelvenster.

    [![Voeg een niveau toe aan de hiërarchie.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Voer de hiërarchiedetails in en selecteer **Opslaan**.

    [![Geef hiërarchiedetails op.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Bulk uploadt een of meer hiërarchieën

> [!TIP]
> U uw hiërarchieën opslaan op uw bureaublad in JSON. Het gedownloade JSON-bestand kan vervolgens via de volgende stappen worden geüpload.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand dat de payload van de hiërarchie bevat.
1. Selecteer **Uploaden**.

    [![Selecties voor het uploaden van hiërarchieën.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Eén hiërarchie bewerken

1. Selecteer de hiërarchie en selecteer het pictogram **bewerken** of **potlood**.
1. Breng de vereiste wijzigingen aan en selecteer **Opslaan**.

    [![Selecties voor het bewerken van één hiërarchie.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Een hiërarchie verwijderen

1. Selecteer de hiërarchie en selecteer het pictogram **verwijderen** of **afvalbakken**. 

    [![Een hiërarchie verwijderen door de knop Verwijderen te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Verwijdering bevestigen door **Verwijderen te**selecteren .

## <a name="types"></a>Typen

De Azure Time Series Insights explorer ondersteunt **bewerkingen Type MAKEN,** **LEZEN,** **BIJWERKEN**en **Verwijderen** in de browser. 

Selecteer om te beginnen **de** modelweergave in de weergave Analyse **van** de tijdzoneinsights.

### <a name="create-a-single-type"></a>Eén type maken

1. Ga naar het deelvenster Model van de tijdreeks en selecteer **Typen** in het menu. Alle typen die zijn gekoppeld aan de geselecteerde Time Series Insights-omgeving worden weergegeven.

    [![Venster typen tijdreeksmodel.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecteer **+ Toevoegen** om de pop-upmodale tekst toevoegen **weer** te geven.
1. Voer eigenschappen en variabelen in voor uw type. Zodra u bent ingevoerd, selecteert u **Opslaan**. 

    [![Configuratie-instellingen om een type toe te voegen.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Bulk uploadt een of meer typen

> [!TIP]
> U uw typen opslaan op uw bureaublad in JSON. Het gedownloade JSON-bestand kan vervolgens via de volgende stappen worden geüpload.

1. Selecteer **JSON uploaden**.
1. Selecteer het bestand met de tekstpayload.
1. Selecteer **Uploaden**.

    [![Bulktypen uploadopties.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Eén type bewerken

1. Selecteer het type en selecteer het pictogram **bewerken** of **potlood**.
1. Breng de vereiste wijzigingen aan en selecteer **Opslaan**.

    [![Bewerk een type in het deelvenster.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Een type verwijderen

1. Selecteer het type en selecteer het pictogram **verwijderen** of **afvalbakken**. .

   [![Een type verwijderen door Delete te selecteren.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Verwijdering bevestigen door **Verwijderen te**selecteren .

## <a name="next-steps"></a>Volgende stappen

- Lees [Gegevensmodellering](./time-series-insights-update-tsm.md)voor meer informatie over het model voor de tijdreeks.

- Lees Gegevens visualiseren in [de Azure Time Series Insights Preview-verkenner](./time-series-insights-update-explorer.md)voor meer informatie over de preview.

- Lees [Ondersteunde JSON-shapes](./time-series-insights-send-events.md#supported-json-shapes)voor meer informatie over ondersteunde JSON-vormen.
