---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79129266"
---
### <a name="event-system-properties-mapping"></a>Toewijzing van eigenschappen van gebeurtenissysteem

> [!Note]
> * Systeemeigenschappen worden ondersteund voor gebeurtenissen met één record.
> * Voor `csv` toewijzing worden eigenschappen toegevoegd aan het begin van de record. Voor `json` toewijzing worden eigenschappen toegevoegd op basis van de naam die wordt weergegeven in de vervolgkeuzelijst.

Als u **de eigenschappen van het gebeurtenissysteem** hebt geselecteerd in de sectie **Gegevensbron** van de tabel, moet u de volgende eigenschappen opnemen in het tabelschema en de toewijzing.

**Voorbeeld van tabelschema**

Als uw gegevens drie`Timespan` `Metric`kolommen `Value`( , en ) `x-opt-enqueued-time` `x-opt-offset`en de eigenschappen bevatten die u opneemt, zijn en, maken of wijzigen van het tabelschema met behulp van deze opdracht:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Voorbeeld van CSV-toewijzing**

Voer de volgende opdrachten uit om gegevens toe te voegen aan het begin van de record. Let op ordinale waarden.

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
 
**VOORBEELD VAN JSON-toewijzing**

Gegevens worden toegevoegd met behulp van de namen van systeemeigenschappen zoals deze worden weergegeven in de lijst Eigenschappen van het **gegevensverbindingsbladgebeurtenissysteem.** **Data connection** Voer deze opdrachten uit:

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
