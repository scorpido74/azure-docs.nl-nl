---
title: Beheer van bestandsruimte voor afzonderlijke/samengevoegde databases
description: Op deze pagina wordt beschreven hoe u bestandsruimte beheert met afzonderlijke en samengevoegde databases in Azure SQL Database en bevat codevoorbeelden om te bepalen of u één of een gepoolde database moet verkleinen en hoe u een databasekrimpende bewerking uitvoeren.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420979"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Bestandsruimte beheren voor afzonderlijke en samengevoegde databases in Azure SQL Database

In dit artikel worden verschillende typen opslagruimte voor afzonderlijke en samengevoegde databases in Azure SQL Database beschreven en stappen die kunnen worden genomen wanneer de bestandsruimte die is toegewezen voor databases en elastische pools expliciet moet worden beheerd.

> [!NOTE]
> Dit artikel is niet van toepassing op de beheerde instantieimplementatieoptie in Azure SQL Database.

## <a name="overview"></a>Overzicht

Met afzonderlijke en samengevoegde databases in Azure SQL Database zijn er werkbelastingpatronen waarbij de toewijzing van onderliggende gegevensbestanden voor databases groter kan worden dan de hoeveelheid gebruikte gegevenspagina's. Dit probleem kan optreden als er meer ruimte wordt gebruikt en er tegelijkertijd gegevens worden verwijderd. De reden hiervoor is dat toegewezen bestandsruimte niet automatisch wordt teruggewonnen wanneer gegevens worden verwijderd.

Mogelijk moet u in de volgende scenario's het gebruik van bestandsruimte bewaken en gegevensbestanden verkleinen:

- Sta groei van gegevens in een elastische pool toe als de toegewezen bestandsruimte voor de databases de maximale grootte van de pool bereikt.
- Sta toe dat de maximale grootte van één database of elastische pool wordt verkleind.
- Sta toe dat databases en elastische pools afzonderlijk naar een andere servicelaag of prestatielaag kunnen worden omgezet met een kleinere maximale grootte.

### <a name="monitoring-file-space-usage"></a>Gebruik van bestandsruimte controleren

De meeste statistieken voor opslagruimte die worden weergegeven in de Azure-portal en de volgende API's meten alleen de grootte van gebruikte gegevenspagina's:

- API's op basis van Azure Resource Manager, waaronder [PowerShell-get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

De volgende API's meten echter ook de grootte van de toegewezen ruimte voor databases en elastische pools:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Krimpende gegevensbestanden

De SQL Database-service krimpt gegevensbestanden niet automatisch om ongebruikte toegewezen ruimte terug te winnen vanwege de mogelijke impact op de databaseprestaties.  Klanten kunnen echter gegevensbestanden via selfservice krimpen op een moment dat ze kiezen door de stappen te volgen die zijn beschreven in het terugvorderen van [niet-gebruikte toegewezen ruimte.](#reclaim-unused-allocated-space)

> [!NOTE]
> In tegenstelling tot gegevensbestanden krimpt de SQL Database-service automatisch logboekbestanden, omdat die bewerking geen invloed heeft op de prestaties van de database.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Typen opslagruimte voor een database begrijpen

Inzicht in de volgende hoeveelheden opslagruimte zijn belangrijk voor het beheren van de bestandsruimte van een database.

|Databasehoeveelheid|Definitie|Opmerkingen|
|---|---|---|
|**Gebruikte gegevensruimte**|De hoeveelheid ruimte die wordt gebruikt om databasegegevens op te slaan in 8 KB-pagina's.|Over het algemeen neemt de gebruikte ruimte toe (afneemt) op inserts (deletes). In sommige gevallen verandert de gebruikte ruimte niet op inserts of deletes, afhankelijk van de hoeveelheid en het patroon van de gegevens die betrokken zijn bij de bewerking en eventuele fragmentatie. Als u bijvoorbeeld één rij van elke gegevenspagina verwijderd, wordt de gebruikte ruimte niet noodzakelijkerwijs kleiner.|
|**Toegewezen gegevensruimte**|De hoeveelheid opgemaakte bestandsruimte die beschikbaar is gesteld voor het opslaan van databasegegevens.|De toegewezen hoeveelheid ruimte groeit automatisch, maar neemt nooit af na verwijderingen. Dit gedrag zorgt ervoor dat toekomstige wisselplaten sneller zijn, omdat de ruimte niet hoeft te worden geformatteerd.|
|**Toegewezen gegevensruimte, maar ongebruikt**|Het verschil tussen de hoeveelheid toegewezen gegevensruimte en de gebruikte gegevensruimte.|Deze hoeveelheid vertegenwoordigt de maximale hoeveelheid vrije ruimte die kan worden teruggewonnen door databasegegevensbestanden te verkleinen.|
|**Maximale grootte van gegevens**|De maximale hoeveelheid ruimte die kan worden gebruikt voor het opslaan van databasegegevens.|De hoeveelheid toegewezen gegevensruimte kan niet verder groeien dan de maximale grootte van de gegevens.|

In het volgende diagram wordt de relatie tussen de verschillende typen opslagruimte voor een database weergegeven.

![opslagtypen en relaties](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Eén database opvragen voor informatie over opslagruimte

De volgende query's kunnen worden gebruikt om de hoeveelheden opslagruimte voor één database te bepalen.  

### <a name="database-data-space-used"></a>Gebruikte databasegegevensruimte

Wijzig de volgende query om de gebruikte hoeveelheid databasegegevensruimte te retourneren.  Eenheden van het queryresultaat zijn in MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Databasegegevensruimte toegewezen en ongebruikte toegewezen ruimte

Gebruik de volgende query om de toegewezen hoeveelheid databasegegevensruimte en de toegewezen hoeveelheid ongebruikte ruimte terug te geven.  Eenheden van het queryresultaat zijn in MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Maximale grootte van databasegegevens

Wijzig de volgende query om de maximale grootte van de databasegegevens te retourneren.  Eenheden van het queryresultaat bevinden zich in bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Inzicht in typen opslagruimte voor een elastische groep

Inzicht in de volgende opslagruimtehoeveelheden zijn belangrijk voor het beheren van de bestandsruimte van een elastische groep.

|Elastische poolhoeveelheid|Definitie|Opmerkingen|
|---|---|---|
|**Gebruikte gegevensruimte**|De optelling van de gegevensruimte die door alle databases in de elastische groep wordt gebruikt.||
|**Toegewezen gegevensruimte**|De optelling van de gegevensruimte die door alle databases in de elastische groep wordt toegewezen.||
|**Toegewezen gegevensruimte, maar ongebruikt**|Het verschil tussen de toegewezen hoeveelheid gegevensruimte en de gegevensruimte die wordt gebruikt door alle databases in de elastische groep.|Deze hoeveelheid vertegenwoordigt de maximale hoeveelheid ruimte die is toegewezen voor de elastische groep die kan worden teruggewonnen door databasegegevensbestanden te verkleinen.|
|**Maximale grootte van gegevens**|De maximale hoeveelheid gegevensruimte die door de elastische groep kan worden gebruikt voor al zijn databases.|De ruimte die is toegewezen voor het elastische zwembad mag niet groter zijn dan de maximale grootte van het elastische zwembad.  Als deze voorwaarde optreedt, kan de toegewezen ruimte die niet wordt gebruikt, worden teruggewonnen door databasegegevensbestanden te verkleinen.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Een elastische groep opvragen voor informatie over opslagruimte

De volgende query's kunnen worden gebruikt om de hoeveelheden opslagruimte voor een elastische groep te bepalen.  

### <a name="elastic-pool-data-space-used"></a>Gebruikte gegevensruimte voor elastische pool

Wijzig de volgende query om de gebruikte hoeveelheid elastische poolgegevensruimte terug te geven.  Eenheden van het queryresultaat zijn in MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Toegewezen en ongebruikte toegewezen ruimte voor elastische poolgegevens

Wijzig de volgende voorbeelden om een tabel weer te geven met de toegewezen en niet-gebruikte toegewezen ruimte voor elke database in een elastische groep. De tabel beveelt databases uit die databases met de grootste hoeveelheid ongebruikte toegewezen ruimte naar de minste hoeveelheid ongebruikte toegewezen ruimte.  Eenheden van het queryresultaat zijn in MB.  

De queryresultaten voor het bepalen van de ruimte die voor elke database in de groep is toegewezen, kunnen worden bijelkaar opgeteld om de totale ruimte te bepalen die is toegewezen aan de elastische groep. De toegewezen elastische zwembadruimte mag de maximale grootte van het elastische zwembad niet overschrijden.  

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

Het PowerShell-script vereist SQL Server PowerShell-module – zie [PowerShell-module downloaden](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) om te installeren.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

De volgende schermafbeelding is een voorbeeld van de uitvoer van het script:

![elastische pool toegewezen ruimte en ongebruikte toegewezen ruimte voorbeeld](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maximale grootte van elastische poolgegevens

Wijzig de volgende T-SQL-query om de maximale grootte van de elastische groepgegevens te retourneren.  Eenheden van het queryresultaat zijn in MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Ongebruikte toegewezen ruimte terugwinnen

> [!NOTE]
> Deze opdracht kan van invloed zijn op de prestaties van de database tijdens het uitvoeren en moet, indien mogelijk, worden uitgevoerd tijdens perioden van weinig gebruik.

### <a name="dbcc-shrink"></a>DBCC krimpen

Zodra databases zijn geïdentificeerd voor het terugwinnen van niet-gebruikte toegewezen ruimte, wijzigt u de naam van de database in de volgende opdracht om de gegevensbestanden voor elke database te verkleinen.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Deze opdracht kan van invloed zijn op de prestaties van de database tijdens het uitvoeren en moet, indien mogelijk, worden uitgevoerd tijdens perioden van weinig gebruik.  

Zie [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)voor meer informatie over deze opdracht.

### <a name="auto-shrink"></a>Automatisch krimpen

Als alternatief kan automatisch krimpen worden ingeschakeld voor een database.  Auto shrink vermindert de complexiteit van bestandsbeheer en `SHRINKDATABASE` heeft `SHRINKFILE`minder impact op de prestaties van de database dan of .  Automatisch krimpen kan vooral handig zijn voor het beheren van elastische pools met veel databases.  Autokrimp kan echter minder effectief zijn in `SHRINKDATABASE` `SHRINKFILE`het terugvorderen van bestandsruimte dan en .
Als u automatisch verkleinen wilt inschakelen, wijzigt u de naam van de database in de volgende opdracht.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Zie [OPTIES voor databaseset](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) voor meer informatie over deze opdracht.

### <a name="rebuild-indexes"></a>Indexen opnieuw opbouwen

Nadat databasegegevensbestanden zijn gekrompen, kunnen indexen gefragmenteerd raken en hun effectiviteit van prestatieoptimalisatie verliezen. Als prestatiedegradatie optreedt, u overwegen database-indexen opnieuw op te bouwen. Zie [Indexen reorganiseren en opnieuw opbouwen voor](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)meer informatie over fragmentatie en wederopbouw.

## <a name="next-steps"></a>Volgende stappen

- Zie voor informatie over de maximale grootte van de database:
  - [Azure SQL Database vCore-gebaseerde inkoopmodellimieten voor één database](sql-database-vcore-resource-limits-single-databases.md)
  - [Resourcelimieten voor afzonderlijke databases met behulp van het op DTU gebaseerde inkoopmodel](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database vCore-gebaseerde inkoopmodellimieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Resourceslimieten voor elastische pools met behulp van het op DTU gebaseerde inkoopmodel](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) `SHRINKDATABASE` voor meer informatie over de opdracht.
- Zie [Indexen reorganiseren en opnieuw opbouwen voor](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)meer informatie over fragmentatie en wederopbouw.
