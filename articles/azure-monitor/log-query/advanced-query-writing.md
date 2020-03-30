---
title: Geavanceerde query's in Azure Monitor | Microsoft Documenten
description: In dit artikel vindt u een zelfstudie voor het gebruik van de Analytics-portal om query's te schrijven in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670284"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Geavanceerde query's schrijven in Azure Monitor

> [!NOTE]
> U moet aan [de slag gaan met Azure Monitor Log Analytics](get-started-portal.md) en Aan de slag met [query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Code opnieuw gebruiken met laten
Hiermee `let` u resultaten toewijzen aan een variabele en deze later in de query verwijzen:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

U ook constante waarden toewijzen aan variabelen. Dit ondersteunt een methode voor het instellen van parameters voor de velden die u elke keer dat u de query uitvoert moet wijzigen. Wijzig deze parameters indien nodig. Bijvoorbeeld om de vrije schijfruimte en het vrije geheugen (in percentiels) in een bepaald tijdvenster te berekenen:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Dit maakt het eenvoudig om de begintijd te wijzigen wanneer u de query de volgende keer uitvoert.

### <a name="local-functions-and-parameters"></a>Lokale functies en parameters
Gebruik `let` instructies om functies te maken die in dezelfde query kunnen worden gebruikt. Definieer bijvoorbeeld een functie die een datumtijdveld (in de UTC-indeling) neemt en converteert naar een standaard-Amerikaanse indeling. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Afdrukken
`print`hiermee wordt een tabel met één kolom en één rij retourneren, met het resultaat van een berekening. Dit wordt vaak gebruikt in gevallen waarin u een eenvoudige berekening nodig hebt. Bijvoorbeeld om de huidige tijd in PST te zoeken en een kolom met EST toe te voegen:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`u een set gegevens definiëren. U geeft een schema en een set waarden op en pipetde de tabel vervolgens naar andere query-elementen. Bijvoorbeeld om een tabel met RAM-gebruik te maken en de gemiddelde waarde per uur te berekenen:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Gegevenstabelconstructies zijn ook erg handig bij het maken van een opzoektabel. Als u bijvoorbeeld tabelgegevens zoals gebeurtenis-namen uit de tabel _SecurityEvent_ wilt toewijzen aan gebeurtenissendie elders worden `datatable` vermeld, maakt u een opzoektabel met de gebeurtenistypen die worden gebruikt en sluit u aan bij deze gegevenstabel met _SecurityEvent-gegevens:_

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het gebruik van de [Kusto-querytaal](/azure/kusto/query/) met Azure Monitor-logboekgegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
