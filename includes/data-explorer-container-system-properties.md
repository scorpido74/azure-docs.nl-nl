---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129266"
---
### <a name="event-system-properties-mapping"></a>Toewijzing gebeurtenis systeem eigenschappen

> [!Note]
> * Systeem eigenschappen worden ondersteund voor gebeurtenissen met één record.
> * Voor `csv` toewijzing worden eigenschappen toegevoegd aan het begin van de record. Voor `json` toewijzing worden eigenschappen toegevoegd op basis van de naam die wordt weer gegeven in de vervolg keuzelijst.

Als u **gebeurtenis systeem eigenschappen** hebt geselecteerd in de sectie **gegevens bron** van de tabel, moet u de volgende eigenschappen in het tabel schema en de toewijzing toevoegen.

**Voor beeld tabel schema**

Als uw gegevens drie kolommen bevatten (`Timespan`, `Metric`en `Value`) en de eigenschappen die u toevoegt, zijn `x-opt-enqueued-time` en `x-opt-offset`, maakt of wijzigt u het tabel schema met behulp van de volgende opdracht:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Voor beeld van CSV-toewijzing**

Voer de volgende opdrachten uit om gegevens toe te voegen aan het begin van de record. Noteer de rang waarden.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Voor beeld van JSON-toewijzing**

Gegevens worden toegevoegd met behulp van de systeem eigenschappen namen zoals ze worden weer gegeven in de lijst met eigenschappen van de Blade **gegevens verbinding** van het **gebeurtenis systeem** . Voer deze opdrachten uit:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
