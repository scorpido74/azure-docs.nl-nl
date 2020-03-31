---
title: Tijd voor het innemen van logboekgegevens in Azure Monitor | Microsoft Documenten
description: Hiermee worden de verschillende factoren uitgelegd die van invloed zijn op de latentie bij het verzamelen van logboekgegevens in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666634"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Opnametijd van gegevens vastleggen in een logboek in Azure Monitor
Azure Monitor is een grootschalige dataservice die duizenden klanten bedient die elke maand terabytes aan gegevens verzenden. Er zijn vaak vragen over de tijd die het duurt voordat loggegevens beschikbaar komen nadat deze zijn verzameld. In dit artikel worden de verschillende factoren uitgelegd die van invloed zijn op deze latentie.

## <a name="typical-latency"></a>Typische latentie
Latentie verwijst naar de tijd dat gegevens worden gemaakt op het bewaakte systeem en de tijd die beschikbaar is voor analyse in Azure Monitor. De typische latentie voor het innemen van loggegevens is tussen de 2 en 5 minuten. De specifieke latentie voor bepaalde gegevens is afhankelijk van een aantal factoren die hieronder worden uitgelegd.


## <a name="factors-affecting-latency"></a>Factoren die van invloed zijn op latentie
De totale innametijd voor een bepaalde set gegevens kan worden onderverdeeld in de volgende gebieden op hoog niveau. 

- Agenttijd - De tijd om een gebeurtenis te ontdekken, deze te verzamelen en deze vervolgens naar azure monitoropnamepunt te verzenden als een logboekrecord. In de meeste gevallen wordt dit proces behandeld door een agent.
- Pijplijntijd. Dit is de tijd die het kost om de logboekrecord te verwerken via de opnamepijplijn. Dit omvat het ontzeggen van de eigenschappen van de gebeurtenis en het mogelijk toevoegen van berekende informatie.
- Indexeringstijd – De tijd die is besteed aan het opnemen van een logboekrecord in Azure Monitor big data store.

Details over de verschillende latentie die in dit proces worden geïntroduceerd, worden hieronder beschreven.

### <a name="agent-collection-latency"></a>Verzamelingslatentie van agent
Agents en managementoplossingen gebruiken verschillende strategieën om gegevens van een virtuele machine te verzamelen, wat van invloed kan zijn op de latentie. Enkele specifieke voorbeelden zijn:

- Windows-gebeurtenissen, syslog-gebeurtenissen en prestatiestatistieken worden onmiddellijk verzameld. Linux prestatietellers worden met intervallen van 30 seconden gepeild.
- IIS-logboeken en aangepaste logboeken worden verzameld zodra hun tijdstempel is gewijzigd. Voor IIS-logboeken wordt dit beïnvloed door het [rolloverschema dat is geconfigureerd op IIS](data-sources-iis-logs.md). 
- De Active Directory Replication-oplossing voert de beoordeling om de vijf dagen uit, terwijl de Active Directory-beoordelingsoplossing een wekelijkse beoordeling van uw Active Directory-infrastructuur uitvoert. De agent verzamelt deze logboeken alleen wanneer de beoordeling is voltooid.

### <a name="agent-upload-frequency"></a>Uploadfrequentie van agent
Om ervoor te zorgen dat de Log Analytics-agent licht van gewicht is, buffert de agent logboeken en uploadt deze periodiek naar Azure Monitor. De uploadfrequentie varieert tussen 30 seconden en 2 minuten, afhankelijk van het type gegevens. De meeste gegevens worden geüpload in minder dan 1 minuut. Netwerkomstandigheden kunnen een negatieve invloed hebben op de latentie van deze gegevens om het innamepunt van Azure Monitor te bereiken.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure-activiteitslogboeken, bronlogboeken en statistieken
Azure-gegevens voegen extra tijd toe om beschikbaar te komen op het innamepunt van Log Analytics voor verwerking:

- Gegevens uit bronlogboeken duren 2-15 minuten, afhankelijk van de Azure-service. Zie de [onderstaande query](#checking-ingestion-time) om deze latentie in uw omgeving te onderzoeken
- Het duurt 3 minuten voordat azure-platformstatistieken naar het innamepunt van Log Analytics worden verzonden.
- Gegevens van het activiteitenlogboek nemen ongeveer 10-15 minuten in beslag en worden verzonden naar het opnamepunt van Log Analytics.

Zodra de gegevens beschikbaar zijn op het innamepunt, duurt het 2-5 minuten extra voordat de gegevens beschikbaar zijn voor query's.

### <a name="management-solutions-collection"></a>Verzameling van managementoplossingen
Sommige oplossingen verzamelen hun gegevens niet van een agent en kunnen een verzamelingsmethode gebruiken die extra latentie introduceert. Sommige oplossingen verzamelen regelmatig gegevens zonder te proberen bijna realtime te verzamelen. Specifieke voorbeelden zijn:

- In de activiteitslogboeken van Office 365 Management Activity wordt momenteel geen activiteitslogboeken voor near-real-time latentie gegarandeerd.
- Windows Analytics-oplossingen (update compliance bijvoorbeeld) gegevens worden verzameld door de oplossing op een dagelijkse frequentie.

Raadpleeg de documentatie voor elke oplossing om de verzamelfrequentie te bepalen.

### <a name="pipeline-process-time"></a>Pijplijnprocestijd
Zodra logboekrecords zijn opgenomen in de Azure Monitor-pijplijn (zoals aangegeven in de [eigenschap _TimeReceived),](log-standard-properties.md#_timereceived) worden ze naar tijdelijke opslag geschreven om de isolatie van de tenant te garanderen en om ervoor te zorgen dat gegevens niet verloren gaan. Dit proces voegt meestal 5-15 seconden toe. Sommige managementoplossingen implementeren zwaardere algoritmen om gegevens samen te voegen en inzichten te verkrijgen terwijl gegevens binnenkomen. De netwerkprestatiebewaking verzamelt bijvoorbeeld binnenkomende gegevens over intervallen van 3 minuten, waardoor de latentie van 3 minuten effectief wordt toegevoegd. Een ander proces dat latentie toevoegt, is het proces dat aangepaste logboeken verwerkt. In sommige gevallen kan dit proces enkele minuten latentie toevoegen aan logboeken die door de agent uit bestanden worden verzameld.

### <a name="new-custom-data-types-provisioning"></a>Nieuwe aangepaste gegevenstypen inrichten
Wanneer een nieuw type aangepaste gegevens wordt gemaakt op basis van een [aangepast logboek](data-sources-custom-logs.md) of de API [voor gegevensverzamelaar,](data-collector-api.md)maakt het systeem een speciale opslagcontainer. Dit is een eenmalige overhead die alleen optreedt bij de eerste weergave van dit gegevenstype.

### <a name="surge-protection"></a>Overspanningsbeveiliging
De hoogste prioriteit van Azure Monitor is ervoor te zorgen dat er geen klantgegevens verloren gaan, dus het systeem heeft ingebouwde beveiliging voor gegevenspieken. Dit omvat buffers om ervoor te zorgen dat zelfs onder immense belasting, het systeem zal blijven functioneren. Onder normale belasting voegen deze besturingselementen minder dan een minuut toe, maar in extreme omstandigheden en fouten kunnen ze aanzienlijke tijd toevoegen terwijl ze ervoor zorgen dat gegevens veilig zijn.

### <a name="indexing-time"></a>Indexeringstijd
Er is een ingebouwde balans voor elk big data-platform tussen het bieden van analytics en geavanceerde zoekmogelijkheden in plaats van directe toegang tot de gegevens. Azure Monitor stelt u in staat om krachtige query's uit te voeren op miljarden records en resultaten binnen een paar seconden te krijgen. Dit wordt mogelijk gemaakt omdat de infrastructuur de gegevens tijdens de inname drastisch transformeert en opslaat in unieke compacte structuren. Het systeem buffert de gegevens totdat er genoeg van beschikbaar is om deze structuren te maken. Dit moet worden voltooid voordat de logboekrecord in de zoekresultaten wordt weergegeven.

Dit proces duurt momenteel ongeveer 5 minuten wanneer er een laag volume van gegevens, maar minder tijd bij hogere gegevenssnelheden. Dit lijkt contra-intuïtief, maar dit proces maakt optimalisatie van latentie voor productieworkloads met een hoog volume mogelijk.



## <a name="checking-ingestion-time"></a>Innametijd controleren
Opnametijd kan variëren voor verschillende middelen onder verschillende omstandigheden. U logboekquery's gebruiken om specifiek gedrag van uw omgeving te identificeren. In de volgende tabel geeft u aan hoe u de verschillende tijden voor een record bepalen terwijl deze is gemaakt en naar Azure Monitor wordt verzonden.

| Stap | Eigenschap of functie | Opmerkingen |
|:---|:---|:---|
| Record gemaakt bij gegevensbron | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Als de gegevensbron deze waarde niet instelt, wordt deze ingesteld op hetzelfde tijdstip als _TimeReceived. |
| Record ontvangen door Azure Monitor-eindpunt voor inname | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Opnemen in werkruimte en beschikbaar voor query's | [ingestion_time.](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Vertragingen innamelatentie
U de latentie van een specifieke record meten door het resultaat van de [functie ingestion_time()](/azure/kusto/query/ingestiontimefunction) te vergelijken met de eigenschap _TimeGenerated._ Deze gegevens kunnen worden gebruikt met verschillende aggregaties om te vinden hoe inname latentie zich gedraagt. Onderzoek enkele percentiel van de innametijd om inzichten te krijgen voor grote hoeveelheden gegevens. 

In de volgende query ziet u bijvoorbeeld welke computers de hoogste innametijd hadden gedurende de voorgaande 8 uur: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

De voorgaande percentielcontroles zijn goed voor het vinden van algemene trends in latentie. Om een piek op korte termijn in latentie te identificeren, kan het gebruik van de maximale (`max()`) effectiever zijn.

Als u de innametijd voor een bepaalde computer over een bepaalde periode wilt inzoomen, gebruikt u de volgende query, die ook de gegevens van de afgelopen dag in een grafiek visualiseert: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Gebruik de volgende query om de inopnametijd van de computer weer te geven door het land/de regio waarin ze zich bevinden en die is gebaseerd op hun IP-adres: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Verschillende gegevenstypen die afkomstig zijn van de agent kunnen een andere innamelatentietijd hebben, zodat de vorige query's kunnen worden gebruikt met andere typen. Gebruik de volgende query om de opnametijd van verschillende Azure-services te onderzoeken: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Bronnen die niet meer reageren 
In sommige gevallen kan een resource stoppen met het verzenden van gegevens. Als u wilt begrijpen of een resource gegevens verzendt of niet, bekijkt u de meest recente record die kan worden geïdentificeerd door het _standaard-timegenerated-veld._  

Gebruik de _heartbeattabel_ om de beschikbaarheid van een vm te controleren, omdat de agent één keer per minuut een hartslag verzonden. Gebruik de volgende query om de actieve computers weer te geven die de hartslag onlangs nog niet hebben gemeld: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Volgende stappen
* Lees de [SLA (Service Level Agreement)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) voor Azure Monitor.

