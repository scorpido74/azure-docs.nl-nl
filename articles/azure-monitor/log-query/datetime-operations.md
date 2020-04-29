---
title: Werken met datum-en tijd waarden in Azure Monitor-logboek query's | Microsoft Docs
description: Hierin wordt beschreven hoe u met datum-en tijd gegevens in Azure Monitor-logboek query's kunt werken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655375"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Werken met datum-en tijd waarden in Azure Monitor-logboek query's

> [!NOTE]
> U moet aan [de slag gaan met de analyse Portal](get-started-portal.md) en [aan de slag met query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel wordt beschreven hoe u met datum-en tijd gegevens in Azure Monitor-logboek query's kunt werken.


## <a name="date-time-basics"></a>Basis principes van datum en tijd
De Kusto-query taal bevat twee hoofd gegevens typen die zijn gekoppeld aan datums en tijden: datetime en time span. Alle datums worden uitgedrukt in UTC. Hoewel er meerdere datum notaties worden ondersteund, wordt de ISO8601-indeling de voor keur. 

TimeSpans worden uitgedrukt als een decimaal gevolgd door een tijds eenheid:

|Steno   | tijds eenheid    |
|:---|:---|
|d           | day          |
|h           | uur         |
|m           | minuut       |
|s           | seconde       |
|Mevrouw          | milliseconde  |
|wacht | wacht  |
|beurs        | nano seconden   |

Datetimes kunnen worden gemaakt door een teken reeks te casten met behulp van de `todatetime` operator. Als u bijvoorbeeld de VM-heartbeats wilt bekijken die in een bepaalde periode zijn verzonden `between` , gebruikt u de operator om een tijds bereik op te geven.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Een ander gebruikelijk scenario is het vergelijken van een datum/tijd met de huidige. Als u bijvoorbeeld alle heartbeats in de afgelopen twee minuten wilt zien, kunt u de `now` operator gebruiken samen met een time span die twee minuten vertegenwoordigt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Er is ook een snelkoppeling beschikbaar voor deze functie:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

De kortste en meest Lees bare methode gebruiken de `ago` operator:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Stel dat u in plaats van de begin-en eind tijd te weten weet wat de begin tijd en de duur zijn. U kunt de query als volgt herschrijven:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Tijds eenheden converteren
Mogelijk wilt u een datum-/tijdwaarde of time span uitdrukken in een andere tijds eenheid dan de standaard waarde. Als u bijvoorbeeld fout gebeurtenissen van de afgelopen 30 minuten bekijkt en een berekende kolom nodig hebt waarin wordt weer gegeven hoe lang geleden de gebeurtenis heeft plaatsgevonden:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

De `timeAgo` kolom bevat waarden zoals: "00:09:31.5118992", wat betekent dat ze zijn opgemaakt als uu: mm: SS. fffffff. Als u deze waarden wilt opmaken in `numver` minuten sinds de begin tijd, deelt u deze waarde door ' 1 minuut ':

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregaties en Bucket op basis van tijds intervallen
Een ander gebruikelijk scenario is de nood zaak om statistieken te verkrijgen over een bepaalde periode van een bepaalde tijd. Voor dit scenario kan een `bin` operator worden gebruikt als onderdeel van een component samenvatten.

Gebruik de volgende query om het aantal gebeurtenissen op te halen dat in het afgelopen halfjaar elke vijf minuten is opgetreden:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Deze query produceert de volgende tabel:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Een andere manier om buckets met resultaten te maken, is met behulp van `startofday`functies, zoals:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Deze query levert de volgende resultaten op:

|tijdstempel|count_|
|--|--|
|2018-07-28T00:00:00.000|7.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5.416|


## <a name="time-zones"></a>Tijd zones
Omdat alle datum-/tijdwaarden worden uitgedrukt in UTC, is het vaak handig deze waarden te converteren naar de lokale tijd zone. Gebruik deze berekening bijvoorbeeld om UTC naar PST-tijden te converteren:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Gerelateerde functies

| Categorie | Functie |
|:---|:---|
| Gegevens typen converteren | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Waarde afronden naar bin-grootte | [dockopslaglocatie](/azure/kusto/query/binfunction) |
| Een specifieke datum of tijd ophalen | [ago](/azure/kusto/query/agofunction) [nu](/azure/kusto/query/nowfunction) geleden   |
| Onderdeel van waarde ophalen | [datetime_part](/azure/kusto/query/datetime-partfunction) [getMonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [DayofWeek](/azure/kusto/query/dayofweekfunction) [DAYOFYEAR](/azure/kusto/query/dayofyearfunction) [WeekofYear](/azure/kusto/query/weekofyearfunction) |
| Een relatieve datum waarde ophalen  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [opsomming](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het gebruik van de [Kusto-query taal](/azure/kusto/query/) met Azure monitor-logboek gegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Samenvoegingen](joins.md)
- [Grafieken](charts.md)
