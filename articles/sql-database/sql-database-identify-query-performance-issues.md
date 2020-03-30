---
title: Typen queryprestatieproblemen in Azure SQL Database
description: Lees in dit artikel over typen queryprestatieproblemen in Azure SQL Database en lees ook hoe u query's met deze problemen identificeren en oplossen
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
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256131"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Detecteerbare typen queryprestatieknelpunten in Azure SQL Database

Wanneer u een prestatieknelpunt probeert op te lossen, moet u eerst bepalen of het knelpunt optreedt terwijl de query in een uitvoeringstoestand of wachtstatus is. De verschillende resoluties zijn afhangend van deze bepaling van toepassing. Gebruik het volgende diagram om inzicht te krijgen in de factoren die een lopend probleem of een wachtprobleem kunnen veroorzaken. Problemen en oplossingen met betrekking tot elk type probleem worden besproken in dit artikel.

U Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) of SQL Server [DMVs](sql-database-monitoring-with-dmvs.md) gebruiken om dit soort prestatieknelpunten op te sporen.

![Werkbelastingsstatus](./media/sql-database-monitor-tune-overview/workload-states.png)

**Running-gerelateerde problemen**: Problemen met betrekking tot het uitvoeren van problemen zijn over het algemeen gerelateerd aan compilatieproblemen die resulteren in een suboptimaal queryplan of uitvoeringsproblemen in verband met onvoldoende of te veel gebruikte resources.
**Problemen met betrekking tot**wachttijden : Wachtgerelateerde problemen houden over het algemeen verband met:

- Sloten (blokkeren)
- I/o
- Stelling in verband met TempDB gebruik
- Geheugen subsidie wacht

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Compilatieproblemen die resulteren in een suboptimaal queryplan

Een suboptimaal plan dat wordt gegenereerd door de SQL Query Optimizer kan de oorzaak zijn van trage queryprestaties. De SQL Query Optimizer kan een suboptimaal plan produceren vanwege een ontbrekende index, verouderde statistieken, een onjuiste schatting van het aantal rijen dat moet worden verwerkt of een onjuiste schatting van het vereiste geheugen. Als u weet dat de query in het verleden of op een andere instantie (een beheerde instantie of een SQL Server-instantie) sneller is uitgevoerd, vergelijkt u de werkelijke uitvoeringsplannen om te zien of deze verschillen.

- Identificeer ontbrekende indexen met behulp van een van de volgende methoden:

  - Gebruik [intelligente inzichten.](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)
  - [Database-adviseur](sql-database-advisor.md) voor afzonderlijke en gepoolde databases.
  - Dmvs. In dit voorbeeld ziet u de impact van een ontbrekende index, hoe u een [ontbrekende index met](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) DM's detecteren en hoe de impact van het implementeren van de ontbrekende indexaanbeveling.
- Probeer [queryhints](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)toe te passen, [statistieken bij te](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)werken of [indexen opnieuw op](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) te bouwen om een beter plan te krijgen. Schakel [automatische plancorrectie](sql-database-automatic-tuning.md) in Azure SQL Database in om deze problemen automatisch te beperken.

  In [dit voorbeeld](sql-database-performance-guidance.md#query-tuning-and-hinting) ziet u de impact van een suboptimaal queryplan als gevolg van een geparameteriseerde query, hoe u deze voorwaarde detecteert en hoe u een queryhint gebruiken om op te lossen.

- Probeer het databasecompatibiliteitsniveau te wijzigen en intelligente queryverwerking te implementeren. De SQL Query Optimizer kan een ander queryplan genereren, afhankelijk van het compatibiliteitsniveau voor uw database. Hogere compatibiliteitsniveaus bieden [intelligentere mogelijkheden voor queryverwerking.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - Zie [Handleiding voor queryverwerking](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)voor meer informatie over queryverwerking.
  - Zie [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)om databasecompatibiliteitsniveaus te wijzigen en meer te lezen over de verschillen tussen compatibiliteitsniveaus.
  - Zie [Cardinality Estimation](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server) voor meer informatie over kardinaliteitsschatting

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Query's oplossen met suboptimale queryuitvoeringsplannen

In de volgende secties wordt besproken hoe u query's oplossen met een suboptimaal queryuitvoeringsplan.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Query's met psp-problemen (parametergevoelige plannen)

Een psp-probleem (parametergevoelig plan) treedt op wanneer de queryoptimizer een queryuitvoeringsplan genereert dat alleen optimaal is voor een specifieke parameterwaarde (of set waarden) en het plan in de cache vervolgens niet optimaal is voor parameterwaarden die in opeenvolgende waarden worden gebruikt Executies. Plannen die niet optimaal zijn, kunnen vervolgens problemen met queryprestaties veroorzaken en de algehele werkbelastingdoorvoer verslechteren.

Zie de [architectuurgids Queryverwerking](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)voor meer informatie over parametersnuiven en queryverwerking.

Verschillende tijdelijke oplossingen kunnen PSP-problemen beperken. Elke tijdelijke oplossing heeft bijbehorende afwegingen en nadelen:

- Gebruik de queryhint [HERCOMPILERen](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) bij elke queryuitvoering. Deze tijdelijke oplossing trades compilatie tijd en verhoogde CPU voor een betere kwaliteit van het plan. De `RECOMPILE` optie is vaak niet mogelijk voor workloads waarvoor een hoge doorvoer vereist is.
- Gebruik de queryhint [OPTION (OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om de werkelijke parameterwaarde te overschrijven met een typische parameterwaarde die een plan produceert dat goed genoeg is voor de meeste parameterwaardemogelijkheden. Deze optie vereist een goed begrip van optimale parameterwaarden en bijbehorende plankenmerken.
- Gebruik de [queryhint OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om de werkelijke parameterwaarde te overschrijven en in plaats daarvan het gemiddelde van de dichtheidsvector te gebruiken. U dit ook doen door de inkomende parameterwaarden in lokale variabelen vast te leggen en vervolgens de lokale variabelen binnen de predicaten te gebruiken in plaats van de parameters zelf te gebruiken. Voor deze fix moet de gemiddelde dichtheid *goed genoeg*zijn.
- Schakel parametersniffing volledig uit met behulp van de [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) queryhint.
- Gebruik de [hint van DE KEEPFIXEDPLAN-query](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om hercompilaties in de cache te voorkomen. Deze tijdelijke oplossing gaat ervan uit dat het gemeenschappelijke plan goed genoeg al in de cache is. U ook automatische statistieken updates uitschakelen om de kans dat het goede plan zal worden uitgezet en een nieuw slecht plan zal worden samengesteld te verminderen.
- Forceer het plan door de queryhint [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) expliciet te gebruiken door de query te herschrijven en de hint in de querytekst toe te voegen. Of stel een specifiek abonnement in met queryarchief of door [automatische afstemming](sql-database-automatic-tuning.md)in te schakelen.
- Vervang de enkele procedure door een geneste set procedures die elk kunnen worden gebruikt op basis van voorwaardelijke logica en de bijbehorende parameterwaarden.
- Dynamische tekenreeksuitvoeringsalternatieven maken voor een statische proceduredefinitie.

Zie deze blogberichten voor meer informatie over het oplossen van PSP-problemen:

- [Ik ruik een parameter](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamische SQL vs. procedures versus plankwaliteit voor geparameteriseerde query's](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL-queryoptimalisatietechnieken in SQL Server: parametersnuiven](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Activiteit compileren die wordt veroorzaakt door onjuiste parameterisering

Wanneer een query literals heeft, parameteriseert de databaseengine de instructie automatisch of parameteriseert een gebruiker de instructie expliciet om het aantal compilaties te verminderen. Een groot aantal compilaties voor een query met hetzelfde patroon, maar verschillende letterlijke waarden kunnen leiden tot een hoog CPU-gebruik. Als u een query die nog steeds literals heeft, parameteriseert de databaseengine de query niet verder.

Hier is een voorbeeld van een gedeeltelijk geparameteriseerde query:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In dit `t1.c1` voorbeeld `@p1`neemt, maar `t2.c2` blijft GUID te nemen als letterlijk. In dit geval wordt de `c2`query als een andere query behandeld en wordt er een nieuwe compilatie gemaakt als u de waarde voor , als u de waarde voor , wijzigt. Als u compilaties in dit voorbeeld wilt verminderen, u ook de GUID parameteriseren.

In de volgende query wordt het aantal query's per queryhash weergegeven om te bepalen of een query juist is geparameteriseerd:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Factoren die van invloed zijn op wijzigingen in queryplannen

Een hercompilatie van een queryuitvoeringsplan kan resulteren in een gegenereerd queryplan dat verschilt van het oorspronkelijke plan in de cache. Een bestaand origineel abonnement kan om verschillende redenen automatisch opnieuw worden gecompileerd:

- Wijzigingen in het schema worden verwezen door de query
- Gegevenswijzigingen in de tabellen worden verwezen door de query
- Opties voor querycontext zijn gewijzigd

Een gecompileerd plan kan om verschillende redenen uit de cache worden verwijderd, zoals:

- Instantie wordt opnieuw opgestart
- Configuratiewijzigingen met databasebereik
- Geheugendruk
- Expliciete verzoeken om de cache te wissen

Als u een recompilehint gebruikt, wordt een abonnement niet in de cache opgeslagen.

Een recompilatie (of nieuwe compilatie na cacheuitzetting) kan nog steeds resulteren in het genereren van een queryuitvoeringsplan dat identiek is aan het origineel. Wanneer het plan verandert ten opzichte van het vorige of oorspronkelijke plan, zijn deze verklaringen waarschijnlijk:

- **Gewijzigd fysiek ontwerp**: Nieuw gemaakte indexen dekken bijvoorbeeld effectiever de vereisten van een query. De nieuwe indexen kunnen worden gebruikt op een nieuwe compilatie als de queryoptimizer besluit dat het gebruik van die nieuwe index optimaaler is dan het gebruik van de gegevensstructuur die oorspronkelijk is geselecteerd voor de eerste versie van de queryuitvoering. Eventuele fysieke wijzigingen in de objecten waarnaar wordt verwezen, kunnen resulteren in een nieuwe plankeuze op het moment van compileren.

- **Serverresourceverschillen:** Wanneer een abonnement in het ene systeem afwijkt van het plan in een ander systeem, kan de beschikbaarheid van resources, zoals het aantal beschikbare processors, van invloed zijn op welk abonnement wordt gegenereerd. Als bijvoorbeeld één systeem meer processors heeft, kan een parallel plan worden gekozen.

- **Verschillende statistieken**: De statistieken die aan de objecten met de verwijzing zijn gekoppeld, kunnen zijn gewijzigd of wezenlijk verschillen van de statistieken van het oorspronkelijke systeem. Als de statistieken veranderen en er een hercompilatie plaatsvindt, gebruikt de queryoptimizer de statistieken vanaf het moment dat deze zijn gewijzigd. De gegevensdistributies en frequenties van de herziene statistieken kunnen afwijken van die van de oorspronkelijke compilatie. Deze wijzigingen worden gebruikt om kardinaliteitsschattingen te maken. (*Schattingen van de kardinaliteit* zijn het aantal rijen dat naar verwachting door de logische querystructuur stroomt.) Wijzigingen in kardinaliteitsschattingen kunnen ertoe leiden dat u verschillende fysieke operatoren en bijbehorende orders van bewerkingen kiest. Zelfs kleine wijzigingen in statistieken kunnen resulteren in een gewijzigd queryuitvoeringsplan.

- **Gewijzigd databasecompatibiliteitsniveau of versie van de kardinaliteit:** Wijzigingen in het compatibiliteitsniveau van de database kunnen nieuwe strategieën en functies inschakelen die kunnen resulteren in een ander uitvoeringsplan voor query's. Naast het databasecompatibiliteitsniveau kan een uitgeschakelde of ingeschakelde traceflag 4199 of een gewijzigde status van de configuratie met databasebereik QUERY_OPTIMIZER_HOTFIXES ook invloed hebben op de keuzes van het queryuitvoeringsplan tijdens het compileren. Trace flags 9481 (force legacy CE) en 2312 (force default CE) hebben ook invloed op het plan.

## <a name="resource-limits-issues"></a>Problemen met resourcelimieten

Trage queryprestaties die niet gerelateerd zijn aan suboptimale queryplannen en ontbrekende indexen zijn over het algemeen gerelateerd aan onvoldoende of overgebruikte resources. Als het queryplan optimaal is, kan de query (en de database) de resourcelimieten voor de database, elastische groep of beheerde instantie raken. Een voorbeeld hiervan is de overtollige logboekschrijfdoorvoer voor het serviceniveau.

- Resourceproblemen detecteren met behulp van de Azure-portal: zie [SQL Database-bronbewaking](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring)om te zien of resourcelimieten het probleem zijn. Zie [Prestatieaanbevelingen voor databaseadvisor](sql-database-advisor.md) en [Query Performance Insights](sql-database-query-performance.md)voor afzonderlijke databases en elastische pools.
- Resourcelimieten detecteren met behulp van [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Resourceproblemen detecteren met [DMVs:](sql-database-monitoring-with-dmvs.md)

  - De [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV retourneert CPU- i/O- en geheugenverbruik voor een SQL-database. Er bestaat één rij voor elk interval van 15 seconden, zelfs als er geen activiteit in de database is. Historische gegevens worden gedurende een uur bewaard.
  - De [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV retourneert CPU-gebruiks- en opslaggegevens voor Azure SQL Database. De gegevens worden verzameld en samengevoegd in intervallen van vijf minuten.
  - [Veel individuele query's die cumulatief hoge CPU verbruiken](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Als u het probleem als onvoldoende resource identificeert, u resources upgraden om de capaciteit van uw SQL-database te vergroten om de CPU-vereisten op te nemen. Zie [Afzonderlijke databaseresources schalen in Azure SQL Database](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen in Azure SQL Database](sql-database-elastic-pool-scale.md)voor meer informatie. Zie [Resourcelimieten voor servicelagen](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)voor informatie over het schalen van een beheerde instantie .

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestatieproblemen veroorzaakt door een verhoogd werkbelastingsvolume

Een toename van het toepassingsverkeer en het werkbelastingsvolume kan leiden tot een verhoogd CPU-gebruik. Maar je moet voorzichtig zijn om goed te diagnosticeren dit probleem. Wanneer u een probleem met een hoge CPU ziet, beantwoordt u deze vragen om te bepalen of de toename wordt veroorzaakt door wijzigingen in het werkbelastingsvolume:

- Zijn de query's van de toepassing de oorzaak van het probleem met een hoge CPU?
- Voor de [beste CPU-verbruikende query's die u identificeren:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Zijn er meerdere uitvoeringsplannen aan dezelfde query gekoppeld? Zo ja, waarom?
  - Waren de uitvoeringstijden consistent voor query's met hetzelfde uitvoeringsplan? Is de uitvoering gestegen? Als dat het zo is, veroorzaakt de toename van de werkbelasting waarschijnlijk prestatieproblemen.

Kortom, als het queryuitvoeringsplan niet anders is uitgevoerd, maar het CPU-gebruik samen met het aantal uitvoeringen is toegenomen, is het prestatieprobleem waarschijnlijk gerelateerd aan een toename van de werkbelasting.

Het is niet altijd gemakkelijk om een wijziging van het werkbelastingvolume te identificeren die een CPU-probleem aandrijft. Houd rekening met deze factoren:

- **Gewijzigd resourcegebruik**: Houd bijvoorbeeld rekening met een scenario waarin het CPU-gebruik gedurende een langere periode is gestegen tot 80 procent. CPU-gebruik alleen betekent niet dat het werkbelastingvolume is gewijzigd. Regressies in het queryuitvoeringsplan en wijzigingen in de gegevensdistributie kunnen ook bijdragen aan meer resourcegebruik, ook al voert de toepassing dezelfde werkbelasting uit.

- **De weergave van een nieuwe query:** een toepassing kan een nieuwe set query's op verschillende tijdstippen aansturen.

- **Een toename of afname van het aantal aanvragen**: Dit scenario is de meest voor de hand liggende maatstaf voor een werkbelasting. Het aantal query's komt niet altijd overeen met meer resourcegebruik. Deze statistiek is echter nog steeds een belangrijk signaal, ervan uitgaande dat andere factoren ongewijzigd blijven.

Gebruik Intelligente inzichten om [workloadverhogingen](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) te detecteren en [regressies te plannen.](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>Wachtgerelateerde problemen

Zodra u een suboptimaal plan en *wachtgerelateerde* problemen hebt geëlimineerd die verband houden met uitvoeringsproblemen, is het prestatieprobleem over het algemeen de query's die waarschijnlijk wachten op een resource. Wachttijden kunnen worden veroorzaakt door:

- **Blokkering**:

  Eén query kan het slot op objecten in de database bevatten, terwijl andere query's proberen toegang te krijgen tot dezelfde objecten. U blokkeringsquery's identificeren met Behulp van [DMVs](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) of [Intelligent Insights.](sql-database-intelligent-insights-troubleshoot-performance.md#locking)
- **IO-problemen**

  Query's wachten mogelijk tot de pagina's naar de gegevens of logboekbestanden worden geschreven. Controleer in dit `INSTANCE_LOG_RATE_GOVERNOR`geval `WRITE_LOG`de `PAGEIOLATCH_*` statistieken , of wacht op statistieken in de DMV. Zie DMVs gebruiken om [problemen met io-prestaties](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)te identificeren.
- **TempDB problemen**

  Als de werkbelasting tijdelijke tabellen gebruikt of als er TempDB-lekken in de plannen zijn, kunnen de query's een probleem hebben met de TempDB-doorvoer. Zie het gebruik van DMVs om [TempDB-problemen te identificeren.](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)
- **Geheugengerelateerde problemen**

  Als de werkbelasting niet genoeg geheugen heeft, kan de levensverwachting van de pagina dalen of krijgen de query's mogelijk minder geheugen dan ze nodig hebben. In sommige gevallen lost de ingebouwde intelligentie in Query Optimizer geheugengerelateerde problemen op. Zie DMVs gebruiken om [problemen met geheugenverlening](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)te identificeren.

### <a name="methods-to-show-top-wait-categories"></a>Methoden om topwachtcategorieën weer te geven

Deze methoden worden vaak gebruikt om de bovenste categorieën wachttypen weer te geven:

- Gebruik Intelligente inzichten om query's te identificeren met prestatiedegradatie als gevolg [van verhoogde wachttijden](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Gebruik [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) om wachtstatistieken voor elke query in de loop van de tijd te vinden. In querywinkel worden wachttypen gecombineerd in wachtcategorieën. U de toewijzing van wachtcategorieën vinden om te wachten in [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Gebruik [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) om informatie te retourneren over alle wachttijden die worden ondervonden door threads die tijdens een querybewerking zijn uitgevoerd. U deze geaggregeerde weergave gebruiken om prestatieproblemen met Azure SQL Database en ook met specifieke query's en batches te diagnosticeren. Query's kunnen wachten op resources, wachtrijwachttijden of externe wachttijden.
- Gebruik [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) om informatie terug te geven over de wachtrij met taken die op een resource wachten.

In scenario's met een hoge CPU geven query-opslag- en wachtstatistieken mogelijk geen rekening met het CPU-gebruik als:

- Query's met een hoge CPU-verbruikende worden nog steeds uitgevoerd.
- De query's die veel CPU-verbruiken, werden uitgevoerd toen er een failover plaatsvond.

DMVs die queryarchief bijhouden en wachtstatistieken tonen resultaten voor alleen voltooide en time-outquery's. Ze tonen geen gegevens voor het uitvoeren van verklaringen totdat de verklaringen zijn voltooid. Gebruik de dynamische beheerweergave [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) om momenteel uitvoerende query's en de bijbehorende werktijd bij te houden.

> [!TIP]
> Aanvullende hulpmiddelen:
>
> - [TigerToolbox wacht en vergrendelingen](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Volgende stappen

[SQL Database monitoring en tuning overzicht](sql-database-monitor-tune-overview.md)