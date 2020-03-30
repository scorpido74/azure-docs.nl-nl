---
title: Bestaande databases migreren om uit te schalen | Microsoft Documenten
description: Gesharddatabases converteren naar elastische databasetools door een shardmapmanager te maken
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c776f4ac09626f0abd1eb754cde391a1c5447627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421220"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Bestaande databases migreren om uit te schalen

Beheer eenvoudig uw bestaande geshard databases met Azure SQL Database-databasetools (zoals de [elastic database-clientbibliotheek).](sql-database-elastic-database-client-library.md) Converteer eerst een bestaande set databases om de [shardmapmanager](sql-database-elastic-scale-shard-map-management.md)te gebruiken.

## <a name="overview"></a>Overzicht

Ga als u een bestaande geshard database migreert:

1. Bereid de [database voor shardmapbeheer voor.](sql-database-elastic-scale-shard-map-management.md)
2. Maak de shardkaart.
3. Bereid de individuele scherven voor.  
4. Toewijzingen toevoegen aan de shardkaart.

Deze technieken kunnen worden geïmplementeerd met behulp van de [.NET Framework-clientbibliotheek](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)of de PowerShell-scripts die zijn gevonden bij [Azure SQL DB - Elastic Database-scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). De voorbeelden hier maken gebruik van de PowerShell scripts.

Zie [Shard mapmanagement](sql-database-elastic-scale-shard-map-management.md)voor meer informatie over de ShardMapManager. Zie Overzicht van elastic [database-functies](sql-database-elastic-scale-introduction.md)voor een overzicht van de elastische database.

## <a name="prepare-the-shard-map-manager-database"></a>De database voor shardmapbeheer voorbereiden

De shard map manager is een speciale database die de gegevens bevat om geschaalde databases te beheren. U een bestaande database gebruiken of een nieuwe database maken. Een database die fungeert als shardmapbeheerder mag niet dezelfde database zijn als een scherf. Het PowerShell-script maakt de database niet voor u.

## <a name="step-1-create-a-shard-map-manager"></a>Stap 1: maak een shard map manager

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>De beheermanager van de shardkaart ophalen

Na het maken u de shardmapmanager ophalen met deze cmdlet. Deze stap is nodig elke keer dat u het shardmapmanager-object moet gebruiken.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Stap 2: maak de shardkaart

Selecteer het type shardkaart dat u wilt maken. De keuze is afhankelijk van de databasearchitectuur:

1. Eén tenant per database (Zie de [woordenlijst](sql-database-elastic-scale-glossary.md)voor termen .)
2. Meerdere tenants per database (twee typen):
   1. Lijsttoewijzing
   2. Bereiktoewijzing

Maak voor een model met één tenant een **shardkaart voor lijsttoewijzing.** Het model met één tenant wijst één database per tenant toe. Dit is een effectief model voor SaaS-ontwikkelaars omdat het het beheer vereenvoudigt.

![Lijsttoewijzing][1]

Het multi-tenantmodel wijst meerdere tenants toe aan een afzonderlijke database (en u groepen tenants over meerdere databases distribueren). Gebruik dit model wanneer u verwacht dat elke tenant kleine gegevensbehoeften heeft. Wijs in dit model een reeks tenants toe aan een database met **bereiktoewijzing.**

![Bereiktoewijzing][2]

U ook een databasemodel met meerdere tenants implementeren met behulp van een *lijsttoewijzing* om meerdere tenants toe te wijzen aan een afzonderlijke database. DB1 wordt bijvoorbeeld gebruikt om informatie over tenant-ID 1 en 5 op te slaan en DB2 slaat gegevens op voor tenant 7 en tenant 10.

![Meerdere tenants op single DB][3]

**Kies op basis van uw keuze een van de volgende opties:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Optie 1: een shardkaart maken voor een lijsttoewijzing

Maak een shardkaart met het object ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Optie 2: een shardkaart maken voor een bereiktoewijzing

Om dit toewijzingspatroon te gebruiken, moeten tenant-id-waarden continu bereiken zijn en is het aanvaardbaar dat er een gat in de bereiken wordt gevonden door het bereik over te slaan bij het maken van de databases.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Optie 3: Toewijzingen in een afzonderlijke database weergeven

Het instellen van dit patroon vereist ook het maken van een lijstkaart zoals weergegeven in stap 2, optie 1.

## <a name="step-3-prepare-individual-shards"></a>Stap 3: Individuele scherven voorbereiden

Voeg elke scherf (database) toe aan de shardmapmanager. Dit bereidt de afzonderlijke databases voor het opslaan van kaartinformatie. Voer deze methode uit op elke shard.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Stap 4: Toewijzingen toevoegen

De toevoeging van mappings is afhankelijk van het soort shard kaart die u hebt gemaakt. Als u een lijstkaart hebt gemaakt, voegt u lijsttoewijzingen toe. Als u een bereikkaart hebt gemaakt, voegt u bereiktoewijzingen toe.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Optie 1: de gegevens toewijzen voor een lijsttoewijzing

Breng de gegevens in kaart door voor elke tenant een lijsttoewijzing toe te voegen.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Optie 2: de gegevens toewijzen voor een bereiktoewijzing

Voeg de bereiktoewijzingen toe voor alle tenant-id-reeks - databasekoppelingen:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Stap 4 optie 3: breng de gegevens voor meerdere tenants in een afzonderlijke database in kaart

Voer voor elke tenant de Add-ListMapping uit (optie 1).

## <a name="checking-the-mappings"></a>De toewijzingen controleren

U informatie over de bestaande shards en de bijbehorende toewijzingen opvragen met behulp van volgende opdrachten:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Samenvatting

Zodra u de installatie hebt voltooid, u beginnen met het gebruik van de elastic database-clientbibliotheek. U ook [gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md) en [multishardquery's](sql-database-elastic-scale-multishard-querying.md)gebruiken.

## <a name="next-steps"></a>Volgende stappen

Haal de PowerShell-scripts uit [Azure SQL DB-Elastic Database-scripts.](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)

De tools staan ook op GitHub: [Azure/elastic-db-tools.](https://github.com/Azure/elastic-db-tools)

Gebruik het gereedschap splitsen om gegevens naar of van een multitenantmodel naar één tenantmodel te verplaatsen. Zie [Gereedschap Samenvoegen splitsen](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Aanvullende bronnen

Zie voor informatie over algemene gegevensarchitectuurpatronen van multitenant software as a service (SaaS)-databasetoepassingen, [Ontwerppatronen voor multitenant SaaS-toepassingen met Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Vragen en functieverzoeken

Voor vragen gebruikt u het [SQL Database-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) en voor functieaanvragen, voegt u deze toe aan het [SQL Database-feedbackforum.](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
