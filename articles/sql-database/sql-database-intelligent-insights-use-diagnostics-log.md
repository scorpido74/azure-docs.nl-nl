---
title: Intelligent Insights performance diagnostics log
description: Intelligent Insights biedt een diagnostisch logboek van azure SQL Database-prestatieproblemen
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bb62b087451140261aee7aaa2fab0de14ea36283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209445"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Het logboek van de prestatiediagnostische gegevensgegevens van Intelligent Insights Azure SQL Database gebruiken

Op deze pagina vindt u informatie over het gebruik van het Azure SQL Database performance diagnostics log gegenereerd door [Intelligent Insights,](sql-database-intelligent-insights.md)de indeling en de gegevens die het bevat voor uw aangepaste ontwikkelingsbehoeften. U dit diagnoselogboek verzenden naar [Azure Monitor-logboeken,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)of een oplossing van derden voor aangepaste devOps-waarschuwings- en rapportagemogelijkheden.

## <a name="log-header"></a>Logboekkoptekst

Het diagnoselogboek maakt gebruik van JSON-standaardformaat om intelligent insights-bevindingen uit te brengen. De exacte categorieeigenschap voor toegang tot een Intelligent Insights-logboek is de vaste waarde "SQLInsights".

De koptekst van het logboek is gebruikelijk en bestaat uit de tijdstempel (TimeGenerated) die aangeeft wanneer een item is gemaakt. Het bevat ook een resource-ID (ResourceId) die verwijst naar de specifieke SQL-database waarop de vermelding betrekking heeft. De categorie (categorie), niveau (Niveau) en bewerkingsnaam (OperationName) zijn vaste eigenschappen waarvan de waarden niet worden gewijzigd. Ze geven aan dat de logentry informatief is en afkomstig is van Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Probleem-id en database getroffen

De eigenschap issue identification (issueId_d) biedt een manier om prestatieproblemen op unieke wijze bij te houden totdat deze is opgelost. Meerdere gebeurtenisrecords in de status van logboekrapportage van hetzelfde probleem delen dezelfde probleem-id.

Samen met de probleem-ID rapporteert het diagnoselogboek de begin-(intervalStartTime_t) en einde (intervalEndTme_t) tijdstempels van de specifieke gebeurtenis met betrekking tot een probleem dat wordt gerapporteerd in het diagnoselogboek.

De eigenschap Elastic Pool (elasticPoolName_s) geeft aan tot welke elastische pool de database met een probleem behoort. Als de database geen deel uitmaakt van een elastische groep, heeft deze eigenschap geen waarde. De database waarin een probleem is gedetecteerd, wordt vermeld in de eigenschap databasenaam (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Gedetecteerde problemen

Het volgende gedeelte van het Intelligent Insights-prestatielogboek bevat prestatieproblemen die zijn gedetecteerd door ingebouwde kunstmatige intelligentie. Detecties worden bekendgemaakt in eigenschappen binnen het JSON-diagnoselogboek. Deze detecties bestaan uit de categorie van een probleem, de impact van het probleem, de betrokken query's en de statistieken. De detectie-eigenschappen kunnen meerdere prestatieproblemen bevatten die zijn gedetecteerd.

Gedetecteerde prestatieproblemen worden gerapporteerd met de volgende eigenschappenstructuur voor detecties:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Detecteerbare prestatiepatronen en de details die naar het diagnoselogboek worden uitgevoerd, worden in de volgende tabel weergegeven.

### <a name="detection-category"></a>Detectiecategorie

De eigenschap categorie (categorie) beschrijft de categorie detecteerbare prestatiepatronen. Zie de volgende tabel voor alle mogelijke categorieën detecteerbare prestatiepatronen. Zie [Problemen met de prestaties van de database oplossen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)voor meer informatie.

Afhankelijk van het prestatieprobleem dat is gedetecteerd, verschillen de gegevens die in het diagnoselogboekbestand zijn uitgevoerd, dienovereenkomstig.

| Detecteerbare prestatiepatronen | Details uitgevoerd |
| :------------------- | ------------------- |
| Het bereiken van resourcelimieten | <li>Getroffen middelen</li><li>Queryhashes</li><li>Percentage resourceverbruik</li> |
| Werkbelasting verhogen | <li>Aantal query's waarvan de uitvoering is toegenomen</li><li>Queryhashes van query's met de grootste bijdrage aan de werkbelastingte verhogen</li> |
| Geheugenbelasting | <li>De bediende van het geheugen</li> |
| Vergrendelen | <li>Getroffen queryhashes</li><li>Queryhashes blokkeren</li> |
| Verhoogde MAXDOP | <li>Queryhashes</li><li>CXP wachttijden</li><li>Wachttijden</li> |
| Stelling voor pagelatch | <li>Queryhashes van query's die twist veroorzaken</li> |
| Ontbrekende index | <li>Queryhashes</li> |
| Nieuwe query | <li>Queryhash van de nieuwe query's</li> |
| Ongebruikelijke wachtstatistiek | <li>Ongebruikelijke wachttypen</li><li>Queryhashes</li><li>Wachttijden voor query's</li> |
| TempDB Stelling | <li>Queryhashes van query's die twist veroorzaken</li><li>Query-toewijzing aan de algemene wachttijd voor databasepagelatch-geschil [%]</li> |
| Elastisch zwembad DTU Tekort | <li>Elastische pool</li><li>Beste DTU-verbruikende database</li><li>Percentage van zwembad DTU gebruikt door de top consument</li> |
| Regressie plannen | <li>Queryhashes</li><li>Goed plan-iD's</li><li>Slecht plan-iD's</li> |
| Wijziging van configuratiewaarde met databasebereik | <li>Configuratiewijzigingen met databasebereik in vergelijking met de standaardwaarden</li> |
| Trage client | <li>Queryhashes</li><li>Wachttijden</li> |
| Downgrade van prijzen | <li>Tekstmelding</li> |

### <a name="impact"></a>Impact

De eigenschap impact (impact) beschrijft hoeveel een gedetecteerd gedrag heeft bijgedragen aan het probleem dat een database heeft. Effecten variëren van 1 tot 3, met 3 als hoogste bijdrage, 2 als matig, en 1 als de laagste bijdrage. De impactwaarde kan worden gebruikt als invoer voor aangepaste waarschuwingsautomatisering, afhankelijk van uw specifieke behoeften. De invloed op de eigenschappenquery's (QueryHashes) biedt een lijst met de queryhashes die door een bepaalde detectie zijn beïnvloed.

### <a name="impacted-queries"></a>Beïnvloede query's

Het volgende gedeelte van het logboek Intelligent Insights bevat informatie over bepaalde query's die zijn getroffen door de gedetecteerde prestatieproblemen. Deze informatie wordt openbaar gemaakt als een array van objecten die zijn ingesloten in de eigenschap impact_s. De impacteigenschap bestaat uit entiteiten en statistieken. Entiteiten verwijzen naar een bepaalde query (Type: Query). De unieke queryhash wordt vermeld onder de eigenschap Value (Value). Bovendien wordt elk van de bekendgemaakte query's gevolgd door een statistiek en een waarde, die een gedetecteerd prestatieprobleem aangeven.

In het volgende logboekvoorbeeld werd vastgesteld dat de query met de hash 0x9102EXZ4 een langere uitvoeringsduur heeft (Metric: DurationIncreaseSeconds). De waarde van 110 seconden geeft aan dat deze specifieke query 110 seconden langer duurde om uit te voeren. Omdat meerdere query's kunnen worden gedetecteerd, kan deze specifieke logboeksectie meerdere queryvermeldingen bevatten.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metrische gegevens

De maateenheid voor elke gerapporteerde statistiek wordt weergegeven onder de eigenschap metric (metric) met de mogelijke waarden van seconden, getal en percentage. De waarde van een gemeten statistiek wordt gerapporteerd in de eigenschap waarde (waarde).

De eigenschap DurationIncreaseSeconds biedt de maateenheid in enkele seconden. De meeteenheid CriticalErrorCount is een getal dat een foutaantal vertegenwoordigt.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Aanbevelingen voor analyse van de hoofdoorzaak en verbetering

Het laatste deel van het Intelligent Insights-prestatielogboek heeft betrekking op de geautomatiseerde analyse van de oorzaak van het probleem met de vastgestelde prestatiedegradatie. De informatie verschijnt in mensvriendelijke verbiage in de oorzaak analyse (rootCauseAnalysis_s) eigenschap. Verbeteraanbevelingen zijn waar mogelijk in het logboek opgenomen.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

U het prestatielogboek Intelligent Insights gebruiken met [Azure Monitor-logboeken]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) of een oplossing van derden voor aangepaste devOps-waarschuwings- en rapportagemogelijkheden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Intelligent Insights-concepten.](sql-database-intelligent-insights.md)
- Meer informatie over het [oplossen van azure SQL Database-prestatieproblemen met Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Meer informatie over het [bewaken van Azure SQL Database met Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Meer informatie over het [verzamelen en consumeren van logboekgegevens uit uw Azure-bronnen.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
