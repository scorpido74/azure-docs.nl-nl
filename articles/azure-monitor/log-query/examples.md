---
title: Voorbeelden van azure monitor-logboekquery's | Microsoft Documenten
description: Voorbeelden van logboekquery's in Azure Monitor met de Kusto-querytaal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480280"
---
# <a name="azure-monitor-log-query-examples"></a>Voorbeelden van azure monitor-logboekquery's
Dit artikel bevat verschillende voorbeelden van [query's](log-query-overview.md) die de [Kusto-querytaal](/azure/kusto/query/) gebruiken om verschillende typen logboekgegevens uit Azure Monitor op te halen. Verschillende methoden worden gebruikt om gegevens te consolideren en te analyseren, zodat u deze voorbeelden gebruiken om verschillende strategieën te identificeren die u mogelijk voor uw eigen vereisten gebruikt.  

Zie de [Kusto-taalverwijzing](https://docs.microsoft.com/azure/kusto/query/) voor meer informatie over de verschillende zoekwoorden die in deze voorbeelden worden gebruikt. Ga door een [les over het maken van query's](get-started-queries.md) als u nieuw bent bij Azure Monitor.

## <a name="events"></a>Gebeurtenissen

### <a name="search-application-level-events-described-as-cryptographic"></a>Gebeurtenissen op toepassingsniveau zoeken die worden omschreven als 'Cryptografisch'
In dit voorbeeld wordt in de tabel **Gebeurtenissen** gezocht naar records waarin **EventLog** _toepassing_ is en **RenderedDescription** _cryptografische_bevat. Inclusief records van de afgelopen 24 uur.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Zoekgebeurtenissen met betrekking tot unmarshaling
Zoek gebeurtenissen **en** **beveiligingsgebeurtenissen** zoeken naar records waarin _unmarshaling wordt vermeld_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Hartslag

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Een week-over-weekweergave weergeven van het aantal computers dat gegevens verzendt

In het volgende voorbeeld wordt het aantal verschillende computers in kaart gebracht dat elke week heartbeats heeft verzonden.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Verouderde computers zoeken

In het volgende voorbeeld worden computers gevonden die op de laatste dag actief waren, maar in het afgelopen uur geen heartbeats hebben verzonden.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Ontvang de nieuwste hartslagrecord per computer-IP

In dit voorbeeld wordt de laatste heartbeatrecord voor elk computer-IP geretourneerd.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Beveiligde statusrecords koppelen aan heartbeatrecords

In dit voorbeeld worden gerelateerde beveiligingsstatusrecords en heartbeatrecords gevonden die zijn afgestemd op zowel computer als tijd.
Let op: het tijdsveld wordt afgerond op de dichtstbijzijnde minuut. We gebruikten runtime bin `round_time=bin(TimeGenerated, 1m)`berekening om dat te doen: .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Beschikbaarheidssnelheid van de server

Bereken de beschikbaarheidssnelheid van de server op basis van hartslagrecords. Beschikbaarheid wordt gedefinieerd als "ten minste 1 hartslag per uur".
Dus, als een server beschikbaar was 98 van 100 uur, de beschikbaarheid is 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Meerdere gegevenstypen

### <a name="chart-the-record-count-per-table"></a>Het recordaantal per tabel weergeven
In het volgende voorbeeld worden alle records van alle tabellen van de afgelopen vijf uur opgehaald en wordt geteld hoeveel records er in elke tabel stonden. De resultaten worden weergegeven in een tijdgrafiek.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Alle logboeken tellen die in het afgelopen uur zijn verzameld op type
In het volgende voorbeeld wordt in alles doorzocht dat in het afgelopen uur is gerapporteerd en worden de records van elke tabel op **Type geteld.** De resultaten worden weergegeven in een staafdiagram.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Azure-diagnostische records tellen per categorie
In dit voorbeeld worden alle Azure-diagnostische records voor elke unieke categorie geteld.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Krijg een willekeurige record voor elke unieke categorie
In dit voorbeeld wordt één willekeurige Azure-diagnoserecord voor elke unieke categorie ontvangen.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Ontvang de nieuwste record per categorie
In dit voorbeeld wordt de nieuwste Azure-diagnostische record in elke unieke categorie ontvangen.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Netwerkbewaking

### <a name="computers-with-unhealthy-latency"></a>Computers met ongezonde latentie
In dit voorbeeld wordt een lijst met afzonderlijke computers met een ongezonde latentie.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestaties

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Deelnemen aan computerperf-records om geheugen en CPU te correleren
In dit voorbeeld worden de **perf-records** van een bepaalde computer gecorreleerd en worden twee tijdgrafieken, de gemiddelde CPU en het maximale geheugen, gecorreleerd.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Grafiek perf CPU-gebruik per computer
In dit voorbeeld wordt het CPU-gebruik berekend en in kaart gebracht van computers die beginnen met _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Beveiligingsstatus

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computers met de statusduur van de niet-rapportagestatus
In dit voorbeeld worden computers weergegeven met een beveiligingsstatus _van Niet rapporteren_ en de duur waarin ze zich in deze status bevonden.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Beveiligde statusrecords koppelen aan heartbeatrecords
In dit voorbeeld worden gerelateerde beveiligingsstatusrecords en heartbeatrecords gevonden die op zowel computer als tijd zijn afgestemd.
Het tijdsveld wordt afgerond op de dichtstbijzijnde minuut met behulp van **opslaglocatie**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Beveiligingsrecords

### <a name="count-security-events-by-activity-id"></a>Beveiligingsgebeurtenissen tellen op activiteits-id


Dit voorbeeld is gebaseerd op de vaste \<\>-\<structuur\>van de kolom **Activiteit:** ID-naam .
Het ontlijnt de **activiteitswaarde** in twee nieuwe kolommen en telt het optreden van elke **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Beveiligingsgebeurtenissen tellen met betrekking tot machtigingen
In dit voorbeeld wordt het aantal **records van securityEvent** weergegeven, waarin de kolom **Activiteit** de hele term _Machtigingen_bevat. De query is van toepassing op records die in de afgelopen 30 minuten zijn gemaakt.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Accounts zoeken die niet kunnen inloggen vanaf computers met een beveiligingsdetectie
In dit voorbeeld worden accounts gevonden en geteld die niet zijn aangemeld vanaf computers waarop we een beveiligingsdetectie identificeren.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Zijn mijn beveiligingsgegevens beschikbaar?
Het starten van gegevensverkenning begint vaak met gegevensbeschikbaarheidscontrole. In dit voorbeeld wordt het aantal **SecurityEvent-records** in de afgelopen 30 minuten weergegeven.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Naam en id van parse-activiteit
De twee onderstaande voorbeelden zijn gebaseerd op \<de\>-\<\>vaste structuur van de kolom **Activiteit:** ID-naam . In het eerste voorbeeld wordt de **operator parse** gebruikt om waarden toe te wijzen aan twee nieuwe kolommen: **activityID** en **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

In dit voorbeeld wordt de **gesplitste** operator gebruikt om een array met afzonderlijke waarden te maken
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Expliciete referentiesprocessen
In het volgende voorbeeld wordt een cirkeldiagram weergegeven met processen die in de afgelopen week expliciete referenties hebben gebruikt

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Best lopende processen

In het volgende voorbeeld wordt een tijdslijn weergegeven voor de vijf meest voorkomende processen van de afgelopen drie dagen.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Herhaalde mislukte inlogpogingen zoeken door hetzelfde account van verschillende IP's

In het volgende voorbeeld worden mislukte inlogpogingen van hetzelfde account van meer dan vijf verschillende IP's in de afgelopen zes uur gevonden.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Gebruikersaccounts zoeken die niet zijn aangemeld 
In het volgende voorbeeld worden gebruikersaccounts geïdentificeerd die op de laatste dag niet meer dan vijf keer zijn aangemeld en wanneer ze voor het laatst hebben geprobeerd in te loggen.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Met behulp van **join**, en **laat** verklaringen kunnen we controleren of dezelfde verdachte accounts later in staat waren om in te loggen met succes.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Gebruik

Het `Usage` gegevenstype kan worden gebruikt om het ingenomen gegevensvolume te volgen op oplossing of gegevenstype. Er zijn andere technieken om ingenomen gegevensvolumes te bestuderen per [computer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) of [Azure-abonnement, resourcegroep of resource.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription)

#### <a name="data-volume-by-solution"></a>Gegevensvolume per oplossing

De query die wordt gebruikt om het factureerbare gegevensvolume per oplossing van de afgelopen maand te bekijken (met uitzondering van de laatste gedeeltelijke dag) is:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Houd er `where IsBillable = true` rekening mee dat de clausule gegevenstypen filtert uit bepaalde oplossingen waarvoor geen innamekosten zijn.  Ook de `TimeGenerated` clausule met is alleen om ervoor te zorgen dat de query-ervaring in de Azure-portal zal terug kijken dan de standaard 24 uur. Wanneer u het gegevenstype `EndTime` Gebruik gebruikt en `StartTime` de tijdbuckets weergeeft waarvoor de resultaten worden weergegeven. 

#### <a name="data-volume-by-type"></a>Gegevensvolume per type

U verder inzoomen om gegevenstrends te bekijken op gegevenstype:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Of om een tabel te zien op oplossing en type voor de afgelopen maand,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Sommige velden van het gegevenstype Gebruik, terwijl ze nog in het schema staan, zijn afgeschaft en zullen hun waarden niet meer worden ingevuld. Dit zijn **computer-** en velden met betrekking tot inname (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** en **AverageProcessingTimeMs**.

## <a name="updates"></a>Updates

### <a name="computers-still-missing-updates"></a>Computers ontbreken nog steeds updates
In dit voorbeeld wordt een lijst weergegeven met computers die een of meer kritieke updates een paar dagen geleden misten en nog steeds updates missen.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Kusto-taalverwijzing](/azure/kusto/query) voor meer informatie over de taal.
- Loop door een [les over het schrijven van logquery's in Azure Monitor.](get-started-queries.md)