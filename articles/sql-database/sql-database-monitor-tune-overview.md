---
title: Prestaties bewaken en afstemmen
description: Een overzicht van de mogelijkheden en methodologie voor het afstemmen van bewaking en prestaties in Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268728"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Bewaking en prestaties afstemmen in Azure SQL Database

Als u de prestaties van een data base in Azure SQL Database wilt bewaken, moet u eerst de CPU-en i/o-resources bewaken die worden gebruikt door uw workload ten opzichte van het prestatie niveau van de data base dat u hebt gekozen bij het selecteren van een bepaalde service tier en Hiertoe Azure SQL Database de metrische gegevens van resources meebrengen die kunnen worden weer gegeven in de Azure Portal of met behulp van een van deze hulpprogram ma's voor SQL-beheer: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Voor afzonderlijke en gegroepeerde Data bases biedt Azure SQL Database een aantal data base-adviseurs om aanbevelingen voor intelligente prestaties te bieden en opties voor automatisch afstemmen om de prestaties te verbeteren. Daarnaast bevat Query Performance Insight informatie over de query's die verantwoordelijk zijn voor het meeste CPU-en IO-gebruik voor één en gepoolde data bases.

Azure SQL Database biedt extra bewakings-en afstemmings mogelijkheden die door kunst matige intelligentie worden ondersteund om u te helpen bij het oplossen van problemen en het optimaliseren van de prestaties van uw data bases en oplossingen. U kunt ervoor kiezen om de [streaming-export](sql-database-metrics-diag-logging.md) van deze [Intelligent Insights](sql-database-intelligent-insights.md) en andere SQL database Resource logboeken en-metrische gegevens te configureren naar een van de verschillende bestemmingen voor verbruik en analyse, met name met behulp van [SQL Analytics](../azure-monitor/insights/azure-sql.md). Azure SQL-analyse is een geavanceerde Cloud bewakings oplossing voor het bewaken van de prestaties van alle Azure SQL-data bases op schaal en op meerdere abonnementen in één weer gave. Voor een lijst met de logboeken en metrische gegevens die u kunt exporteren, raadpleegt u [Diagnostische telemetrie voor exporteren](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Ten slotte heeft SQL zijn eigen bewakings-en diagnostische mogelijkheden met [SQL Server query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) en [dynamische beheer weergaven (dmv's)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Zie [bewaking met behulp van dmv's](sql-database-monitoring-with-dmvs.md) voor scripts om te controleren op diverse prestatie problemen.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Mogelijkheden voor bewaking en afstemming in de Azure Portal

In de Azure Portal, Azure SQL Database het bewaken van metrische gegevens over resources voor alle implementatie typen. Voor afzonderlijke en gepoolde data bases kunnen data base Advisor en Query Performance Insight aanbevelingen voor het afstemmen van query's en analyse van query prestaties bieden. Ten slotte kunt u in het Azure Portal automatische inschakelen voor logische servers en de afzonderlijke data bases.

### <a name="sql-database-resource-monitoring"></a>SQL Database Bron bewaking

U kunt snel de volgende metrische gegevens voor resources bewaken in de Azure Portal in de weer gave **metrische gegevens** :

- **DTU-gebruik**

  Controleer of een Data Base of elastische pool 100 procent van het DTU-gebruik bereikt gedurende een lange periode. Het gebruik van een hoog DTU geeft aan dat uw workload mogelijk meer CPU-of IO-resources nodig heeft. Dit kan ook duiden op query's die moeten worden geoptimaliseerd.
- **CPU-gebruik**

  Controleer of een Data Base, een elastische pool of een beheerd exemplaar gedurende een lange periode 100 procent van het CPU-gebruik bereikt. Hoge CPU geeft aan dat uw workload mogelijk meer CPU-of IO-resources nodig heeft. Dit kan ook duiden op query's die moeten worden geoptimaliseerd.
- **IO-gebruik**

  Controleer of een Data Base, een elastische pool of een beheer exemplaar de i/o-limieten bereikt van de onderliggende opslag. Hoge IO-gebruik geeft aan dat uw workload mogelijk meer CPU-of IO-resources nodig heeft. Dit kan ook duiden op query's die moeten worden geoptimaliseerd.

  ![Metrische gegevens van resources](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Data base Advisor
' Azure SQL Database [database adviezen](sql-database-advisor.md) bevat die aanbevelingen voor het afstemmen van prestaties bieden voor afzonderlijke data bases. Deze aanbevelingen zijn beschikbaar in het Azure Portal en met behulp van [Power shell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). U kunt ook [automatisch afstemmen](sql-database-automatic-tuning.md) inschakelen, zodat SQL database deze afstemmings aanbevelingen automatisch kunt implementeren.

### <a name="query-performance-insight"></a>Inzicht in queryprestaties

[Query Performance Insight](sql-database-query-performance.md) toont de prestaties in het Azure portal van de meest gebruikte en langste query's die worden uitgevoerd voor één en gepoolde data bases.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Intelligente beoordelingen van prestatie problemen genereren

Azure SQL Database [intelligent Insights](sql-database-intelligent-insights.md) maakt gebruik van ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detecteert storende gebeurtenissen die de prestaties nadelig beïnvloeden. Intelligent Insights detecteert automatisch prestatie problemen met data bases in Azure SQL Database op basis van de wacht tijden voor de query uitvoering, fouten of time-outs. Eenmaal gedetecteerd, wordt er een gedetailleerde analyse uitgevoerd waarmee een resource logboek (SQLInsights) wordt gegenereerd met een [intelligente evaluatie van de problemen](sql-database-intelligent-insights-troubleshoot-performance.md). Deze evaluatie bestaat uit een analyse van de hoofd oorzaak van het prestatie probleem van de data base en, waar mogelijk, aanbevelingen voor betere prestaties.

Intelligent Insights is een unieke mogelijkheid van ingebouwde intelligentie van Azure die de volgende waarde biedt:

- Proactieve controle
- Aangepaste prestatie inzichten
- Vroegtijdige detectie van de prestaties van de data base verlagen
- Hoofd oorzaak analyse van gedetecteerde problemen
- Aanbevelingen voor prestatie verbetering
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Streaming-export van metrische gegevens en bron logboeken inschakelen

U kunt de [streaming-export van diagnostische telemetrie](sql-database-metrics-diag-logging.md) inschakelen en configureren op een van de volgende bestemmingen, met inbegrip van het intelligent Insights-bron logboek. Gebruik [SQL Analytics](../azure-monitor/insights/azure-sql.md) en andere mogelijkheden om deze extra diagnostische telemetrie te gebruiken om prestatie problemen te identificeren en op te lossen.

U kunt Diagnostische instellingen configureren voor het streamen van categorieën metrische gegevens en bron logboeken voor afzonderlijke data bases, gepoolde data bases, elastische groepen, beheerde exemplaren en data bases van instanties naar een van de volgende Azure-resources.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics-werk ruimte in azure monitor

U kunt metrische gegevens en bron logboeken streamen naar een [log Analytics werk ruimte in azure monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Gegevens die hier worden gestreamd, kunnen worden gebruikt door [SQL Analytics](../azure-monitor/insights/azure-sql.md). Dit is een alleen-Cloud bewakings oplossing die intelligente bewaking van uw data bases bevat, waaronder prestatie rapporten, waarschuwingen en aanbevelingen voor risico beperking. Gegevens die naar een Log Analytics-werk ruimte worden gestreamd, kunnen worden geanalyseerd met andere bewakings gegevens die worden verzameld en biedt u ook de mogelijkheid om andere Azure Monitor functies, zoals waarschuwingen en visualisaties, te gebruiken.

### <a name="azure-event-hubs"></a>Azure Event Hubs

U kunt metrische gegevens en bron logboeken streamen naar [Azure Event hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Diagnostische telemetrie streamen naar Event hubs om de volgende functionaliteit te bieden:

- **Stroom logboeken naar logboek registratie van derden en telemetrie-systemen**

  U kunt al uw metrische gegevens en bron logboeken streamen naar één Event Hub voor het pipeen van een logboek met een SIEM of een logboek analyse programma van derden.
- **Een aangepast telemetrie-en logboek registratie platform bouwen**

  Met de uiterst schaal bare functie voor publiceren en abonneren van Event hubs kunt u flexibele opname gegevens en resource logboeken maken in een aangepast telemetrie-platform. Zie [het ontwerp en de grootte van een telemetrie-platform op wereld wijd schalen op Azure Event hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.
- **Bekijk de service status door gegevens te streamen naar Power BI**

  Gebruik Event Hubs, Stream Analytics en Power BI om uw diagnostische gegevens te transformeren naar bijna realtime inzichten op uw Azure-Services. Zie [Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) voor meer informatie over deze oplossing.

### <a name="azure-storage"></a>Azure Storage

Metrische gegevens van Stream en bron logboeken naar [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Gebruik Azure Storage om grote hoeveel heden diagnostische telemetrie te archiveren voor een fractie van de kosten van de vorige twee opties voor gegevens stromen.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Uitgebreide gebeurtenissen gebruiken in de SQL database-engine

Daarnaast kunt u [uitgebreide gebeurtenissen](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) in SQL gebruiken voor meer geavanceerde bewaking en probleem oplossing. Met de architectuur Extended Events kunnen gebruikers zoveel of zo weinig gegevens verzamelen als nodig is om problemen op te lossen of te identificeren. Zie [Extended Events in SQL database](sql-database-xevent-db-diff-from-svr.md)voor meer informatie over het gebruik van uitgebreide gebeurtenissen in SQL database.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over intelligente prestatie aanbevelingen voor één en gepoolde data bases aanbevelingen voor de [prestaties van data base Advisor](sql-database-advisor.md).
- Zie [Azure SQL intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie over het automatisch bewaken van database prestaties met automatische diagnose en de oorzaak van prestatie problemen.
'''''''''