---
title: Standaardeigenschappen in Azure Monitor-logboekrecords | Microsoft Documenten
description: Beschrijft eigenschappen die gemeenschappelijk zijn voor meerdere gegevenstypen in Azure Monitor-logboeken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274474"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standaardeigenschappen in Azure Monitor-logboeken
Gegevens in Azure Monitor-logboeken worden [opgeslagen als een set records in een Log Analytics-werkruimte of application Insights-toepassing,](../log-query/logs-structure.md)elk met een bepaald gegevenstype met een unieke set eigenschappen. Veel gegevenstypen hebben standaardeigenschappen die gebruikelijk zijn voor meerdere typen. In dit artikel worden deze eigenschappen beschreven en worden voorbeelden gegeven van hoe u ze gebruiken in query's.

> [!NOTE]
> Sommige standaardeigenschappen worden niet weergegeven in de schemaweergave of intellisense in Log Analytics en worden niet weergegeven in queryresultaten, tenzij u de eigenschap in de uitvoer expliciet opgeeft.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated en tijdstempel
De eigenschappen **TimeGenerated** (Log Analytics workspace) en **timestamp** (Application Insights application) bevatten de datum en tijd waarop de record is gemaakt door de gegevensbron. Zie [Tijd voor het innemen van gegevens registreren in Azure Monitor](data-ingestion-time.md) voor meer informatie.

**TimeGenerated** en **timestamp** bieden een algemene eigenschap die u gebruiken voor het filteren of samenvatten per tijd. Wanneer u een tijdsbereik selecteert voor een weergave of dashboard in de Azure-portal, wordt TimeGenerated of timestamp gebruikt om de resultaten te filteren. 

### <a name="examples"></a>Voorbeelden

Met de volgende query wordt het aantal foutgebeurtenissen geretourneerd dat is gemaakt voor elke dag in de vorige week.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

In de volgende query wordt het aantal uitzonderingen geretourneerd dat is gemaakt voor elke dag in de vorige week.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
De eigenschap ** \_TimeReceived** bevat de datum en tijd waarop de record is ontvangen door het innamepunt azure-monitor in de Azure-cloud. Dit kan handig zijn voor het identificeren van latentieproblemen tussen de gegevensbron en de cloud. Een voorbeeld hiervan is een netwerkprobleem dat een vertraging veroorzaakt met gegevens die door een agent worden verzonden. Zie [Tijd voor het innemen van gegevens registreren in Azure Monitor](data-ingestion-time.md) voor meer informatie.

De volgende query geeft de gemiddelde latentie per uur voor gebeurtenisrecords van een agent. Dit omvat de tijd van de agent naar de cloud en de totale tijd dat de record beschikbaar is voor logboekquery's.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type en itemType
De eigenschappen **Type** (Log Analytics workspace) en **itemType** (Application Insights application) behouden de naam van de tabel waaruit de record is opgehaald, waarvan ook kan worden aangenomen als recordtype. Deze eigenschap is handig in query's die records uit `search` meerdere tabel, zoals die welke de operator gebruiken, combineren om onderscheid te maken tussen records van verschillende typen. **$table** kan op sommige plaatsen in plaats van **type** worden gebruikt.

### <a name="examples"></a>Voorbeelden
Met de volgende query wordt het aantal records per type geretourneerd dat in het afgelopen uur is verzameld.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
De eigenschap ** \_ItemId** bevat een unieke id voor de record.


## <a name="_resourceid"></a>\_ResourceId
De eigenschap ** \_ResourceId** bevat een unieke id voor de resource waarmee de record is gekoppeld. Dit geeft u een standaardeigenschap die u gebruiken om uw query alleen te gebruiken voor records van een bepaalde bron of om gerelateerde gegevens in meerdere tabellen samen te voegen.

Voor Azure-resources is de waarde van **_ResourceId** de URL van [azure-bron-id](../../azure-resource-manager/templates/template-functions-resource.md). De eigenschap is momenteel beperkt tot Azure-resources, maar wordt uitgebreid tot bronnen buiten Azure, zoals on-premises computers.

> [!NOTE]
> Sommige gegevenstypen hebben al velden die Azure-bron-id of ten minste delen ervan bevatten, zoals abonnements-ID. Hoewel deze velden worden bewaard voor achterwaartse compatibiliteit, wordt aanbevolen om de _ResourceId te gebruiken om cross correlatie uit te voeren, omdat het consistenter zal zijn.

### <a name="examples"></a>Voorbeelden
Met de volgende query worden prestatie- en gebeurtenisgegevens voor elke computer samengemaakt. Het toont alle gebeurtenissen met een ID van _101_ en processorgebruik meer dan 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

De volgende query voegt _AzureActivity-records_ samen met _SecurityEvent-records._ Het toont alle activiteiten met gebruikers die zijn aangemeld bij deze machines.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

De volgende query parseert **_ResourceId** en verzamelt gefactureerde gegevensvolumes per Azure-abonnement.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Gebruik `union withsource = tt *` deze query's spaarzaam als scans over gegevenstypen zijn duur uit te voeren.

## <a name="_isbillable"></a>\_IsFactable
De eigenschap ** \_IsBillable** geeft aan of ingenomen gegevens factureerbaar zijn. Gegevens met ** \_IsBillable die** gelijk zijn aan _false,_ worden gratis verzameld en niet gefactureerd op uw Azure-account.

### <a name="examples"></a>Voorbeelden
Als u een lijst met computers wilt krijgen die gefactureerde gegevenstypen verzenden, gebruikt u de volgende query:

> [!NOTE]
> Gebruik query's spaarzaam, `union withsource = tt *` omdat scans tussen gegevenstypen duur zijn om uit te voeren. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Dit kan worden uitgebreid om het aantal computers per uur dat gefactureerde gegevenstypen verzendt, terug te sturen:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_Gefactureerdformaat
De ** \_eigenschap BilledSize** geeft de grootte aan in bytes van gegevens die worden gefactureerd aan uw Azure-account als ** \_IsBillable** waar is.


### <a name="examples"></a>Voorbeelden
Als u de grootte wilt zien van de `_BilledSize` ingenomen gebeurtenissen per computer, gebruikt u de eigenschap die de grootte in bytes biedt:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Als u de grootte wilt zien van de ingenomen gebeurtenissen per abonnement, gebruikt u de volgende query:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Als u de grootte wilt zien van de ingenomen overste gebeurtenissen per resourcegroep, gebruikt u de volgende query:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Als u het aantal ingenomen gebeurtenissen per computer wilt zien, gebruikt u de volgende query:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Als u het aantal gefactureerde gebeurtenissen per computer wilt zien, gebruikt u de volgende query: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Als u het aantal factureerbare gegevenstypen van een specifieke computer wilt bekijken, gebruikt u de volgende query:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Volgende stappen

- Lees meer over hoe [Azure Monitor-logboekgegevens worden opgeslagen.](../log-query/log-query-overview.md)
- Krijg een les over [het schrijven van log query's](../../azure-monitor/log-query/get-started-queries.md).
- Ontvang een les over [het samenvoegen van tabellen in logquery's.](../../azure-monitor/log-query/joins.md)
