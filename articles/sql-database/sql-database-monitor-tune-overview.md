---
title: Prestaties bewaken en afstemmen
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
ms.openlocfilehash: e77af00dc3352af3265da90685e58b34c96bee81
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382340"
---
# <a name="monitoring-and-performance-tuning"></a>Prestaties bewaken en afstemmen

Azure SQL Database voorziet in hulpprogram ma's en methoden die u kunt gebruiken om het gebruik te controleren, resources toe te voegen of te verwijderen (zoals CPU, geheugen of I/O), mogelijke problemen op te lossen en aanbevelingen te doen voor het verbeteren van de prestaties van een Data Base. Functies in Azure SQL Database kunnen automatisch problemen oplossen in de data bases. 

Automatisch afstemmen maakt het mogelijk een data base aan te passen aan de werk belasting en automatisch de prestaties te optimaliseren. Er zijn echter enkele aangepaste problemen die kunnen leiden tot probleem oplossing. In dit artikel worden enkele aanbevolen procedures en andere hulpprogram ma's beschreven die u kunt gebruiken om prestatie problemen op te lossen.

Om ervoor te zorgen dat een Data Base zonder problemen wordt uitgevoerd, moet u het volgende doen:
- De prestaties van de [Data Base controleren](#monitor-database-performance) om ervoor te zorgen dat de resources die aan de Data Base zijn toegewezen de werk belasting kunnen afhandelen. Als de data base bronnen limieten heeft, overweeg dan het volgende:
   - Het identificeren en optimaliseren van de belangrijkste query's die van resources worden verbruikt.
   - Meer resources toevoegen door [de](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)servicelaag bij te werken.
- [Los problemen met de prestaties](#troubleshoot-performance-problems) op om te bepalen waarom er een mogelijk probleem is opgetreden en om de hoofd oorzaak van het probleem vast te stellen. Nadat u de hoofd oorzaak hebt geïdentificeerd, voert u stappen uit om het probleem op te lossen.

## <a name="monitor-database-performance"></a>Databaseprestaties bewaken

Als u de prestaties van een SQL database in azure wilt bewaken, moet u beginnen met het controleren van de resources die worden gebruikt ten opzichte van het prestatie niveau dat u hebt gekozen. Controleer de volgende bronnen:
 - **CPU-gebruik**: Controleer of de data base gedurende een lange periode 100 procent van het CPU-gebruik bereikt. Een hoog CPU-gebruik kan erop wijzen dat u query's moet identificeren en afstemmen die gebruikmaken van de meeste reken kracht. Met een hoog CPU-gebruik kan ook worden aangegeven dat de data base of het exemplaar moet worden bijgewerkt naar een hogere servicelaag. 
 - **Wacht statistieken**: gebruik [sys. dm_os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) om te bepalen hoe lang query's wachten. Query's kunnen wachten op resources, wachten op wacht rijen of externe wacht tijden. 
 - **Io-gebruik**: Controleer of de data base de i/o-limieten van de onderliggende opslag bereikt.
 - **Geheugen gebruik**: de hoeveelheid geheugen die beschikbaar is voor de data base of het exemplaar, is evenredig met het aantal vCores. Zorg ervoor dat het geheugen voldoende is voor de werk belasting. Pagina duur verwachting is een van de para meters die kunnen aangeven hoe snel de pagina's uit het geheugen worden verwijderd.

De Azure SQL Database-Service bevat hulpprogram ma's en bronnen om u te helpen bij het oplossen van mogelijke prestatie problemen. U kunt verkoop kansen identificeren om de query prestaties te verbeteren en te optimaliseren zonder resources te wijzigen door de aanbevelingen voor het [afstemmen van prestaties](sql-database-advisor.md)te controleren. 

Ontbrekende indexen en onvoldoende geoptimaliseerde query's zijn veelvoorkomende redenen voor slechte databaseprestaties. U kunt afstemmings aanbevelingen Toep assen om de prestaties van de werk belasting te verbeteren. U kunt Azure SQL Database ook [de prestaties van de query's automatisch laten optimaliseren](sql-database-automatic-tuning.md) door alle geïdentificeerde aanbevelingen toe te passen. Controleer vervolgens of de aanbevelingen de prestaties van de data base verbeteren.

> [!NOTE]
> Indexeren is alleen beschikbaar in één data base en elastische Pools. Indexeren is niet beschikbaar in een beheerd exemplaar.

Kies uit de volgende opties om de database prestaties te controleren en op te lossen:

- Selecteer in de [Azure Portal](https://portal.azure.com) **SQL-data bases** en selecteer de data base. Zoek in de **bewakings** grafiek naar bronnen die het maximale gebruik benadert. Het DTU-verbruik wordt standaard weer gegeven. Selecteer **bewerken** om het tijds bereik en de weer gegeven waarden te wijzigen.
- Hulpprogram ma's zoals SQL Server Management Studio bieden veel nuttige rapporten, zoals [prestatie dashboard](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Gebruik deze rapporten om het resource gebruik te bewaken en de meest voorkomende query's voor resources te identificeren. U kunt [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) gebruiken om query's te identificeren waarvan de prestaties teruggedraaide hebben.
- Gebruik in de [Azure Portal](https://portal.azure.com) [query Performance Insight](sql-database-query-performance.md) om de query's te identificeren die gebruikmaken van de meeste resources. Deze functie is alleen beschikbaar voor afzonderlijke data bases en elastische Pools.
- Gebruik [SQL database Advisor](sql-database-advisor-portal.md) om aanbevelingen te bekijken om u te helpen bij het maken en verwijderen van indexen, para meters query's en het oplossen van schema problemen. Deze functie is alleen beschikbaar voor afzonderlijke data bases en elastische Pools.
- Gebruik [Azure SQL intelligent Insights](sql-database-intelligent-insights.md) om de prestaties van de data base automatisch te controleren. Wanneer er een prestatie probleem wordt gedetecteerd, wordt een diagnostisch logboek gegenereerd. Het logboek bevat details en een hoofd oorzaak analyse (RCA) van het probleem. Indien mogelijk is er een aanbeveling voor verbetering van de prestaties.
- [Schakel automatisch afstemmen](sql-database-automatic-tuning-enable.md) in om automatisch prestatie problemen door Azure SQL database te laten oplossen.
- Gebruik [dynamische beheer weergaven (dmv's)](sql-database-monitoring-with-dmvs.md), [uitgebreide gebeurtenissen](sql-database-xevent-db-diff-from-svr.md)en [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) voor meer informatie over het oplossen van problemen met prestaties.

> [!TIP]
> Nadat u een prestatie probleem hebt geïdentificeerd, raadpleegt u onze [richt lijnen voor prestaties](sql-database-performance-guidance.md) om technieken te vinden voor het verbeteren van de prestaties van Azure SQL database.

## <a name="troubleshoot-performance-problems"></a>Prestatie problemen oplossen

Om prestatie problemen vast te stellen en op te lossen, begint u met het vinden van de status van elke actieve query en de voor waarden die prestatie problemen veroorzaken die relevant zijn voor elke werk belasting status. Als u de prestaties van Azure SQL Database wilt verbeteren, moet u weten dat elke actieve query aanvraag van de toepassing in een actieve status of in een wacht status is. Bij het oplossen van problemen met de prestaties van Azure SQL Database, houdt u het volgende diagram in acht.

![Status van werk belasting](./media/sql-database-monitor-tune-overview/workload-states.png)

Een prestatie probleem in een workload kan worden veroorzaakt door een CPU-conflict (een aan de slag gerelateerde voor waarde) of afzonderlijke query's die wachten op iets (een voor waarde *met* *betrekking tot een wacht* tijd).

Problemen met betrekking tot de uitvoering kunnen worden veroorzaakt door:
- **Compilatie problemen**: SQL query Optimizer kan een suboptimaal plan veroorzaken vanwege verouderde statistieken, een onjuiste schatting van het aantal te verwerken rijen of een onnauwkeurige schatting van het vereiste geheugen. Als u weet dat de query sneller is uitgevoerd in het verleden of op een ander exemplaar (een beheerd exemplaar of een SQL Server-exemplaar), vergelijkt u de werkelijke uitvoerings plannen om te zien of ze verschillend zijn. Probeer query hints toe te passen of de statistieken of indexen opnieuw samen te stellen om het betere plan te krijgen. Schakel automatische correctie van plannen in Azure SQL Database in om deze problemen automatisch te verhelpen.
- **Uitvoerings problemen**: als het query plan optimaal is, worden de resource limieten van de data base waarschijnlijk bereikt, zoals het door voeren van een schrijf bewerking in het logboek. Het is ook mogelijk dat er gefragmenteerde indexen worden gebruikt die opnieuw moeten worden opgebouwd. Er kunnen ook uitvoerings problemen optreden wanneer een groot aantal gelijktijdige query's dezelfde bronnen nodig heeft. *Wacht* problemen zijn doorgaans gerelateerd aan uitvoerings problemen, omdat de query's die niet efficiënt worden uitgevoerd, waarschijnlijk wachten op een aantal resources.

Er kunnen problemen met de wacht worden veroorzaakt door:
- **Blok keren**: een query kan de vergren deling van objecten in de Data Base bevatten terwijl andere gebruikers toegang proberen te krijgen tot dezelfde objecten. U kunt blokkerende query's identificeren met behulp van Dmv's-of controle hulpprogramma's.
- **I/o-problemen**: query's kunnen wachten tot de pagina's naar de gegevens of logboek bestanden zijn geschreven. Controleer in dit geval de `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`of `PAGEIOLATCH_*` wacht statistieken in de DMV.
- **Problemen met Tempdb**: als de workload tijdelijke tabellen gebruikt of als er sprake is van tempdb-overloop in de plannen, hebben de query's mogelijk een probleem met de door Voer van tempdb. 
- **Problemen met het geheugen**: als de werk belasting onvoldoende geheugen heeft, kan het verwachting van de pagina verloren gaan of kunnen de query's minder geheugen krijgen dan nodig is. In sommige gevallen worden met ingebouwde intelligentie in query Optimizer problemen met het geheugen opgelost.
 
In de volgende secties wordt uitgelegd hoe u bepaalde soorten problemen kunt identificeren en oplossen.

## <a name="performance-problems-related-to-running"></a>Prestatie problemen met betrekking tot het uitvoeren van

Als algemene richt lijn, als het CPU-gebruik consistent is met of hoger 80 procent, is er een probleem met de prestaties. Een probleem met betrekking tot de werking kan worden veroorzaakt door onvoldoende CPU-resources. Of het kan zijn gerelateerd aan een van de volgende voor waarden:

- Te veel query's uitvoeren
- Te veel query's voor het compileren
- Een of meer query's die gebruikmaken van een suboptimaal query plan

Als u een probleem hebt met betrekking tot prestaties, is het doel om het precieze probleem te identificeren door een of meer methoden te gebruiken. Deze methoden zijn de meest voorkomende manieren om uitvoerings problemen te identificeren:

- Gebruik de [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) om het CPU-percentage gebruik te bewaken.
- Gebruik de volgende [dmv's](sql-database-monitoring-with-dmvs.md):

  - De [sys. dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) dmv retourneert CPU, I/O en geheugen verbruik voor een SQL database. Er bestaat één rij voor elk interval van 15 seconden, zelfs als er geen activiteit in de data base is. Historische gegevens worden één uur bewaard.
  - De [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) dmv retourneert het CPU-gebruik en de opslag gegevens voor Azure SQL database. De gegevens worden verzameld en samengevoegd in intervallen van vijf minuten.

> [!IMPORTANT]
> Zie [CPU-prestatie problemen identificeren](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)voor informatie over het oplossen van problemen met CPU-gebruik voor T-SQL-query's die gebruikmaken van de dmv's sys. dm_db_resource_stats en sys. resource_stats.

### <a name="ParamSniffing"></a>Query's met PSP-problemen

Er treedt een fout op in het parameter gevoelige plan (PSP) wanneer de query Optimizer een query-uitvoerings plan genereert dat optimaal is voor een specifieke parameter waarde (of een set waarden) en het schema in de cache vervolgens niet optimaal is voor parameter waarden die opeenvolgend worden gebruikt uitvoeringen. Plannen die niet optimaal zijn, kunnen vervolgens problemen met de prestaties van query's veroorzaken en de door Voer van de algehele werk belasting verminderen. 

Zie voor meer informatie over para meter-sniffing en query verwerking de [hand leiding](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)voor het verwerken van query's.

Diverse tijdelijke oplossingen kunnen PSP-problemen oplossen. Elke tijdelijke oplossing heeft voor-en nadelen:

- De query Hint [REcompile](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) gebruiken bij elke uitvoering van de query. Deze tijdelijke oplossing verhandelt compilatie tijd en verhoogde CPU voor een betere plan kwaliteit. De optie `RECOMPILE` is vaak niet mogelijk voor werk belastingen die een hoge door Voer vereisen.
- Gebruik de query Hint [(Optimize for...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) om de feitelijke parameter waarde te overschrijven met een typische parameter waarde die een plan produceert dat voldoende is voor de meeste mogelijkheden voor parameter waarden. Deze optie vereist een goed idee van de optimale parameter waarden en de bijbehorende plan kenmerken.
- Gebruik de [optie (optimaliseren voor onbekende)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) query hint om de werkelijke parameter waarde te overschrijven en gebruik in plaats daarvan het gemiddelde van de dichtheids vector te gebruiken. U kunt dit ook doen door de binnenkomende parameter waarden vast te leggen in lokale variabelen en vervolgens de lokale variabelen binnen de predikaten te gebruiken in plaats van de para meters zelf te gebruiken. Voor deze oplossing moet de gemiddelde densiteit *goed genoeg*zijn.
- Schakel para meter-sniffing helemaal uit met behulp van de query Hint [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Gebruik de [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) -query hint om hercompilaties in de cache te voor komen. Bij deze tijdelijke oplossing wordt ervan uitgegaan dat het goed gang bare plan al in de cache staat. U kunt ook automatische statistieken voor updates uitschakelen om de kans te verkleinen dat het goede plan wordt verwijderd en er wordt een nieuw, beschadigd plan gecompileerd.
- Dwing het plan expliciet met behulp van de query Hint [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) door de query opnieuw te schrijven en de hint in de query tekst toe te voegen. Of stel een specifiek plan in met behulp van query Store of door [automatisch afstemmen](sql-database-automatic-tuning.md)in te scha kelen.
- Vervang de ene procedure door een geneste set procedures die elk kan worden gebruikt op basis van voorwaardelijke logica en de bijbehorende parameter waarden.
- Alternatieven voor het uitvoeren van dynamische teken reeksen maken voor een statische procedure definitie.

Voor meer informatie over het oplossen van PSP-problemen raadpleegt u deze blog berichten:

- [Ik geur een para meter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor versus dynamische SQL versus procedures versus plan kwaliteit voor query's met para meters](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Optimalisatie technieken voor SQL-query's in SQL Server: parameter sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Compilatie activiteit veroorzaakt door onjuiste parameterisering

Wanneer een query letterlijke waarden bevat, wordt de instructie automatisch door de data base-engine parameterizes of een gebruiker parameterizes de instructie expliciet om het aantal compilaties te verminderen. Een groot aantal compilaties voor een query met hetzelfde patroon, maar met verschillende letterlijke waarden kan leiden tot een hoog CPU-gebruik. Als u een query slechts gedeeltelijk para meters met letterlijke waarden, wordt de query niet meer door de data base-engine para meters.  

Hier volgt een voor beeld van een gedeeltelijk Geparametriseerde query:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In dit voor beeld neemt `t1.c1` `@p1`, maar `t2.c2` blijft GUID als letterlijke waarde. Als u in dit geval de waarde voor `c2`wijzigt, wordt de query als een andere query behandeld en wordt er een nieuwe compilatie uitgevoerd. Als u de compilaties in dit voor beeld wilt reduceren, zou u ook de GUID para meters.

Met de volgende query wordt het aantal query's per query-hash weer gegeven om te bepalen of een query juist is para meters:

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

### <a name="factors-that-affect-query-plan-changes"></a>Factoren die van invloed zijn op de wijzigingen in het query plan

Een hercompilatie van een query uitvoerings plan kan leiden tot een gegenereerd query plan dat verschilt van het oorspronkelijke schema van de cache. Een bestaand oorspronkelijk plan kan om verschillende redenen automatisch opnieuw worden gecompileerd:
- Er wordt door de query naar wijzigingen in het schema verwezen.
- Er wordt naar gegevens wijzigingen in de tabellen verwezen door de query. 
- De query context opties zijn gewijzigd.

Een gecompileerd plan kan om verschillende redenen uit de cache worden uitgeworpen, zoals:

- Het exemplaar wordt opnieuw opgestart.
- Wijzigingen in de configuratie van het database bereik.
- Geheugen druk.
- Expliciete aanvragen voor het wissen van de cache.

Als u een RECOMPILE-Hint gebruikt, wordt er geen plan in de cache opgeslagen.

Een hercompilatie (of nieuwe compilatie na verwijdering van de cache) kan nog steeds leiden tot het genereren van een query-uitvoerings plan dat identiek is aan het origineel. Wanneer het plan wordt gewijzigd ten opzichte van het vorige of oorspronkelijke abonnement, zijn deze toelichtingen waarschijnlijk:

- **Gewijzigd fysiek ontwerp**: nieuwe indexen maken bijvoorbeeld effectiever de vereisten van een query. De nieuwe indexen kunnen worden gebruikt voor een nieuwe compilatie als de query optimalisatie besluit dat het gebruik van die nieuwe index betrouwbaarder is dan het gebruik van de gegevens structuur die oorspronkelijk was geselecteerd voor de eerste versie van de uitvoering van de query.  Eventuele fysieke wijzigingen in de objecten waarnaar wordt verwezen, kunnen leiden tot een nieuwe plannings optie tijdens het compileren.

- **Verschillen in Server bronnen**: wanneer een plan in het ene systeem verschilt van het plan in een ander systeem, kan de beschik baarheid van resources, zoals het aantal beschik bare processors, invloed hebben op welk plan wordt gegenereerd.  Als een systeem bijvoorbeeld meer processors heeft, kan een parallelle planning worden gekozen. 

- **Andere statistieken**: de statistieken die zijn gekoppeld aan de objecten waarnaar wordt verwezen, zijn mogelijk gewijzigd of kunnen afwijken van de statistieken van het oorspronkelijke systeem.  Als de statistieken veranderen en een hercompilatie plaatsvindt, gebruikt de query Optimizer de statistieken die beginnen wanneer ze zijn gewijzigd. De gegevens distributies en-frequenties van de herziene statistieken kunnen verschillen van die van de oorspronkelijke compilatie.  Deze wijzigingen worden gebruikt voor het maken van kardinaliteit. (*Schattingen van kardinaliteit* zijn het aantal rijen dat naar verwachting door de logische query structuur loopt.) Wijzigingen in de kardinaliteit kunnen ertoe leiden dat u verschillende fysieke Opera tors en gekoppelde orders van bewerkingen kunt kiezen.  Zelfs kleine wijzigingen in statistieken kunnen leiden tot een gewijzigd query-uitvoerings plan.

- Het **database compatibiliteits niveau of de Estimator-versie van de kardinaliteit is gewijzigd**: wijzigingen in het database compatibiliteits niveau kunnen nieuwe strategieën en functies mogelijk maken die kunnen leiden tot een ander uitvoerings plan voor query's.  Buiten het database compatibiliteits niveau kan een uitgeschakelde of ingeschakelde tracerings vlag 4199 of een gewijzigde status van de database bereik configuratie QUERY_OPTIMIZER_HOTFIXES ook van invloed zijn op het uitvoerings plan van de query tijdens het compileren.  Tracerings vlaggen 9481 (geforceerd verouderd CE) en 2312 (standaard-CE forceren) zijn ook van invloed op het plan. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Probleem query's oplossen of meer resources opgeven

Nadat u het probleem hebt vastgesteld, kunt u de probleem query's afstemmen of de berekenings grootte of servicelaag upgraden om de capaciteit van uw SQL database te verg Roten om de CPU-vereisten te absorberen. 

Zie voor meer informatie bronnen voor het [schalen van één data base in Azure SQL database](sql-database-single-database-scale.md) en het [schalen van elastische pool resources in Azure SQL database](sql-database-elastic-pool-scale.md). Zie [resource limieten voor de service tier](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)voor meer informatie over het schalen van een beheerd exemplaar.

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Prestatie problemen als gevolg van een toegenomen hoeveelheid werk belasting

Een toename van het toepassings verkeer en het werkbelasting volume kan een groter CPU-gebruik veroorzaken. Maar u moet er wel voor zorgen dat u dit probleem correct kunt vaststellen. Wanneer u een probleem met een hoge CPU ziet, beantwoordt u deze vragen om te bepalen of de toename wordt veroorzaakt door wijzigingen in het werkbelasting volume:

- Zijn de query's van de toepassing de oorzaak van het probleem met de hoge CPU?
- Voor de top van CPU-verbruiks query's die u kunt herkennen:

   - Zijn er meerdere uitvoerings plannen gekoppeld aan dezelfde query? Als dat het geval is, waarom?
   - Voor query's met hetzelfde uitvoerings plan zijn de uitvoerings tijden consistent? Is het aantal uitvoeringen verhoogd? Als dit het geval is, worden er waarschijnlijk problemen met de werk belasting veroorzaakt.

Als het query-uitvoerings plan niet anders is uitgevoerd, maar het CPU-gebruik is verhoogd met het aantal uitvoeringen, is het prestatie probleem waarschijnlijk gerelateerd aan een verhoging van de werk belasting.

Het is niet altijd gemakkelijk om een wijziging van het werkbelasting volume te identificeren dat een CPU-probleem ondervindt. Houd rekening met de volgende factoren: 

- **Resource gebruik gewijzigd**: u kunt bijvoorbeeld een scenario gebruiken waarbij het CPU-gebruik gedurende een lange periode is verhoogd tot 80 procent.  Het CPU-gebruik betekent alleen dat het werkbelasting volume is gewijzigd. Regressies in het query-uitvoerings plan en wijzigingen in de gegevens distributie kunnen ook bijdragen aan meer resource gebruik, zelfs als de toepassing dezelfde werk belasting uitvoert.

- **Het uiterlijk van een nieuwe query**: een toepassing kan op verschillende tijdstippen een nieuwe set query's aansturen.

- **Een verhoging of afname van het aantal aanvragen**: dit scenario is de meest duidelijke maat regel voor een werk belasting. Het aantal query's komt niet altijd overeen met meer resource gebruik. Deze metriek is echter nog steeds een significant signaal, ervan uitgaande dat andere factoren ongewijzigd blijven.

## <a name="waiting-related-performance-problems"></a>Problemen met betrekking tot de prestaties 

Als u zeker weet dat het prestatie probleem niet is gerelateerd aan het hoge CPU-gebruik of om te worden uitgevoerd, heeft het probleem te maken met wachten. Dat wil zeggen dat uw CPU-resources niet efficiënt worden gebruikt omdat de CPU op een andere resource wacht. In dit geval moet u bepalen wat uw CPU-bronnen wachten. 

Deze methoden worden vaak gebruikt om de hoogste categorieën van wacht typen weer te geven:

- Gebruik [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) om gedurende een bepaalde periode wacht statistieken voor elke query te vinden. In query Store worden wachtende typen gecombineerd in wachtende categorieën. U kunt de toewijzing van wachtende Categorieën vinden door te wachten op typen in [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Gebruik [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) om informatie te retour neren over alle wacht tijden die zijn aangetroffen door threads die worden uitgevoerd tijdens de bewerking. U kunt deze geaggregeerde weer gave gebruiken om prestatie problemen vast te stellen met Azure SQL Database en ook met specifieke query's en batches.
- Gebruik [sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) om informatie te retour neren over de wachtrij met taken die op een bepaalde resource wachten.

In scenario's met een hoge CPU worden in de query Store-en wait-statistieken mogelijk geen CPU-gebruik weer gegeven als:

- Query's met een hoog CPU-verbruik worden nog steeds uitgevoerd.
- De query's met een hoog CPU-verbruik worden uitgevoerd tijdens een failover.

Dmv's die query Store bijhouden en wachten op statistieken tonen alleen resultaten voor voltooide en time-out query's. Er worden geen gegevens weer gegeven voor de instructies die momenteel worden uitgevoerd tot de instructies zijn voltooid. Gebruik de dynamische beheer weergave [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) voor het bijhouden van query's die momenteel worden uitgevoerd en de bijbehorende werk tijd.

In het diagram aan het begin van dit artikel ziet u dat de meest voorkomende wacht tijden zijn:

- Vergren delingen (blok keren)
- I/O
- Conflicten met betrekking tot TempDB
- Wachten op geheugen toekenning

> [!IMPORTANT]
> Zie voor een set T-SQL-query's die Dmv's gebruiken om problemen met wachtende gebeurtenissen op te lossen:
>
> - [I/O-prestatie problemen identificeren](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Wachten op geheugen toekenningen bepalen](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox-wacht tijden en latches](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Verbeter de database prestaties met meer bronnen

Als er geen items kunnen worden verbeterd, kunt u de hoeveelheid beschik bare resources in Azure SQL Database wijzigen. Wijs meer resources toe door de [DTU-servicelaag](sql-database-service-tiers-dtu.md) van één Data Base te wijzigen. Of verg root de Edtu's van een elastische pool op elk gewenst moment. Als u het [inkoop model op basis van vCore](sql-database-service-tiers-vcore.md)gebruikt, moet u ook de servicelaag wijzigen of de resources verhogen die aan uw data base zijn toegewezen.

Voor afzonderlijke data bases kunt u [service lagen wijzigen of bronnen](sql-database-single-database-scale.md) op aanvraag berekenen om de database prestaties te verbeteren. Voor meerdere data bases kunt u gebruikmaken van [elastische Pools](sql-database-elastic-pool-guidance.md) om resources automatisch te schalen.

## <a name="tune-and-refactor-application-or-database-code"></a>Toepassing of database code voor afstemmen en refactorie

U kunt de toepassings code voor de data base optimaliseren, indexen wijzigen, plannen forceren of hints gebruiken om de data base hand matig aan uw werk belasting toe te passen. Zie [richt lijnen voor het afstemmen van prestaties](sql-database-performance-guidance.md)voor meer informatie over het hand matig afstemmen en het herschrijven van de code.

## <a name="next-steps"></a>Volgende stappen

- Als u automatisch afstemmen in Azure SQL Database wilt inschakelen en de werk belasting door de functie voor automatisch afstemmen volledig wilt laten beheren, raadpleegt u [automatisch afstemmen inschakelen](sql-database-automatic-tuning-enable.md).
- Als u hand matige afstemming wilt gebruiken, bekijkt u [de aanbevelingen voor het afstemmen in de Azure Portal](sql-database-advisor-portal.md). Pas de aanbevelingen voor het verbeteren van de prestaties van uw query's hand matig toe.
- Wijzig de beschik bare resources in uw data base door [Azure SQL database service lagen](sql-database-performance-guidance.md)te wijzigen.
