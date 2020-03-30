---
title: Aggregaties in Azure Monitor-logboekquery's| Microsoft Documenten
description: Beschrijft aggregatiefuncties in Azure Monitor-logboekquery's die nuttige manieren bieden om uw gegevens te analyseren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670301"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregaties in azure monitor-logboekquery's

> [!NOTE]
> U moet aan [de slag gaan met de Analytics-portal](get-started-portal.md) en aan de slag gaan met [query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel worden aggregatiefuncties beschreven in azure monitor-logboekquery's die nuttige manieren bieden om uw gegevens te analyseren. Deze functies werken `summarize` allemaal samen met de operator die een tabel produceert met geaggregeerde resultaten van de invoertabel.

## <a name="counts"></a>Telt

### <a name="count"></a>count
Tel het aantal rijen in de resultaatset nadat filters zijn toegepast. In het volgende voorbeeld wordt het totale aantal rijen in de _perf-tabel_ van de laatste 30 minuten geretourneerd. Het resultaat wordt geretourneerd in een kolom met de naam *count_,* tenzij u deze een specifieke naam toewijst:


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

Een tijddiagramvisualisatie kan handig zijn om een trend in de loop van de tijd te zien:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

De uitvoer uit dit voorbeeld toont de trendlijn van het aantal perf-recordtellingen in intervallen van 5 minuten:

![Tellingtrend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Afzonderlijke `dcount` `dcountif` waarden in een specifieke kolom gebruiken en tellen. In de volgende query wordt geëvalueerd hoeveel verschillende computers in het afgelopen uur heartbeats hebben verzonden:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Als u alleen de Linux-computers `dcountif`wilt tellen die heartbeats hebben verzonden, gebruikt u:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Subgroepen evalueren
Als u een aantal of andere aggregaties `by` op subgroepen in uw gegevens wilt uitvoeren, gebruikt u het trefwoord. Bijvoorbeeld om het aantal verschillende Linux-computers te tellen dat heartbeats in elk land/regio heeft verzonden:

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


Als u nog kleinere subgroepen van uw gegevens `by` wilt analyseren, voegt u extra kolomnamen toe aan de sectie. U bijvoorbeeld de verschillende computers van elk land/regio per OSType tellen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiels en variantie
Bij de beoordeling van numerieke waarden is `summarize avg(expression)`het gebruikelijk om ze gemiddeld te gebruiken met behulp van . Gemiddelden worden beïnvloed door extreme waarden die slechts een paar gevallen karakteriseren. Om dat probleem op te lossen, `median` kunt `variance`u minder gevoelige functies gebruiken, zoals of .

### <a name="percentile"></a>Percentiel
Als u de mediaanwaarde wilt zoeken, gebruikt u de `percentile` functie met een waarde om het percentiel op te geven:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

U ook verschillende percentiels opgeven om een geaggregeerd resultaat voor elk te krijgen:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Dit kan aantonen dat sommige computer CPU's hebben vergelijkbare mediane waarden, maar terwijl sommige zijn stabiel rond de mediaan, andere computers hebben gemeld veel lagere en hogere CPU-waarden wat betekent dat ze ervaren spikes.

### <a name="variance"></a>Variantie
Als u de variantie van een waarde rechtstreeks wilt evalueren, gebruikt u de standaarddeviatie- en variantiemethoden:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Een goede manier om de stabiliteit van het CPU-gebruik te analyseren is om stdev te combineren met de mediaan berekening:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Bekijk andere lessen voor het gebruik van de [Kusto-querytaal](/azure/kusto/query/) met Azure Monitor-logboekgegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
