---
title: Bewaken & prestaties afstemmen-Azure SQL Database | Microsoft Docs
description: Tips voor het afstemmen van de prestaties van Azure SQL Database door te evalueren en te verbeteren.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: ee4bd9d61856ef4ea1afdd027d6f39e730b92d78
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129205"
---
# <a name="monitoring-and-performance-tuning"></a>Prestaties bewaken en afstemmen

Azure SQL Database voorziet in hulpprogram ma's en methoden om het gebruik eenvoudig te controleren en resources (CPU, geheugen, I/O) te beheren, mogelijke problemen op te lossen en aanbevelingen te doen voor het verbeteren van de prestaties van een Data Base. Azure SQL Database bevat functies waarmee automatisch problemen in de data bases kunnen worden opgelost. Automatisch afstemmen maakt het mogelijk een data base aan te passen aan de werk belasting en automatisch de prestaties te optimaliseren. Er zijn echter enkele aangepaste problemen die mogelijk problemen moeten oplossen. In dit artikel worden enkele aanbevolen procedures en hulpprogram ma's beschreven die kunnen worden gebruikt om de prestatie problemen op te lossen.

Er zijn twee belang rijke activiteiten die moeten worden uitgevoerd om ervoor te zorgen dat een Data Base zonder problemen wordt uitgevoerd:
- De prestaties van de [Data Base controleren](#monitoring-database-performance) om ervoor te zorgen dat de resources die aan de Data Base zijn toegewezen de werk belasting kunnen afhandelen. Als u ziet dat voor een Data Base de resource limieten worden aangeraden, overweeg dan:
   - het identificeren en optimaliseren van de meest voorkomende query's in de resource.
   - meer resources toevoegen door de servicelaag bij te werken.
- [Problemen met prestaties oplossen](#troubleshoot-performance-issues) om te bepalen waarom een mogelijk probleem is opgetreden, de hoofd oorzaak van het probleem identificeren. Nadat de hoofd oorzaak is vastgesteld, voert u de stappen uit om het probleem op te lossen.

## <a name="monitoring-database-performance"></a>Prestaties van de data base bewaken

Het bewaken van de prestaties van een SQL database in azure begint met het controleren van de resources die worden gebruikt ten opzichte van het gekozen prestatie niveau van de data base. De volgende resources moeten worden bewaakt:
 - **CPU-gebruik** : Controleer of de data base gedurende lange tijd 100% van het CPU-gebruik bereikt. Een hoog CPU-gebruik kan erop wijzen dat query's die gebruikmaken van de meeste reken kracht, moeten worden geïdentificeerd en afgestemd. Of een hoog CPU-gebruik kan erop wijzen dat de data base of het exemplaar moet worden bijgewerkt naar een hogere servicelaag. 
 - **Wacht statistieken** : gebruik [sys. DM _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) om de wacht tijden te bepalen die query's ondervinden. Query's kunnen wachten op resources, wachten op een wachtrij of externe wacht tijden. 
 - **I/o-gebruik** : Controleer of de data base de i/o-limieten van de onderliggende opslag bereikt.
 - **Geheugen gebruik** : de hoeveelheid geheugen die beschikbaar is voor de data base of het exemplaar, is evenredig met het aantal vCores. Controleer of het geheugen voldoende is voor de workload. Pagina duur verwachting is een van de para meters die kunnen aangeven hoe snel de pagina's uit het geheugen worden verwijderd.

Azure SQL Database-service **bevat de hulpprogram ma's en bronnen om mogelijke prestatie problemen op te lossen en te**verhelpen. Verkoop kansen kunnen worden geïdentificeerd om de query prestaties te verbeteren en te optimaliseren zonder bronnen te wijzigen door de aanbevelingen voor het afstemmen van [prestaties](sql-database-advisor.md)te controleren. Ontbrekende indexen en onvoldoende geoptimaliseerde query's zijn veelvoorkomende redenen voor slechte databaseprestaties. Deze afstemmings aanbevelingen kunnen worden toegepast om de prestaties van de werk belasting te verbeteren. We kunnen ook instellen dat Azure SQL database [de prestaties van de query's automatisch optimaliseert](sql-database-automatic-tuning.md) door alle geïdentificeerde aanbevelingen toe te passen en de aanbevelingen verbeterde database prestaties te controleren.

De volgende opties zijn beschikbaar voor het bewaken en oplossen van problemen met de Data Base:

- Klik in de [Azure Portal](https://portal.azure.com)op **SQL-data bases**, selecteer de data base en gebruik vervolgens het bewakings diagram om te zoeken naar resources waarmee het maximale gebruik wordt bereikt. Het DTU-verbruik wordt standaard weer gegeven. Klik op **bewerken** om het tijds bereik en de weer gegeven waarden te wijzigen.
- Hulpprogram ma's zoals SQL Server Management Studio bieden een groot aantal nuttige rapporten zoals een [dash board voor prestaties](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) om het resource gebruik te bewaken en de belangrijkste query's in de resource te identificeren. [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) kan worden gebruikt voor het identificeren van query's met teruggedraaide-prestaties.
- Gebruik [query Performance Insight](sql-database-query-performance.md) in de [Azure Portal](https://portal.azure.com) om de query's te identificeren die gebruikmaken van de meeste resources. Deze functie is alleen beschikbaar in Individuele database en elastische Pools.
- Gebruik [SQL database Advisor](sql-database-advisor-portal.md) om aanbevelingen weer te geven voor het maken en verwijderen van indexen, parameterizing query's en het oplossen van schema problemen. Deze functie is alleen beschikbaar in Individuele database en elastische Pools.
- Gebruik [Azure SQL intelligent Insights](sql-database-intelligent-insights.md) voor het automatisch bewaken van de prestaties van de data base. Als er een prestatie probleem is gedetecteerd, wordt er een diagnostisch logboek gegenereerd met details en basis oorzaak analyse (RCA) van het probleem. Als dat mogelijk is, wordt aanbevolen om de prestaties te verbeteren.
- [Schakel automatisch afstemmen in](sql-database-automatic-tuning-enable.md) en laat Azure SQL database automatisch vastgestelde prestatie problemen oplossen.
- Gebruik [dynamische beheer weergaven (dmv's)](sql-database-monitoring-with-dmvs.md), [uitgebreide gebeurtenissen](sql-database-xevent-db-diff-from-svr.md)en de [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) voor meer gedetailleerde probleem oplossing van prestatie problemen.

> [!TIP]
> Raadpleeg de [richt lijnen voor prestaties](sql-database-performance-guidance.md) om technieken te vinden die u kunt gebruiken voor het verbeteren van de prestaties van Azure SQL database nadat u het prestatie probleem hebt geïdentificeerd met behulp van een of meer van de bovenstaande methoden.

## <a name="troubleshoot-performance-issues"></a>Prestatieproblemen oplossen

Om prestatie problemen vast te stellen en op te lossen, begint u met de status van elke actieve query en de voor waarden die prestatie problemen veroorzaken die relevant zijn voor de status van elke werk belasting. Als u de prestaties van Azure SQL Database wilt verbeteren, moet u weten dat elke actieve query aanvraag vanuit de toepassing een actief of wacht status heeft. Bij het oplossen van problemen met een prestatie probleem in Azure SQL Database houdt u rekening met het volgende in dit artikel voor informatie over het vaststellen en oplossen van prestatie problemen.

![Status van werk belasting](./media/sql-database-monitor-tune-overview/workload-states.png)

Voor een werk belasting met prestatie problemen kan het prestatie probleem worden veroorzaakt door een CPU-conflict (een **uitvoerings** voorwaarde) of door afzonderlijke query's te wachten op iets (een voor waarde voor een **wacht** actie).

De oorzaken voor het **uitvoeren** van problemen zijn mogelijk:
- **Compilatie problemen** : SQL query Optimizer kan een suboptimaal plan produceren als gevolg van verouderde statistieken, een onjuiste schatting van het aantal rijen dat wordt verwerkt of een onnauwkeurige schatting van het vereiste geheugen. Als we weten dat de query sneller is uitgevoerd in het verleden of op een ander exemplaar (ofwel een beheerd exemplaar of SQL Server exemplaar), neemt u de daad werkelijke uitvoerings plannen en vergelijkt u deze om te zien of ze verschillend zijn. Probeer query hints toe te passen of statistieken opnieuw op te bouwen of indexen opnieuw op te bouwen om het betere plan te krijgen. Schakel automatische correctie van plannen in Azure SQL Database in om deze problemen automatisch te verhelpen.
- **Uitvoerings problemen** : als het query plan optimaal is, zijn er waarschijnlijk resource limieten in de data base, zoals schrijf doorvoer in het logboek of het gebruik van gefragmenteerde indexen die opnieuw moeten worden opgebouwd. Een groot aantal gelijktijdige query's die dezelfde resources nodig hebben, kan ook de oorzaak zijn van uitvoerings problemen. Er zijn in de meeste gevallen te maken met wachtende problemen die betrekking hebben op het uitvoeren van problemen, omdat de query's die niet efficiënt worden uitgevoerd, waarschijnlijk wachten op een aantal resources.

De oorzaken van de volgende problemen kunnen optreden:
- **Blok keren** : een query kan de vergren deling van objecten in de Data Base bevatten terwijl andere gebruikers toegang proberen te krijgen tot dezelfde objecten. Het blok keren van query's kan eenvoudig worden geïdentificeerd met behulp van Dmv's of controle hulpprogramma's.
- **Io-problemen** : query's kunnen wachten tot de pagina's naar de gegevens of logboek bestanden zijn geschreven. In dit geval ziet `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`of `PAGEIOLATCH_*` wacht u de statistieken in de DMV.
- **Problemen met Tempdb** : als de werk belasting tijdelijke tabellen gebruikt of als er sprake is van tempdb-overloop in de plannen, hebben de query's mogelijk een probleem met de door Voer van tempdb. 
- **Problemen met betrekking** tot het geheugen-er is mogelijk onvoldoende geheugen beschikbaar voor de werk belasting, zodat de verwachting van de pagina mogelijk verloren gaat of de query's minder geheugen krijgen dan nodig is. In sommige gevallen worden deze problemen opgelost met ingebouwde intelligentie in query Optimizer.
 
 In de volgende secties wordt uitgelegd hoe u een aantal van deze problemen kunt identificeren en oplossen.

## <a name="running-related-performance-issues"></a>Prestatie problemen met betrekking tot prestaties

Als algemene richt lijn, als het CPU-gebruik consistent is op of meer dan 80%, is er een probleem met betrekking tot prestaties. Als er een probleem optreedt, kan dit worden veroorzaakt door onvoldoende CPU-bronnen of kan het zijn gerelateerd aan een van de volgende voor waarden:

- Te veel query's uitvoeren
- Te veel query's voor het compileren
- Een of meer query's die worden uitgevoerd, maken gebruik van een suboptimaal query plan

Als wordt vastgesteld dat er sprake is van een probleem met betrekking tot prestaties, is het doel om het precieze probleem te identificeren met behulp van een of meer methoden. De meest voorkomende methoden voor het identificeren van uitvoerings problemen zijn:

- Gebruik de [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) om het CPU-percentage gebruik te bewaken.
- Gebruik de volgende [dynamische beheer weergaven](sql-database-monitoring-with-dmvs.md):

  - [sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourneert CPU, I/O en geheugen verbruik voor een Azure SQL database. Er bestaat één rij voor elk interval van 15 seconden, zelfs als er geen activiteit in de data base is. Historische gegevens worden één uur bewaard.
  - [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) retourneert het CPU-gebruik en de opslag gegevens voor een Azure SQL database. De gegevens worden binnen een interval van vijf minuten verzameld en geaggregeerd.

> [!IMPORTANT]
> Zie [CPU-prestatie problemen identificeren](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)voor T-SQL-query's met sys. DM _db_resource_stats en sys. resource_stats dmv's voor het oplossen van problemen met CPU-gebruik.

### <a name="ParamSniffing"></a>Problemen met query's oplossen met parameter gevoelige query's uitvoerings plan problemen

Het PSP-probleem (parameter gevoelig plan) verwijst naar een scenario waarbij de query Optimizer een query-uitvoerings plan genereert dat optimaal is voor een specifieke parameter waarde (of een set waarden) en het schema in de cache niet optimaal is voor parameter waarden die worden gebruikt in opeenvolgende uitvoeringen. Niet-optimale plannen kunnen vervolgens leiden tot problemen met de query prestaties en de algehele door Voer van de werk belasting. Zie de [hand leiding](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)voor het verwerken van query's voor meer informatie over parameter-sniffing en query verwerking.

Er zijn verschillende oplossingen die worden gebruikt om deze problemen te verhelpen, elk met bijbehorende compromissen en nadelen:

- De query [](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) Hint RECOMPILE gebruiken bij elke uitvoering van de query. Deze tijdelijke oplossing verhandelt compilatie tijd en verhoogde CPU voor een betere plan kwaliteit. Het gebruik `RECOMPILE` van de optie is vaak niet mogelijk voor werk belastingen die een hoge door Voer vereisen.
- Gebruik de query Hint [optie (Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om de werkelijke parameter waarde te overschrijven met een typische parameter waarde die een goed schema voor de meeste parameter waarden produceert.   Deze optie vereist een goed idee van de optimale parameter waarden en de bijbehorende plan kenmerken.
- Gebruik de query Hint [(Optimize for UNknown)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om de werkelijke parameter waarde in Exchange te overschrijven voor het gebruik van het gemiddelde dichtheids vector. Een andere manier om dit te doen, is door de inkomende parameter waarden vast te leggen in lokale variabelen en vervolgens de lokale variabelen binnen de predikaten te gebruiken in plaats van de para meters zelf te gebruiken. De gemiddelde densiteit moet *goed genoeg* zijn met deze specifieke oplossing.
- Schakel para meter-sniffing volledig uit met behulp van de [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -query hint.
- Gebruik de [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -query hint om opnieuw te compileren in de cache. Bij deze tijdelijke oplossing wordt ervan uitgegaan dat het *goede* algemene plan al in de cache staat. U kunt ook automatische updates naar statistieken uitschakelen om de kans te verkleinen dat het goede plan wordt verwijderd en een nieuw, beschadigd plan wordt gecompileerd.
- Dwing het plan af door expliciet gebruik te maken van de query Hint [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (door expliciet op te geven, door een specifiek plan in te stellen met behulp van query Store of door [automatisch afstemmen](sql-database-automatic-tuning.md)in te scha kelen.
- Vervang de ene procedure door een geneste set procedures die elk kan worden gebruikt op basis van voorwaardelijke logica en de bijbehorende parameter waarden.
- Alternatieven voor het uitvoeren van dynamische teken reeksen maken voor een statische procedure definitie.

Voor meer informatie over het oplossen van dit soort problemen raadpleegt u de blog berichten:

- [Ik geur een para meter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [dynamische SQL versus plan kwaliteit voor query's met para meters](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Optimalisatie technieken voor SQL-Query's in SQL Server: Parameter sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Problemen met compilatie activiteiten oplossen vanwege onjuiste parameterisering

Wanneer een query letterlijke waarden bevat, kiest de data base-engine automatisch de instructie para meters of een gebruiker kan deze expliciet para meters om het aantal compilaties te verminderen. Een groot aantal compilaties voor een query met hetzelfde patroon, maar met verschillende letterlijke waarden kan leiden tot een hoog CPU-gebruik. Als u een query slechts gedeeltelijk para meters met letterlijke waarden, wordt deze ook niet door de data base-engine para meters.  Hieronder ziet u een voor beeld van een gedeeltelijk Geparametriseerde query:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In het voor gaande voor `t1.c1` beeld `@p1` `t2.c2` neemt de GUID als letterlijke waarde. Als u in dit geval de waarde wijzigt voor `c2`, wordt de query behandeld als een andere query en wordt er een nieuwe compilatie uitgevoerd. Om compilaties in het vorige voor beeld te verminderen, is de oplossing ook para meters de GUID.

Met de volgende query wordt het aantal query's per query-hash weer gegeven om te bepalen of een query op de juiste wijze is para meters:

```sql
SELECT  TOP 10  
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
### <a name="factors-influencing-query-plan-changes"></a>Factoren die van invloed zijn op de wijzigingen in het query plan

Een hercompilatie van een query plan kan leiden tot een gegenereerd query plan dat verschilt van wat oorspronkelijk in de cache is opgeslagen. Er zijn verschillende redenen waarom een bestaand oorspronkelijk schema mogelijk automatisch opnieuw wordt gecompileerd:
- Wijzigingen in het schema waarnaar wordt verwezen door de query
- Gegevens wijzigingen in de tabellen waarnaar wordt verwezen door de query 
- Wijzigingen in de query context opties 

Een gecompileerd plan kan om verschillende redenen uit de cache worden verwijderd, inclusief het opnieuw opstarten van het exemplaar, de configuratie wijzigingen van het database bereik, de geheugen belasting en expliciete aanvragen om de cache te wissen. Daarnaast betekent het gebruik van een RECOMPILE hint dat een plan niet in de cache wordt opgeslagen.

Een hercompilatie (of nieuwe compilatie na verwijdering van de cache) kan nog steeds leiden tot het genereren van een identiek uitvoerings plan voor query's dat oorspronkelijk werd geobserveerd.  Als er wijzigingen zijn in het abonnement vergeleken met het vorige of oorspronkelijke abonnement, zijn het volgende de meest voorkomende uitleg waarom een query-uitvoerings plan is gewijzigd:

- Het **fysieke ontwerp is gewijzigd**. Er zijn bijvoorbeeld nieuwe indexen gemaakt waarmee de vereisten van een query effectiever worden besproken. De nieuwe indexen kunnen worden gebruikt voor een nieuwe compilatie als de optimalisatie van de query is voltooid, is het beter om die nieuwe index te gebruiken dan voor de eerste versie van de uitvoering van de query.  Eventuele fysieke wijzigingen in de objecten waarnaar wordt verwezen, kunnen leiden tot een nieuwe plannings optie tijdens het compileren.

- **Verschillen tussen Server bronnen**. In een scenario waarbij één abonnement verschilt van ' systeem A ' versus ' systeem B ': de beschik baarheid van resources, zoals het aantal beschik bare processors, kan beïnvloeden welk plan wordt gegenereerd.  Als bijvoorbeeld één systeem een groter aantal processors heeft, kan een parallel abonnement worden gekozen. 

- **Verschillende statistieken**. De statistieken die zijn gekoppeld aan de objecten waarnaar wordt verwezen, zijn gewijzigd of zijn afhankelijk van de statistieken van het oorspronkelijke systeem.  Als de statistieken veranderen en een hercompilatie plaatsvindt, worden in de query Optimizer statistieken gebruikt voor het specifieke punt in de tijd. De herziene statistieken kunnen verschillende gegevens distributies en-frequenties hebben dan de oorspronkelijke compilatie.  Deze wijzigingen worden gebruikt voor het schatten van kardinaliteit (aantal rijen dat wordt verwacht om door de logische query structuur te stromen).  Wijzigingen in de kardinaliteit kunnen leiden tot het kiezen van verschillende fysieke Opera tors en gekoppelde order bewerkingen.  Zelfs kleine wijzigingen in statistieken kunnen leiden tot een gewijzigd query-uitvoerings plan.

- Het **database compatibiliteits niveau of de kardinaliteit Estimator-versie is gewijzigd**.  Wijzigingen in het database compatibiliteits niveau kunnen nieuwe strategieën en functies inschakelen die kunnen leiden tot een ander uitvoerings plan voor query's.  Buiten het database compatibiliteits niveau, het in-of inschakelen van de tracerings vlag 4199 of het wijzigen van de status van de data base-scoped configuratie QUERY_OPTIMIZER_HOTFIXES kunnen ook de keuze van de query-uitvoerings plannen beïnvloeden tijdens het compileren.  Tracerings vlaggen 9481 (geforceerd verouderd CE) en 2312 (standaard-CE afdwingen) zijn ook van invloed op de planning. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Probleem query's oplossen of meer resources opgeven

Zodra u het probleem hebt geïdentificeerd, kunt u de probleem query's afstemmen of de berekenings grootte of de servicelaag upgraden om de capaciteit van uw Azure-SQL database te verg Roten om de CPU-vereisten te absorberen. Zie voor meer informatie over het schalen van resources voor afzonderlijke data bases [schaal bare bronnen voor één data base schalen in Azure SQL database](sql-database-single-database-scale.md) en voor het schalen van resources voor elastische Pools raadpleegt u [bronnen voor elastische pools schalen in Azure SQL database](sql-database-elastic-pool-scale.md). Zie [resource limieten op exemplaar niveau](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)voor meer informatie over het schalen van een beheerd exemplaar.

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Vaststellen of er problemen optreden als gevolg van een verg root hoeveelheid werk belasting volume

Een toename in het toepassings verkeer en de werk belasting kan het CPU-gebruik verhogen, maar u moet er wel voor zorgen dat dit probleem correct kan worden vastgesteld. In een scenario met een hoge CPU beantwoordt u deze vragen om te bepalen of een CPU-toename te wijten is aan het aantal wijzigingen in het werkbelasting volume:

1. Zijn de query's van de toepassing de oorzaak van het probleem met hoge CPU?
2. Voor de top van CPU-verbruikte query's (die kunnen worden geïdentificeerd):

   - Bepaal of er meerdere uitvoerings plannen zijn gekoppeld aan dezelfde query. Als dit het geval is, bepaalt u waarom.
   - Bepaal voor query's met hetzelfde uitvoerings plan of de uitvoerings tijden consistent zijn en of het aantal uitvoeringen is verhoogd. Zo ja, dan zijn er waarschijnlijk prestatie problemen als gevolg van een verhoging van de werk belasting.

Als u wilt samenvatten, als het query-uitvoerings plan niet anders is uitgevoerd, maar het CPU-gebruik is verhoogd met het aantal uitvoeringen, is er waarschijnlijk een probleem met het verhogen van de werk belasting.

Het is niet altijd gemakkelijk om te concluderen dat er een wijziging in het workload-volume optreedt dat een CPU-probleem ondervindt.   Factoren om rekening mee te houden: 

- **Resource gebruik gewijzigd**

  Denk bijvoorbeeld aan een scenario waarbij CPU gedurende lange tijd is verhoogd tot 80%.  Het gemiddelde van het CPU-gebruik is niet gewijzigd.  Wijzigingen in de regressies van het query plan en de gegevens distributie kunnen ook bijdragen aan meer resource gebruik, zelfs als de toepassing dezelfde werk belasting uitvoert.

- **Nieuwe query verscheen**

   Een toepassing kan op verschillende tijdstippen een nieuwe set query's aansturen.

- **Aantal verg root of afgenomen aanvragen**

   Dit scenario is de meest duidelijke maat staf voor de werk belasting. Het aantal query's komt niet altijd overeen met meer resource gebruik. Deze metriek is echter nog steeds een significant signaal, waarbij andere factoren ongewijzigd blijven.

## <a name="waiting-related-performance-issues"></a>Problemen met betrekking tot de prestaties

Wanneer u zeker weet dat u geen hoge prestaties hebt, hebt u een probleem met betrekking tot het prestatie probleem. Dat wil zeggen dat uw CPU-resources niet efficiënt worden gebruikt omdat de CPU op een andere resource wacht. In dit geval is de volgende stap het identificeren van de CPU-resources op. De meest voorkomende methoden voor het weer geven van de hoogste wacht type categorieën zijn:

- Het [query archief](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) biedt gedurende een bepaalde periode wacht statistieken per query. In query Store worden wachtende typen gecombineerd in wachtende categorieën. De toewijzing van wachtende Categorieën op typen is beschikbaar in [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) retourneert informatie over alle wacht tijden die zijn aangetroffen door threads die worden uitgevoerd tijdens de bewerking. U kunt deze geaggregeerde weer gave gebruiken om prestatie problemen met Azure SQL Database op te sporen en ook met specifieke query's en batches.
- [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) retourneert informatie over de wacht wachtrij met taken die wachten op een bepaalde resource.

In scenario's met een hoge CPU weer spie gelen de query Store-en wait-statistieken niet altijd het CPU-gebruik om de volgende twee redenen:

- Query's met een hoog CPU-verbruik kunnen nog steeds worden uitgevoerd en de query's zijn nog niet voltooid
- De query's met een hoog CPU-verbruik zijn actief toen een failover werd uitgevoerd

Query Store en wacht tijden bijhouden dynamische beheer weergaven geven alleen resultaten weer voor voltooide en time-out query's en geven geen gegevens weer voor de instructies die momenteel worden uitgevoerd (totdat ze zijn voltooid). Met de dynamische beheer weergave [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) kunt u op dit moment query's volgen die momenteel worden uitgevoerd en de bijbehorende werk tijd.

Zoals in de vorige grafiek wordt weer gegeven, zijn de meest voorkomende wacht tijden:

- Vergren delingen (blok keren)
- I/O
- `tempdb`-gerelateerde conflicten
- Wachten op geheugen toekenning

> [!IMPORTANT]
> Zie voor een set a T-SQL-query's met behulp van deze Dmv's voor het oplossen van deze wachtende problemen:
>
> - [I/O-prestatie problemen identificeren](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Prestatie `tempdb` problemen identificeren](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Wachten op geheugen toekenningen bepalen](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox-wacht tijden en Latches](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Database prestaties verbeteren met meer bronnen

Ten slotte, als er geen actie kan worden uitgevoerd voor items die de prestaties van uw data base kunnen verbeteren, kunt u de hoeveelheid beschik bare resources in Azure SQL Database wijzigen. Wijs meer resources toe door de [DTU-servicelaag](sql-database-service-tiers-dtu.md) van één Data Base te wijzigen of de edtu's van een elastische pool op elk gewenst moment te verg Roten. Als u het [inkoop model op basis van vCore](sql-database-service-tiers-vcore.md)gebruikt, wijzigt u de servicelaag of verhoogt u de resources die zijn toegewezen aan uw data base.

1. Voor afzonderlijke data bases kunt u [service lagen wijzigen](sql-database-single-database-scale.md) of [bronnen](sql-database-single-database-scale.md) op aanvraag berekenen om de prestaties van de data base te verbeteren.
2. Voor meerdere data bases kunt u gebruikmaken van [elastische Pools](sql-database-elastic-pool-guidance.md) om resources automatisch te schalen.

## <a name="tune-and-refactor-application-or-database-code"></a>Toepassing of database code voor afstemmen en refactorie

U kunt de toepassings code wijzigen in de data base optimaal te gebruiken, indexen te wijzigen, plannen af te dwingen of hints te gebruiken om de data base hand matig aan uw werk belasting toe te passen. Vind hulp en tips voor het hand matig afstemmen en herschrijven van de code in het artikel [richt lijnen voor prestaties](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>Volgende stappen

- Als u automatisch afstemmen in Azure SQL Database wilt inschakelen en de werk belasting door automatische afstemming wilt laten beheren, raadpleegt u [automatisch afstemmen inschakelen](sql-database-automatic-tuning-enable.md).
- Als u hand matige afstemming wilt gebruiken, kunt u afstemmings [aanbevelingen in azure Portal](sql-database-advisor-portal.md) bekijken en hand matig Toep assen om de prestaties van uw query's te verbeteren.
- Resources die beschikbaar zijn in uw data base wijzigen door [Azure SQL database service lagen](sql-database-performance-guidance.md) te wijzigen
