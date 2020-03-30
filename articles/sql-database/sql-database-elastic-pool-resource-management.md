---
title: Resourcebeheer in dichte elastische zwembaden | Microsoft Documenten
description: Compute resources beheren in Azure SQL-elastische pools met veel databases.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473724"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Resourcebeheer in dichte elastische pools

Azure SQL Database [elastic pools](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) is een kosteneffectieve oplossing voor het beheren van veel databases met wisselend resourcegebruik. Alle databases in een elastische groep delen dezelfde toewijzing van resources, zoals CPU, geheugen, werknemersthreads, opslagruimte, tempdb, in de veronderstelling dat **alleen een subset van databases in de groep op een bepaald moment rekenbronnen zal gebruiken.** Deze veronderstelling maakt het mogelijk elastische zwembaden kosteneffectief te zijn. In plaats van te betalen voor alle resources die elke afzonderlijke database mogelijk nodig zou kunnen hebben, betalen klanten voor een veel kleinere set resources, gedeeld tussen alle databases in de groep.

## <a name="resource-governance"></a>Resourcebeheer

Voor het delen van resources moet het systeem het gebruik van resources zorgvuldig beheren om het effect 'luidruchtige buren' te minimaliseren, waarbij een database met een hoog resourceverbruik van invloed is op andere databases in dezelfde elastische groep. Tegelijkertijd moet het systeem voldoende resources bieden voor functies zoals hoge beschikbaarheid en disaster recovery (HADR), back-up en herstel, monitoring, Query Store, Automatische afstemming, enz.

Azure SQL Database bereikt deze doelen door meerdere resourcegovernancemechanismen te gebruiken, waaronder Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) voor process level resource governance, Windows [File Server Resource Manager (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) voor storage quota beheer en een gewijzigde en uitgebreide versie van SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) om resource governance te implementeren binnen elk SQL Server-exemplaar dat wordt uitgevoerd in Azure SQL Database.

Het primaire ontwerpdoel van elastische zwembaden is om kosteneffectief te zijn. Om deze reden stelt het systeem klanten opzettelijk in staat om _dichte_ groepen te maken, dat wil zeggen pools met het aantal databases dat nadert of op het maximaal toegestane niveau, maar met een gematigde toewijzing van rekenresources. Om dezelfde reden reserveert het systeem niet alle mogelijk benodigde resources voor de interne processen, maar maakt het het delen van resources tussen interne processen en gebruikersworkloads mogelijk.

Deze aanpak stelt klanten in staat om dichte elastische zwembaden te gebruiken om adequate prestaties en grote kostenbesparingen te bereiken. Als de werklast ten opzichte van veel databases in een dichte groep echter voldoende intens is, wordt de geschil over resources aanzienlijk. Resourcetwist vermindert de prestaties van de gebruikerswerklast en kan een negatieve invloed hebben op interne processen.

> [!IMPORTANT]
> In dichte pools met veel actieve databases is het mogelijk niet haalbaar om het aantal databases in de groep te verhogen tot de maxima die zijn gedocumenteerd voor [DTU-](sql-database-dtu-resource-limits-elastic-pools.md) en [vCore-elastische](sql-database-vcore-resource-limits-elastic-pools.md) pools.
> 
> Het aantal databases dat in dichte groepen kan worden geplaatst zonder problemen met resourceconflicten en prestaties te veroorzaken, is afhankelijk van het aantal gelijktijdig actieve databases en van het verbruik van resources door gebruikersworkloads in elke database. Dit aantal kan in de loop van de tijd veranderen als gebruikersworkloads veranderen.

Wanneer resource-twist optreedt in een dichtbevolkte groep, kunnen klanten een of meer van de volgende acties kiezen om deze te beperken:
- Stem de querywerklast af om het resourceverbruik te verminderen of verspreid het resourceverbruik in de loop van de tijd over meerdere databases.
- Verklein de dichtheid van de groep door sommige databases naar een andere groep te verplaatsen of door ze zelfstandige databases te maken.
- Schaal de pool op om meer resources te krijgen.

Zie [Operationele aanbevelingen](#operational-recommendations) later in dit artikel voor suggesties over de uitvoering van de laatste twee acties. Het verminderen van resource-twistvoordelen voor zowel gebruikersworkloads als interne processen en laat het systeem het verwachte serviceniveau betrouwbaar handhaven.

## <a name="monitoring-resource-consumption"></a>Het verbruik van hulpbronnen bewaken

Om prestatiedegradatie als gevolg van resourcetwist te voorkomen, moeten klanten die dichte elastische pools gebruiken proactief het verbruik van resources controleren en tijdig actie ondernemen als de toenemende brononenigheid de werkbelasting begint te beïnvloeden. Continue bewaking is belangrijk omdat het gebruik van resources in een groep in de loop van de tijd verandert als gevolg van wijzigingen in de werkbelasting van de gebruiker, wijzigingen in gegevensvolumes en -distributie, wijzigingen in de pooldichtheid en wijzigingen in de Azure SQL Database-service. 

Azure SQL Database biedt verschillende statistieken die relevant zijn voor dit type bewaking. Het overschrijden van de aanbevolen gemiddelde waarde voor elke statistiek duidt op resourcestelling in de groep en moet worden aangepakt met een van de eerder genoemde acties.

|Naam van meetwaarde|Beschrijving|Aanbevolen gemiddelde waarde| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|CPU-gebruik van het SQL Server-proces dat is gekoppeld aan een elastische pool, gemeten door het onderliggende besturingssysteem. Beschikbaar in de [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) weergave in elke database, en `master` in de [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weergave in de database. Deze statistiek wordt ook uitgezonden naar Azure Monitor, waar deze de [naam heeft](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`en kan worden weergegeven in azure-portal. Deze waarde is hetzelfde voor elke database in dezelfde elastische groep.|Onder de 70%. Af en toe korte pieken tot 90% kan aanvaardbaar zijn.|
|`max_worker_percent`|[Gebruik van de draad]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) van de arbeider. Voorzien voor elke database in het zwembad, evenals voor het zwembad zelf. Er zijn verschillende limieten voor het aantal werknemersthreads op databaseniveau en op poolniveau wordt daarom aanbevolen om deze statistiek op beide niveaus te controleren. Beschikbaar in de [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) weergave in elke database, en `master` in de [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weergave in de database. Deze statistiek wordt ook uitgezonden naar Azure Monitor, waar deze de [naam heeft](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`en kan worden weergegeven in azure-portal.|Onder de 80%. Pieken tot 100% zorgen ervoor dat verbindingspogingen en query's mislukken.|
|`avg_data_io_percent`|IOPS-gebruik voor lezen en schrijven van fysieke IO. Voorzien voor elke database in het zwembad, evenals voor het zwembad zelf. Er zijn verschillende limieten voor het aantal IOPS op databaseniveau en op poolniveau wordt daarom aanbevolen om deze statistiek op beide niveaus te controleren. Beschikbaar in de [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) weergave in elke database, en `master` in de [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weergave in de database. Deze statistiek wordt ook uitgezonden naar Azure Monitor, waar deze de [naam heeft](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`en kan worden weergegeven in azure-portal.|Onder de 80%. Af en toe korte pieken tot 100% kan aanvaardbaar zijn.|
|`avg_log_write_percent`|Doorvoergebruik voor transactielogboekschrijf IO. Voorzien voor elke database in het zwembad, evenals voor het zwembad zelf. Er zijn verschillende limieten voor de logdoorvoer op databaseniveau en op poolniveau wordt daarom aanbevolen om deze statistiek op beide niveaus te controleren. Beschikbaar in de [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) weergave in elke database, en `master` in de [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weergave in de database. Deze statistiek wordt ook uitgezonden naar Azure Monitor, waar deze de [naam heeft](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`en kan worden weergegeven in azure-portal. Wanneer deze statistiek bijna 100% bedraagt, worden alle databasewijzigingen (INSERT, UPDATE, DELETE, MERGE statements, SELECT ... INTO, BULK INSERT, enz.) zal langzamer zijn.|Onder de 90%. Af en toe korte pieken tot 100% kan aanvaardbaar zijn.|
|`oom_per_second`|De snelheid van out-of-memory (OOM) fouten in een elastische pool, dat is een indicator van de geheugendruk. Beschikbaar in de [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) weergave. Zie [Voorbeelden](#examples) voor een voorbeeldquery om deze statistiek te berekenen.|0|
|`avg_storage_percent`|Gebruik van opslagruimte op elastisch zwembadniveau. Beschikbaar in de [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) weergave in de `master` database. Deze statistiek wordt ook uitgezonden naar Azure Monitor, waar deze de [naam heeft](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`en kan worden weergegeven in azure-portal.|Onder de 80%. Kan 100% benaderen voor pools zonder datagroei.|
|`tempdb_log_used_percent`|Gebruik van transactielogboekruimte in de `tempdb` database. Hoewel tijdelijke objecten die in één database zijn gemaakt, niet `tempdb` zichtbaar zijn in andere databases in dezelfde elastische groep, is dit een gedeelde bron voor alle databases in dezelfde groep. Een langlopende of `tempdb` niet-actieve transactie die is gestart vanuit één database in de groep, kan een groot deel van het transactielogboek verbruiken en fouten veroorzaken voor query's in andere databases in dezelfde groep. Beschikbaar in de [sys.dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) weergave. Deze statistiek wordt ook uitgezonden naar Azure Monitor en kan worden weergegeven in Azure-portal. Zie [Voorbeelden](#examples) voor een voorbeeldquery om de huidige waarde van deze statistiek te retourneren.|Onder de 50%. Incidentele pieken tot 80% zijn aanvaardbaar.|
|||

Naast deze statistieken biedt Azure SQL Database een weergave die de werkelijke resourcegovernancelimieten retourneert, evenals aanvullende weergaven die resourcegebruiksstatistieken op resourcepoolniveau en op het niveau van de werkbelastinggroep retourneren.

|Naam weergeven|Beschrijving|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Geeft als resultaat de werkelijke configuratie- en capaciteitsinstellingen die worden gebruikt door resourcegovernancemechanismen in de huidige database of elastische groep.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Retourneert informatie over de huidige resourcegroepstatus, de huidige configuratie van resourcegroepen en cumulatieve resourcegroepstatistieken.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Geeft als resultaat cumulatieve werkbelastinggroepstatistieken en de huidige configuratie van de werkbelastinggroep. Deze weergave kan worden samengevoegd met `pool_id` sys.dm_resource_governor_resource_pools in de kolom om informatie over resourcepool te krijgen.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Geeft als resultaat de gebruiksstatistieken van resourcespool voor de laatste 32 minuten. Elke rij vertegenwoordigt een interval van 20 seconden. De `delta_` kolommen geven de wijziging in elke statistiek tijdens het interval terug.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Geeft als resultaat de statistieken voor het gebruik van werkbelastinggroepen voor de laatste 32 minuten. Elke rij vertegenwoordigt een interval van 20 seconden. De `delta_` kolommen geven de wijziging in elke statistiek tijdens het interval terug.|
|||

Deze weergaven kunnen worden gebruikt om het gebruik van resources te controleren en resourceconflicten in bijna realtime op te lossen. Gebruikerswerkbelasting op de primaire en leesbare secundaire replica's, `SloSharedPool1` waaronder `UserPrimaryGroup.DBId[N]` georeplica's, wordt ingedeeld in de resourcegroep en werkbelastinggroep, waar `N` staat voor de waarde van de database-id.

Naast het bewaken van het huidige resourcegebruik, kunnen klanten die dichte groepen gebruiken, historische gegevens over het gebruik van resources in een afzonderlijk gegevensarchief bijhouden. Deze gegevens kunnen worden gebruikt in voorspellende analyses om het gebruik van resources proactief te beheren op basis van historische en seizoensgebonden trends.

## <a name="operational-recommendations"></a>Operationele aanbevelingen

**Laat voldoende hoofdruimte over de bron.** Als er sprake is van onenigheid en prestatiedegradatie van resources, kan mitigatie inhouden dat sommige databases uit de betreffende elastische groep worden verplaatst of dat de groep wordt opgeschaald, zoals eerder is opgemerkt. Deze acties vereisen echter extra rekenresources om te voltooien. Voor premium- en bedrijfskritiekegroepen moeten deze acties met name alle gegevens overdragen voor de databases die worden verplaatst of voor alle databases in de elastische groep als de groep wordt opgeschaald. Gegevensoverdracht is een langdurige en resource-intensieve operatie. Als de pool al onder hoge resourcedruk staat, zal de verzachtende werking zelf de prestaties nog verder verslechteren. In extreme gevallen is het mogelijk niet mogelijk om resource-twist op te lossen via databaseverplaatsing of schaalvergroting in de database omdat de vereiste resources niet beschikbaar zijn. In dit geval kan het tijdelijk verminderen van de querywerklast op de betreffende elastische groep de enige oplossing zijn.

Klanten die dichte groepen gebruiken, moeten trends in het gebruik van resources, zoals eerder beschreven, nauwlettend volgen en verzachtende maatregelen nemen terwijl statistieken binnen de aanbevolen bereiken blijven en er nog voldoende resources in de elastische groep zijn.

Het gebruik van resources is afhankelijk van meerdere factoren die in de loop van de tijd veranderen voor elke database en elke elastische groep. Het bereiken van een optimale prijs-prestatieverhouding in dichte zwembaden vereist continue bewaking en herbalancering, dat is het verplaatsen van databases van meer gebruikte pools naar minder gebruikte zwembaden, en het creëren van nieuwe pools indien nodig om een grotere werkbelasting mogelijk te maken.

**Verplaats geen "hot" databases**. Als resourcetwist op het poolniveau voornamelijk wordt veroorzaakt door een klein aantal sterk gebruikte databases, kan het verleidelijk zijn om deze databases naar een minder gebruikte pool te verplaatsen of standalone databases te maken. Echter, om dit te doen terwijl een database blijft sterk gebruikt wordt niet aanbevolen, omdat de verhuizing operatie zal verder verslechteren prestaties, zowel voor de database wordt verplaatst, en voor de hele pool. In plaats daarvan, ofwel wachten tot een hoog gebruik afneemt, of verplaatsen minder gebruikte databases in plaats daarvan om de druk van de middelen te verlichten op het poolniveau. Maar het verplaatsen van databases met een zeer laag gebruik biedt in dit geval geen voordeel, omdat het resourcegebruik op poolniveau niet wezenlijk vermindert.

**Maak nieuwe databases in een "quarantaine" groep.** In scenario's waarin vaak nieuwe databases worden gemaakt, zoals toepassingen die het tenant-per-database-model gebruiken, bestaat het risico dat een nieuwe database die in een bestaande elastische groep wordt geplaatst, onverwacht aanzienlijke bronnen verbruikt en andere databases beïnvloedt en interne processen in het zwembad. Om dit risico te beperken, maakt u een aparte "quarantaine" pool met voldoende toewijzing van middelen. Gebruik deze groep voor nieuwe databases met nog onbekende resourceconsumptiepatronen. Zodra een database in deze groep is gebleven voor een conjunctuurcyclus, zoals een week of een maand, en het verbruik van resources bekend is, kan deze worden verplaatst naar een pool met voldoende capaciteit om dit extra resourcegebruik mogelijk te maken.

**Vermijd overdreven dichte logische servers.** Azure SQL Database [ondersteunt](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) maximaal 5000 databases per logische server. Klanten die elastische pools met duizenden databases gebruiken, kunnen overwegen om meerdere elastische pools op één server te plaatsen, waarbij het totale aantal databases tot de ondersteunde limiet wordt beperkt. Logische servers met vele duizenden databases zorgen echter voor operationele uitdagingen. Bewerkingen waarvoor alle databases op een server moeten worden opgesomd, bijvoorbeeld het bekijken van databases in de portal, zullen trager verlopen. Operationele fouten, zoals onjuiste wijziging van aanmeldingen op serverniveau of firewallregels, hebben gevolgen voor een groter aantal databases. Voor het per ongeluk verwijderen van de logische server is hulp van Microsoft Support nodig om databases op de verwijderde server te herstellen en veroorzaakt het een langdurige uitval voor alle getroffen databases.

We raden u aan het aantal databases per logische server te beperken tot een lager aantal dan het maximaal ondersteunde aantal. In veel scenario's is het gebruik van maximaal 1000-2000 databases per server optimaal. Om de kans op het per ongeluk verwijderen van de server te verkleinen, raden we u aan een [delete-vergrendeling](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) te plaatsen op de logische server of de brongroep.

In het verleden waren bepaalde scenario's met bewegende databases in, uit of tussen elastische pools op dezelfde logische server sneller dan bij het verplaatsen van databases tussen logische servers. Momenteel worden alle databasebewegingen met dezelfde snelheid uitgevoerd, ongeacht de bron- en doellogische server.

## <a name="examples"></a>Voorbeelden

### <a name="monitoring-memory-utilization"></a>Geheugengebruik controleren

Met deze query `oom_per_second` wordt de statistiek voor elke resourcegroep berekend in de afgelopen 32 minuten. Deze query kan worden uitgevoerd in elke database in een elastische groep.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Het `tempdb` gebruik van logboekruimte bewaken

Met deze query wordt `tempdb_log_used_percent` de huidige waarde van de statistiek geretourneerd. Deze query kan worden uitgevoerd in elke database in een elastische groep.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Volgende stappen

* Zie Elastische pools voor een inleiding tot elastische pools [waarmee u meerdere Azure SQL-databases beheren en schalen.](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Zie [Bewaken en afstemmen en]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index) [afstemmen](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)van query's voor informatie over het afstemmen van queryworkloads om het gebruik van resources te verminderen.
