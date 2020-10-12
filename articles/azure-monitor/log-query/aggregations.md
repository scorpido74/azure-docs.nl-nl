---
title: Aggregaties in Azure Monitor-logboek query's | Microsoft Docs
description: Hierin worden aggregatie functies beschreven in Azure Monitor-logboek query's die handige manieren bieden om uw gegevens te analyseren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670301"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregaties in Azure Monitor-logboek query's

> [!NOTE]
> U moet aan [de slag gaan met de analyse Portal](get-started-portal.md) en [aan de slag met query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel worden aggregatie functies in Azure Monitor-logboek query's beschreven die handige manieren bieden om uw gegevens te analyseren. Deze functies werken allemaal met de `summarize` operator die een tabel produceert met geaggregeerde resultaten van de invoer tabel.

## <a name="counts"></a>Counts

### <a name="count"></a>count
Het aantal rijen in de resultatenset tellen nadat filters zijn toegepast. In het volgende voor beeld wordt het totale aantal rijen in de _prestatie_ tabel van de laatste 30 minuten geretourneerd. Het resultaat wordt geretourneerd in een kolom met de naam *count_* tenzij u een specifieke naam toewijst:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Een timechart-visualisatie kan handig zijn om een trend over een bepaalde periode te bekijken:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

De uitvoer van dit voor beeld toont de trend lijn aantal prestatie records in intervallen van vijf minuten:

![Aantal trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Gebruik `dcount` en `dcountif` om afzonderlijke waarden in een specifieke kolom te tellen. De volgende query evalueert hoeveel afzonderlijke computers in het afgelopen uur heartbeats hebben verzonden:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Als u alleen de Linux-computers wilt tellen die heartbeats hebben verzonden, gebruikt u `dcountif` :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Subgroepen evalueren
Als u een aantal of andere aggregaties voor subgroepen in uw gegevens wilt uitvoeren, gebruikt u het `by` sleutel woord. Als u bijvoorbeeld het aantal afzonderlijke Linux-computers wilt tellen dat in elk land/elke regio heartbeats heeft verzonden:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Verenigde Staten    | 19                  |
|Canada           | 3                   |
|Ierland          | 0                   |
|Verenigd Koninkrijk   | 0                   |
|Nederland      | 2                   |


Als u nog kleinere subgroepen van uw gegevens wilt analyseren, voegt u aanvullende kolom namen toe aan de `by` sectie. U kunt bijvoorbeeld de afzonderlijke computers van elk land/elke regio per OSType tellen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentielen en variantie
Bij het evalueren van numerieke waarden is het normaal om ze te berekenen met behulp van `summarize avg(expression)` . De gemiddelden worden beïnvloed door extreme waarden die slechts enkele gevallen kenmerkend zijn. Als u het probleem wilt oplossen, kunt u minder gevoelige functies gebruiken, zoals `median` of `variance` .

### <a name="percentile"></a>Percentiel
Als u de mediaan waarde wilt weten, gebruikt u de `percentile` functie met een waarde om het percentiel op te geven:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

U kunt ook verschillende percentielen opgeven om een geaggregeerd resultaat te krijgen voor elk:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Dit kan aantonen dat sommige computer-Cpu's vergelijk bare mediaan waarden hebben, maar dat sommige gestage rond de mediaan zijn, maar dat andere computers veel lagere CPU-waarden hebben gerapporteerd, wat betekent dat er pieken zijn opgetreden.

### <a name="variance"></a>Variantie
Gebruik de methoden standaard afwijking en variantie om de variantie van een waarde direct te evalueren:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Een goede manier om de stabiliteit van het CPU-gebruik te analyseren is het combi neren van STDEV met de mediaan berekening:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zie andere lessen voor het gebruik van de [Kusto-query taal](/azure/kusto/query/) met Azure monitor-logboek gegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Samenvoegingen](joins.md)
- [Grafieken](charts.md)
