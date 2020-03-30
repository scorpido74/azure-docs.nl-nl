---
title: Prestaties bewaken en afstemmen
description: Een overzicht van de mogelijkheden voor monitoring en prestatieafstemming en methodologie in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268728"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitoring en prestatieafstemming in Azure SQL Database

Als u de prestaties van een database in Azure SQL Database wilt controleren, moet u eerst de CPU- en IO-resources bewaken die door uw werkbelasting worden gebruikt ten opzichte van het niveau van de databaseprestaties dat u hebt gekozen bij het selecteren van een bepaalde servicelaag en prestatieniveau. Om dit te bereiken, zendt Azure SQL Database resourcemetrics uit die kunnen worden weergegeven in de Azure-portal of met behulp van een van deze SQL-beheertools: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Voor afzonderlijke en gepoolde databases biedt Azure SQL Database een aantal databaseadviseurs om intelligente aanbevelingen voor het afstemmen van prestaties en automatische tuningopties te bieden om de prestaties te verbeteren. Daarnaast toont Query Performance Insight u details over de query's die verantwoordelijk zijn voor het meeste CPU- en IO-gebruik voor afzonderlijke en gepoolde databases.

Azure SQL Database biedt extra monitoring- en tuningmogelijkheden, ondersteund door kunstmatige intelligentie, om u te helpen bij het oplossen van problemen en het maximaliseren van de prestaties van uw databases en oplossingen. U ervoor kiezen om de [streaming-export](sql-database-metrics-diag-logging.md) van deze [Intelligent Insights](sql-database-intelligent-insights.md) en andere SQL Database-bronlogboeken en -statistieken te configureren naar een van de verschillende bestemmingen voor consumptie en analyse, met name met SQL [Analytics).](../azure-monitor/insights/azure-sql.md) Azure SQL Analytics is een geavanceerde cloudmonitoringoplossing voor het bewaken van de prestaties van al uw Azure SQL-databases op schaal en voor meerdere abonnementen in één weergave. Zie [diagnostische telemetrie voor export voor](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database) een lijst met logboeken en statistieken die u exporteren

Tot slot heeft SQL zijn eigen monitoring- en diagnostische mogelijkheden met [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) en dynamic management views [(DMVs).](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) Zie [Controleren met DMVs](sql-database-monitoring-with-dmvs.md) voor scripts die kunnen worden gecontroleerd op verschillende prestatieproblemen.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Bewakings- en tuningmogelijkheden in de Azure-portal

In de Azure-portal biedt Azure SQL Database bewaking van resourcemetrics voor alle implementatietypen. Bovendien bieden databaseadviseurs en Query Performance Insight voor afzonderlijke en samengevoegde databases aanbevelingen voor queryafstemming en queryprestatieanalyse. Ten slotte u in de Azure-portal automatische voor logische servers en hun enkele en gepoolde databases inschakelen.

### <a name="sql-database-resource-monitoring"></a>SQL Database-bronbewaking

U snel de volgende resourcestatistieken in de Azure-portal controleren in de weergave **Statistieken:**

- **DTU-gebruik**

  Controleer of een database of elastische pool gedurende een langere periode 100 procent van het DTU-gebruik bereikt. Een hoog DTU-gebruik geeft aan dat uw werkbelasting mogelijk meer CPU- of IO-bronnen nodig heeft. Het kan ook aangeven query's die moeten worden geoptimaliseerd.
- **CPU-gebruik**

  Controleer of een database, elastische pool of beheerde instantie gedurende een langere periode 100 procent van het CPU-gebruik bereikt. Hoge CPU geeft aan dat uw werkbelasting mogelijk meer CPU- of IO-bronnen nodig heeft. Het kan ook aangeven query's die moeten worden geoptimaliseerd.
- **IO-gebruik**

  Controleer of een database, elastische pool of beheerinstantie de IO-limieten van de onderliggende opslag bereikt. Een hoog IO-gebruik geeft aan dat uw werkbelasting mogelijk meer CPU- of IO-bronnen nodig heeft. Het kan ook aangeven query's die moeten worden geoptimaliseerd.

  ![Resourcestatistieken](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Database adviseurs
' Azure SQL Database bevat [databaseadviseurs](sql-database-advisor.md) die aanbevelingen voor prestatieafstemming bieden voor afzonderlijke en gepoolde databases. Deze aanbevelingen zijn beschikbaar in de Azure-portal en met [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) U ook [automatische afstemming](sql-database-automatic-tuning.md) inschakelen, zodat SQL Database deze tuningaanbevelingen automatisch kan implementeren.

### <a name="query-performance-insight"></a>Inzicht in queryprestaties

[Queryperformance Insight](sql-database-query-performance.md) toont de prestaties in de Azure-portal van de meest tijdrovende en langstlopende query's voor afzonderlijke en samengevoegde databases.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Intelligente beoordelingen van prestatieproblemen genereren

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) maakt gebruik van ingebouwde intelligentie om het gebruik van gegevens continu te monitoren door middel van kunstmatige intelligentie en storende gebeurtenissen te detecteren die slechte prestaties veroorzaken. Intelligent Insights detecteert automatisch prestatieproblemen met databases in Azure SQL Database op basis van wachttijden, fouten of time-outs voor query-uitvoering. Eenmaal gedetecteerd, wordt een gedetailleerde analyse uitgevoerd die een resourcelog genereert (SQLInsights genoemd) met een [intelligente beoordeling van de problemen.](sql-database-intelligent-insights-troubleshoot-performance.md) Deze beoordeling bestaat uit een hoofdoorzaakanalyse van het probleem van de databaseprestaties en, waar mogelijk, aanbevelingen voor prestatieverbeteringen.

Intelligent Insights is een unieke mogelijkheid van azure ingebouwde intelligentie die de volgende waarde biedt:

- Proactieve controle
- Inzichten op maat
- Vroegtijdige detectie van verslechtering van de databaseprestaties
- Hoofdoorzaakanalyse van gedetecteerde problemen
- Aanbevelingen voor prestatieverbetering
- Capaciteit uitschalen op honderdduizenden databases
- Positieve impact op de resources van DevOps en de totale eigendomskosten

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Het streamen van statistieken en bronlogboeken inschakelen

U het [streamen van diagnostische telemetrie](sql-database-metrics-diag-logging.md) naar een van de verschillende bestemmingen inschakelen en configureren, waaronder het intelligent insights-bronlogboek. Gebruik [SQL Analytics](../azure-monitor/insights/azure-sql.md) en andere mogelijkheden om deze aanvullende diagnostische telemetrie te gebruiken om prestatieproblemen te identificeren en op te lossen.

U configureert diagnostische instellingen om categorieën metrische gegevens en bronlogboeken voor afzonderlijke databases, samengevoegde databases, elastische pools, beheerde instanties en instantiedatabases te streamen naar een van de volgende Azure-bronnen.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics-werkruimte in Azure-monitor

U statistieken en bronlogboeken streamen naar een [werkruimte log Analytics in Azure Monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Gegevens die hier worden gestreamd, kunnen worden verbruikt door [SQL Analytics,](../azure-monitor/insights/azure-sql.md)een cloudoplossing voor het monitoren van alleen gegevens die intelligente monitoring van uw databases biedt met prestatierapporten, waarschuwingen en aanbevelingen voor mitigatie. Gegevens die naar een Log Analytics-werkruimte worden gestreamd, kunnen worden geanalyseerd met andere monitoringgegevens die worden verzameld en stelt u ook in staat andere Azure Monitor-functies te gebruiken, zoals waarschuwingen en visualisaties.

### <a name="azure-event-hubs"></a>Azure Event Hubs

U statistieken en bronlogboeken streamen naar [Azure Event Hubs.](../azure-monitor/platform/resource-logs-stream-event-hubs.md) Diagnostische telemetrie streamen naar gebeurtenishubs om de volgende functionaliteit te bieden:

- **Logboeken streamen naar logboekregistratie- en telemetriesystemen van derden**

  Stream al uw statistieken en bronlogboeken naar één gebeurtenishub om logboekgegevens te pipeteren naar een SIEM- of loganalysetool van derden.
- **Een aangepast telemetrie- en logboekplatform bouwen**

  De zeer schaalbare publicatie-abonneren aard van event hubs u flexibel inslikken metrics en resource logs in een aangepaste telemetrie platform. Zie [Het ontwerpen en dimensioneren van een telemetrieplatform op globale schaal op Azure-gebeurtenishubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.
- **De status van de service weergeven door gegevens naar Power BI te streamen**

  Gebruik Gebeurtenishubs, Stream Analytics en Power BI om uw diagnostische gegevens om te zetten in bijna realtime inzichten over uw Azure-services. Zie [Stream Analytics en Power BI: een realtime analysedashboard voor het streamen van gegevens](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) voor meer informatie over deze oplossing.

### <a name="azure-storage"></a>Azure Storage

Stream statistieken en bronlogboeken naar [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Gebruik Azure-opslag om grote hoeveelheden diagnostische telemetrie te archiveren voor een fractie van de kosten van de vorige twee streamingopties.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Uitgebreide gebeurtenissen gebruiken in de SQL-databaseengine

Daarnaast u [uitgebreide gebeurtenissen](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) in SQL gebruiken voor aanvullende geavanceerde monitoring en probleemoplossing. De uitgebreide gebeurtenissenarchitectuur stelt gebruikers in staat om zoveel of zo weinig gegevens te verzamelen als nodig is om een prestatieprobleem op te lossen of te identificeren. Zie [Uitgebreide gebeurtenissen in SQL Database](sql-database-xevent-db-diff-from-svr.md)voor informatie over het gebruik van uitgebreide gebeurtenissen in SQL Database.

## <a name="next-steps"></a>Volgende stappen

- Zie [Prestatieaanbevelingen](sql-database-advisor.md)voor databaseadviseur voor meer informatie over intelligente prestatieaanbevelingen voor afzonderlijke en samengevoegde databases.
- Zie [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie over het automatisch monitoren van databaseprestaties met geautomatiseerde diagnostiek en analyse van de hoofdoorzaak van prestatieproblemen.
'''''''''