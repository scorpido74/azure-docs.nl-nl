---
title: Waarschuwings query's vastleggen in Azure Monitor | Microsoft Docs
description: Biedt aanbevelingen voor het schrijven van efficiënte query's voor logboek waarschuwingen in Azure Monitor updates en een proces voor het converteren van bestaande query's.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667785"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Waarschuwings query's vastleggen in Azure Monitor
[Waarschuwings regels op basis van Azure monitor logboeken](alerts-unified-log.md) worden regel matig uitgevoerd, zodat u zeker weet dat ze zijn geschreven om de overhead en latentie te minimaliseren. Dit artikel bevat aanbevelingen voor het schrijven van efficiënte query's voor logboek waarschuwingen en een proces voor het converteren van bestaande query's. 

## <a name="types-of-log-queries"></a>Typen logboek query's
[Logboek query's in azure monitor](../log-query/log-query-overview.md) beginnen met een tabel of een operator voor [zoeken](/azure/kusto/query/searchoperator) of [samen voegen](/azure/kusto/query/unionoperator) .

De volgende query ligt bijvoorbeeld binnen het bereik van de tabel _SecurityEvent_ en zoekt naar een specifieke gebeurtenis-id. Dit is de enige tabel die de query moet verwerken.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Met query's die beginnen met `search` of `union` kunt u zoeken in meerdere kolommen in een tabel of zelfs op meerdere tabellen. In de volgende voor beelden ziet u meerdere methoden voor het zoeken naar de term _geheugen_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Hoewel `search` en `union` handig zijn tijdens het verkennen van gegevens, zijn ze minder efficiënt dan het gebruik van een tabel, omdat ze moeten scannen over meerdere tabellen. Omdat query's in waarschuwings regels met regel matige tussen pozen worden uitgevoerd, kan dit leiden tot buitensporige overhead bij het toevoegen van latentie aan de waarschuwing. Als gevolg van deze overhead moeten query's voor waarschuwings regels voor logboeken in azure altijd beginnen met een tabel om een duidelijk bereik te definiëren. Dit verbetert de prestaties van query's en de relevantie van de resultaten.

## <a name="unsupported-queries"></a>Niet-ondersteunde query's
Vanaf 11 januari 2019, het maken of wijzigen van logboek waarschuwings regels die gebruikmaken van `search`, of `union` Opera tors worden niet ondersteund in de Azure Portal. Als u deze opera tors in een waarschuwings regel gebruikt, wordt er een fout bericht weer gegeven. Deze wijziging heeft geen invloed op bestaande waarschuwings regels en waarschuwings regels die zijn gemaakt en bewerkt met de Log Analytics-API. U moet nog steeds de waarschuwings regels wijzigen die gebruikmaken van deze typen query's om de efficiëntie te verbeteren.  

Het vastleggen van waarschuwings regels met behulp van [query's voor meerdere resources](../log-query/cross-workspace-query.md) wordt niet beïnvloed door deze wijziging omdat query's voor meerdere bronnen gebruikmaken van `union`, waarmee het query bereik wordt beperkt tot specifieke resources. Dit is niet hetzelfde als `union *` dat niet kan worden gebruikt.  Het volgende voor beeld is geldig in een waarschuwings regel voor logboeken:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Er wordt een [query voor meerdere resources](../log-query/cross-workspace-query.md) in logboek waarschuwingen ondersteund in de nieuwe [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor maakt standaard gebruik van de [verouderde log Analytics waarschuwings-API](api-alerts.md) voor het maken van nieuwe logboek waarschuwings regels van Azure Portal, tenzij u overschakelt van [VERouderde API voor logboek waarschuwingen](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Na de switch wordt de nieuwe API de standaard instelling voor nieuwe waarschuwings regels in Azure Portal en kunt u regels voor het maken van query logboek waarschuwingen voor meerdere resources. U kunt waarschuwings regels voor het query logboek voor [meerdere resources](../log-query/cross-workspace-query.md) maken zonder de switch te maken met behulp van de [arm-sjabloon voor de scheduledQueryRules-API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) , maar deze waarschuwings regel kan wel worden beheerd met [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) en niet vanuit Azure Portal.

## <a name="examples"></a>Voorbeelden
De volgende voor beelden zijn logboek query's die gebruikmaken van `search` en `union` en stappen bieden die u kunt gebruiken om deze query's te wijzigen voor gebruik met waarschuwings regels.

### <a name="example-1"></a>Voorbeeld 1
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die prestatie gegevens ophaalt met behulp van `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen horen:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Het resultaat van deze query geeft aan dat de eigenschap _CounterName_ afkomstig is van de _prestatie_ tabel. 

U kunt dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Voorbeeld 2
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die prestatie gegevens ophaalt met behulp van `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen horen:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Het resultaat van deze query geeft aan dat de eigenschap _ObjectName_ en _CounterName_ afkomstig is uit de _prestatie_ tabel. 

U kunt dit resultaat gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Voorbeeld 3

U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query waarbij zowel `search` als `union` worden gebruikt voor het ophalen van prestatie gegevens: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Als u deze query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren waarvan de eigenschappen in het eerste deel van de query deel uitmaken: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Het resultaat van deze query geeft aan dat al deze eigenschappen afkomstig zijn uit de _prestatie_ tabel. 

Gebruik nu `union` met `withsource` opdracht om te bepalen welke bron tabel elke rij heeft bijgedragen.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Het resultaat van deze query geeft aan dat deze eigenschappen ook afkomstig zijn uit de _prestatie_ tabel. 

U kunt deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel: 

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

### <a name="example-4"></a>Voor beeld 4
U wilt een waarschuwings regel voor het logboek maken met behulp van de volgende query die de resultaten van twee `search` query's samenvoegt:

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
 

Als u de query wilt wijzigen, gebruikt u de volgende query om de tabel te identificeren die de eigenschappen aan de linkerkant van de samen voeging bevat: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Het resultaat geeft aan dat de eigenschappen aan de linkerkant van de samen voeging deel uitmaken van de tabel _SecurityEvent_ . 

Gebruik nu de volgende query om de tabel te identificeren die de eigenschappen in de rechter kant van de koppeling bevat: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Het resultaat geeft aan dat de eigenschappen in de rechter kant van de samen voeging deel uitmaken van de heartbeat-tabel. 

U kunt deze resultaten gebruiken om de volgende query te maken die u zou gebruiken voor de waarschuwings regel: 


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
- Meer informatie over [logboek waarschuwingen](alerts-log.md) in azure monitor.
- Meer informatie over [logboek query's](../log-query/log-query-overview.md).

