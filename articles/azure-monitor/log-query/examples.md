---
title: Voor beelden van Azure Monitor-logboek query | Microsoft Docs
description: Voor beelden van logboek query's in Azure Monitor met behulp van de Kusto-query taal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: 9bfadf55e4f68bb7188b27e4ef5bc03e3955f375
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662045"
---
# <a name="azure-monitor-log-query-examples"></a>Voor beelden van Azure Monitor-logboek query
Dit artikel bevat verschillende voor beelden van [query's](log-query-overview.md) met behulp van de [Kusto-query taal](/azure/kusto/query/) om verschillende soorten logboek gegevens op te halen uit Azure monitor. Er worden verschillende methoden gebruikt om gegevens samen te voegen en te analyseren. u kunt deze voor beelden gebruiken om verschillende strategieën te identificeren die u voor uw eigen vereisten kunt gebruiken.  

Zie de [Kusto-taal referentie](https://docs.microsoft.com/azure/kusto/query/) voor meer informatie over de verschillende tref woorden die in deze voor beelden worden gebruikt. Door loop een [Les over het maken van query's](get-started-queries.md) als u geen ervaring hebt met Azure monitor.

## <a name="events"></a>Gebeurtenissen

### <a name="search-application-level-events-described-as-cryptographic"></a>Gebeurtenissen op toepassings niveau zoeken die worden beschreven als ' cryptografisch '
In dit voor beeld wordt de tabel **Events** doorzocht op records waarin **Eventlog** _Application_ and **RenderedDescription** bevat _Cryptographic_. Bevat records van de afgelopen 24 uur.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Zoek gebeurtenissen met betrekking tot het opmars hallen
Zoek tabellen **gebeurtenis** -en **SecurityEvents** voor records die _unmarshaling_vermelden.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Grafiek een week-over-week weergave van het aantal computers dat gegevens verzendt

In het volgende voor beeld wordt het aantal afzonderlijke computers voor de heartbeats per week verzonden.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Verouderde computers zoeken

In het volgende voor beeld wordt gezocht naar computers die in de afgelopen dag actief waren, maar geen heartbeats hebben verzonden.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>De meest recente heartbeat-record ophalen per computer-IP

In dit voor beeld wordt de laatste heartbeat-record voor elk IP-adres van de computer geretourneerd.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Beveiligde status records vergelijken met heartbeat-records

In dit voor beeld vindt u gerelateerde beveiligings status records en heartbeat-records, die overeenkomen op computer en tijd.
Houd er rekening mee dat het veld tijd wordt afgerond op de dichtstbijzijnde minuut. We hebben de volgende runtime bin-berekening gebruikt: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Beschikbaarheids tempo server

Het beschikbaarheids tempo van de server berekenen op basis van heartbeat-records. Beschik baarheid is gedefinieerd als ' minstens 1 heartbeat per uur '.
Als er dus een server beschikbaar was van 98 100 uur, is de beschik baarheid van 98%.

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


## <a name="multiple-data-types"></a>Meerdere gegevens typen

### <a name="chart-the-record-count-per-table"></a>Grafiek van het aantal records per tabel
In het volgende voor beeld worden alle records van alle tabellen van de afgelopen vijf uur verzameld en telt het aantal records in elke tabel. De resultaten worden weer gegeven in een timechart.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Alle logboeken die het afgelopen uur zijn verzameld per type tellen
In het volgende voor beeld wordt gezocht naar alles in het afgelopen uur en worden de records van elke tabel per **type**geteld. De resultaten worden weer gegeven in een staaf diagram.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Azure Diagnostics-records per categorie tellen
In dit voor beeld worden alle Azure Diagnostics-records voor elke unieke categorie geteld.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Een wille keurige record ophalen voor elke unieke categorie
In dit voor beeld wordt één wille keurige Azure Diagnostics-record opgehaald voor elke unieke categorie.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>De meest recente record per categorie ophalen
In dit voor beeld wordt de meest recente Azure Diagnostics-record in elke unieke categorie opgehaald.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Netwerkbewaking

### <a name="computers-with-unhealthy-latency"></a>Computers met een onjuiste latentie
In dit voor beeld wordt een lijst gemaakt van afzonderlijke computers met een onjuiste latentie.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Prestaties

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Computer prestatie records samen voegen om geheugen en CPU te correleren
In dit voor beeld worden de **prestatie** records van een bepaalde computer en twee tijd grafieken, de gemiddelde CPU en het maximale geheugen, gemaakt.

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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Grafiek van prestaties CPU-gebruik per computer
In dit voor beeld wordt het CPU-gebruik van computers die beginnen met _Contoso_, berekend en gegrafeerd.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Beveiligings status

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computers met een status duur van niet-rapportage beveiliging
In dit voor beeld worden computers weer gegeven die de beveiligings status _niet rapporteren_ hebben en de duur die ze in deze status hadden.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Beveiligde status records vergelijken met heartbeat-records
In dit voor beeld worden gerelateerde beveiligings status records en vitale records gevonden die overeenkomen op zowel de computer als de tijd.
Het veld tijd wordt afgerond op de dichtstbijzijnde minuut met behulp van **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Beveiligings records

### <a name="count-security-events-by-activity-id"></a>Beveiligings gebeurtenissen per activiteits-ID tellen


In dit voor beeld wordt gebruikgemaakt van de vaste structuur van de kolom **activiteit** : \<-ID\>-\<naam\>.
De waarde van de **activiteit** wordt geparseerd in twee nieuwe kolommen en telt het exemplaar van elke **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Beveiligings gebeurtenissen met betrekking tot machtigingen tellen
In dit voor beeld wordt het aantal **securityEvent** -records weer gegeven waarin de kolom **activiteit** de volledige term _machtigingen_bevat. De query is van toepassing op records die zijn gemaakt in de afgelopen 30 minuten.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Accounts zoeken die zich niet kunnen aanmelden bij computers met een beveiligings detectie
In dit voor beeld worden accounts gevonden en geteld die niet kunnen worden aangemeld bij computers waarop we een beveiligings detectie identificeren.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Zijn mijn beveiligings gegevens beschikbaar?
Het verkennen van gegevens begint vaak met de controle van de gegevens beschikbaarheid. In dit voor beeld wordt het aantal **SecurityEvent** -records in de afgelopen 30 minuten weer gegeven.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Naam en ID van de parser-activiteit
De twee voor beelden hieronder zijn afhankelijk van de vaste structuur van de kolom **activiteit** : \<ID\>-\<naam\>. In het eerste voor beeld wordt de operator **parse** gebruikt om waarden toe te wijzen aan twee nieuwe kolommen: **activityID** en **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

In dit voor beeld wordt de operator **Split** gebruikt om een matrix met afzonderlijke waarden te maken
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Expliciete referenties verwerken
In het volgende voor beeld ziet u een cirkel diagram van processen die in de afgelopen week expliciete referenties hebben gebruikt

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Meest actieve processen

In het volgende voor beeld ziet u de tijd lijn van de vijf meest voorkomende processen, in de afgelopen drie dagen.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Herhaalde mislukte aanmeldings pogingen zoeken op hetzelfde account uit verschillende Ip's

In het volgende voor beeld wordt gezocht naar mislukte aanmeldings pogingen van hetzelfde account van meer dan vijf verschillende Ip's in de afgelopen zes uur.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Gebruikers accounts zoeken waarvoor het aanmelden is mislukt 
In het volgende voor beeld worden gebruikers accounts die in de afgelopen dag niet meer dan vijf keer zijn aangemeld, geïdentificeerd en wanneer de gebruiker zich voor het laatst heeft geprobeerd aan te melden.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Met de instructie **samen voegen**kunnen we **controleren of dezelfde** verdachte accounts later kunnen worden aangemeld.

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

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>De gemiddelde grootte van het prestatie gebruiks rapport per computer berekenen

In dit voor beeld wordt de gemiddelde grootte van de rapporten over prestaties per computer berekend, in de afgelopen 3 uur.
De resultaten worden weer gegeven in een staaf diagram.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Timechart-latentie percentielen 50 en 95

In dit voor beeld worden de 50e en 95e percentielen van gerapporteerde **avgLatency** per uur in de afgelopen 24 uur berekend en gegrafieken.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Gebruik van specifieke computers vandaag
In dit voor beeld worden **gebruiks** gegevens opgehaald van de laatste dag voor computer namen die de teken reeks _ContosoFile_bevatten. De resultaten worden gesorteerd op **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Updates

### <a name="computers-still-missing-updates"></a>Er ontbreken nog updates op computers
In dit voor beeld ziet u een lijst met computers waarvoor een of meer essentiële updates een paar dagen geleden ontbreken en nog steeds ontbrekende updates.

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

- Raadpleeg de [Naslag Gids voor Kusto](/azure/kusto/query) voor meer informatie over de taal.
- Door loop een [Les over het schrijven van logboek query's in azure monitor](get-started-queries.md).
