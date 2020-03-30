---
title: Waarschuwingsquery's aanmelden in Azure Monitor | Microsoft Documenten
description: Bevat aanbevelingen voor het schrijven van efficiënte query's voor logboekwaarschuwingen in Azure Monitor-updates en een proces voor het converteren van bestaande query's.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667785"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Waarschuwingsquery's aanmelden in Azure Monitor
[Waarschuwingsregels op basis van Azure Monitor-logboeken](alerts-unified-log.md) worden regelmatig uitgevoerd, dus u moet ervoor zorgen dat ze zijn geschreven om overhead en latentie te minimaliseren. In dit artikel vindt u aanbevelingen voor het schrijven van efficiënte query's voor logboekwaarschuwingen en een proces voor het converteren van bestaande query's. 

## <a name="types-of-log-queries"></a>Typen logboekquery's
[Log query's in Azure Monitor](../log-query/log-query-overview.md) beginnen met een tabel of een [zoek-](/azure/kusto/query/searchoperator) of [vakbondsoperator.](/azure/kusto/query/unionoperator)

De volgende query wordt bijvoorbeeld beperkt tot de _securityevent-tabel_ en zoekt naar specifieke gebeurtenis-id. Dit is de enige tabel die de query moet verwerken.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Query's die `search` `union` beginnen met of u toestaan om te zoeken in meerdere kolommen in een tabel of zelfs meerdere tabellen. In de volgende voorbeelden worden meerdere methoden weergegeven voor het zoeken in de term _Geheugen:_

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Hoewel `search` `union` en nuttig zijn tijdens het verkennen van gegevens, zijn ze minder efficiënt dan het gebruik van een tabel, omdat ze in meerdere tabellen moeten scannen. Aangezien query's in waarschuwingsregels regelmatig worden uitgevoerd, kan dit leiden tot overmatige overhead toevoegen van latentie aan de waarschuwing. Vanwege deze overhead moeten query's voor logboekwaarschuwingsregels in Azure altijd beginnen met een tabel om een duidelijk bereik te definiëren, wat zowel de queryprestaties als de relevantie van de resultaten verbetert.

## <a name="unsupported-queries"></a>Niet-ondersteunde query's
Vanaf 11 januari 2019 worden logboekwaarschuwingsregels die `search`worden `union` gebruikt of operators worden niet ondersteund in Azure-portal. Als u deze operatoren in een waarschuwingsregel gebruikt, wordt een foutbericht weergegeven. Bestaande waarschuwingsregels en waarschuwingsregels die zijn gemaakt en bewerkt met de Api log Analytics, worden niet beïnvloed door deze wijziging. U moet nog steeds overwegen om alle waarschuwingsregels te wijzigen die dit soort query's gebruiken om hun efficiëntie te verbeteren.  

Logwaarschuwingsregels met [behulp van query's met cross-resource](../log-query/cross-workspace-query.md) worden `union`niet beïnvloed door deze wijziging, omdat het gebruik van queryquery's met verschillende bronnen wordt gebruikt, waardoor het querybereik wordt beperkt tot specifieke bronnen. Dit is geen `union *` equivalent waarvan niet kan worden gebruikt.  Het volgende voorbeeld is geldig in een regel voor logboekwaarschuwingen:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Logboekwaarschuwingen voor [cross-resourcequery's](../log-query/cross-workspace-query.md) worden ondersteund in de nieuwe [api voor geplandeQueryregels](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor gebruikt standaard de [verouderde Log Analytics Alert API](api-alerts.md) voor het maken van nieuwe logboekwaarschuwingsregels vanuit Azure portal, tenzij u overstapt van de verouderde Api voor [logboekwaarschuwingen.](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Na de switch wordt de nieuwe API de standaardvoor nieuwe waarschuwingsregels in azure-portal en u regels voor waarschuwingen voor querylogboeken met meerdere resources maken. U [waarschuwingsregels voor querylogboeken met meerdere bronnen](../log-query/cross-workspace-query.md) maken zonder de overstap te maken met de [ARM-sjabloon voor de geplande QueryRules-API,](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) maar deze waarschuwingsregel is beheersbaar via [de GeplandeQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit azure-portal.

## <a name="examples"></a>Voorbeelden
De volgende voorbeelden zijn logboekquery's die gebruik maken `search` van en `union` stappen bieden die u gebruiken om deze query's te wijzigen voor gebruik met waarschuwingsregels.

### <a name="example-1"></a>Voorbeeld 1
U wilt een logboekwaarschuwingsregel maken met de volgende `search`query waarmee prestatiegegevens worden opgehaald met : 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Als u deze query wilt wijzigen, gaat u aan de slag met de volgende query om de tabel te identificeren waartoe de eigenschappen behoren:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Het resultaat van deze query laat zien dat de eigenschap _CounterName_ afkomstig is uit de _perf-tabel._ 

U dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwingsregel:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Voorbeeld 2
U wilt een logboekwaarschuwingsregel maken met de volgende `search`query waarmee prestatiegegevens worden opgehaald met : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Als u deze query wilt wijzigen, gaat u aan de slag met de volgende query om de tabel te identificeren waartoe de eigenschappen behoren:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Het resultaat van deze query geeft aan dat de eigenschap _ObjectName_ en _CounterName_ afkomstig is uit de _tabel Perf._ 

U dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwingsregel:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Voorbeeld 3

U wilt een logboekwaarschuwingsregel maken met de `search` `union` volgende query die beide gebruikt en prestatiegegevens ophalen: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Als u deze query wilt wijzigen, gaat u aan de slag met de volgende query om de tabel te identificeren waartoe de eigenschappen in het eerste deel van de query behoren: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Het resultaat van deze query zou laten zien dat al deze eigenschappen afkomstig waren uit de _perf-tabel._ 

Gebruik `union` nu `withsource` met opdracht om te bepalen welke brontabel elke rij heeft bijgedragen.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Het resultaat van deze query zou laten zien dat deze eigenschappen ook afkomstig waren uit de _perf-tabel._ 

U deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwingsregel: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Voorbeeld 4
U wilt een logboekwaarschuwingsregel maken met de volgende `search` query die de resultaten van twee query's samenvoegt:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Als u de query wilt wijzigen, gaat u aan de slag met de volgende query om de tabel te identificeren die de eigenschappen aan de linkerkant van de join bevat: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Het resultaat geeft aan dat de eigenschappen aan de linkerkant van de join behoren tot de tabel _SecurityEvent._ 

Gebruik nu de volgende query om de tabel te identificeren die de eigenschappen aan de rechterkant van de join bevat: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Het resultaat geeft aan dat de eigenschappen aan de rechterkant van de join behoren tot heartbeattabel. 

U deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwingsregel: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboekwaarschuwingen](alerts-log.md) in Azure Monitor.
- Meer informatie over [logboekquery's](../log-query/log-query-overview.md).

