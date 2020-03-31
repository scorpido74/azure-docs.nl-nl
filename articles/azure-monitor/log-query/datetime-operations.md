---
title: Werken met datumtijdwaarden in Azure Monitor-logboekquery's| Microsoft Documenten
description: Beschrijft hoe u werken met datum- en tijdgegevens in azure monitorlogboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655375"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Werken met datumtijdwaarden in Azure Monitor-logboekquery's

> [!NOTE]
> U moet aan [de slag gaan met de Analytics-portal](get-started-portal.md) en aan de slag gaan met [query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel wordt beschreven hoe u werken met datum- en tijdgegevens in azure monitorlogboekquery's.


## <a name="date-time-basics"></a>Basisbeginselen van datumtijd
De Kusto-querytaal heeft twee belangrijke gegevenstypen die zijn gekoppeld aan datums en tijden: datumtijd en tijdspanne. Alle datums worden uitgedrukt in UTC. Hoewel meerdere datumtijdnotaties worden ondersteund, heeft de ISO8601-indeling de voorkeur. 

Tijdspannes worden uitgedrukt als een decimale, gevolgd door een tijdseenheid:

|Steno   | tijdseenheid    |
|:---|:---|
|d           | day          |
|h           | uur         |
|m           | minuut       |
|s           | seconde       |
|Mevrouw          | Milliseconde  |
|microseconde | microseconde  |
|Teek        | nanoseconde   |

Datetimes kunnen worden gemaakt door `todatetime` een tekenreeks te gieten met behulp van de operator. Als u bijvoorbeeld de VM-heartbeats wilt controleren die `between` in een bepaald tijdsbestek zijn verzonden, gebruikt u de operator om een tijdsbereik op te geven.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Een ander veelvoorkomend scenario is het vergelijken van een datumtijd met het heden. Als u bijvoorbeeld alle hartslagen van de afgelopen twee `now` minuten wilt zien, u de operator gebruiken samen met een tijdspanne die twee minuten vertegenwoordigt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Voor deze functie is ook een snelkoppeling beschikbaar:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

De kortste en meest leesbare methode `ago` is echter het gebruik van de operator:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Stel dat in plaats van de begin- en eindtijd te kennen, u de begintijd en de duur kent. U de query als volgt herschrijven:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Tijdeenheden converteren
U een datumtijd of tijdspanne uitdrukken in een andere tijdseenheid dan de standaardinstelling. Als u bijvoorbeeld foutgebeurtenissen van de laatste 30 minuten bekijkt en een berekende kolom nodig hebt die aangeeft hoe lang geleden de gebeurtenis is gebeurd:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

De `timeAgo` kolom bevat waarden als: "00:09:31.5118992", wat betekent dat ze zijn opgemaakt als hh:mm:sss.fffffff. Als u deze waarden wilt `numver` opmaken op de minuten van minuten sinds de begintijd, deelt u die waarde door "1 minuut":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregaties en bucketing door tijdsintervallen
Een ander gemeenschappelijk scenario is de noodzaak om statistieken te verkrijgen over een bepaalde periode in een bepaalde tijd graan. Voor dit scenario `bin` kan een operator worden gebruikt als onderdeel van een samenvattende clausule.

Gebruik de volgende query om het aantal gebeurtenissen op te vragen dat zich elke 5 minuten tijdens het laatste half uur heeft voorgedaan:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Met deze query wordt de volgende tabel geproduceerd:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Een andere manier om buckets met resultaten `startofday`te maken, is het gebruik van functies, zoals:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Deze query levert de volgende resultaten op:

|tijdstempel|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.00.000|5,416|


## <a name="time-zones"></a>Tijdzones
Aangezien alle datumtijdwaarden worden uitgedrukt in UTC, is het vaak handig om deze waarden om te zetten in de lokale tijdzone. Gebruik deze berekening bijvoorbeeld om UTC om te zetten in PST-tijden:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Gerelateerde functies

| Categorie | Functie |
|:---|:---|
| Gegevenstypen converteren | [tot nu toe tot](/azure/kusto/query/todatetimefunction)  [aanuurtijd](/azure/kusto/query/totimespanfunction)  |
| Ronde waarde naar opslaglocatiegrootte | [Bin](/azure/kusto/query/binfunction) |
| Een specifieke datum of tijd | [geleden](/azure/kusto/query/agofunction) [nu](/azure/kusto/query/nowfunction)   |
| Krijg een deel van de waarde | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekvan het jaar](/azure/kusto/query/weekofyearfunction) |
| Een relatieve datumwaarde  | [einde van de](/azure/kusto/query/endofdayfunction) [week endofmonth](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [beginvanmaand](/azure/kusto/query/startofmonthfunction) [beginvan jaar](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het gebruik van de [Kusto-querytaal](/azure/kusto/query/) met Azure Monitor-logboekgegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
