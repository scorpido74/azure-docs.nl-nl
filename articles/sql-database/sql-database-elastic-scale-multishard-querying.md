---
title: Geshard databases voor query's
description: Voer query's uit over shards met behulp van de elastische databaseclientbibliotheek.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067300"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Multi-shard query's met behulp van elastische databasetools

## <a name="overview"></a>Overzicht

Met de [tools Elastic Database](sql-database-elastic-scale-introduction.md)u geshard databaseoplossingen maken. **Multi-shard query's** worden gebruikt voor taken zoals het verzamelen/rapporteren van gegevens waarvoor een query moet worden uitgevoerd die zich uitstrekt over verschillende shards. (Contrast dit met [gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md), die alle werkzaamheden op één enkele shard uitvoert.)

1. Ontvang een **RangeShardMap** ([Java,](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap) [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) of **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) met behulp van de **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), de **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), of de **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) methode. Zie [Een ShardMapManager maken](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) en een [RangeShardMap of ListShardMap opmaken](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Maak een **object MultiShardConnection** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)
3. Een **MultiShardStatement of MultiShardCommand** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET) maken.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)
4. Stel de **eigenschap CommandText** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)in op een opdracht T-SQL.
5. Voer de opdracht uit door de methode **ExecuteQueryAsync of ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)aan te roepen.
6. Bekijk de resultaten met de klasse **MultiShardResultSet of MultiShardDataReader** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)

## <a name="example"></a>Voorbeeld

De volgende code illustreert het gebruik van multi-shard query's met behulp van een bepaalde **ShardMap** genaamd *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Een belangrijk verschil is de aanleg van multi-shard verbindingen. Waar **SqlConnection** werkt op een individuele database, neemt de **MultiShardConnection** een ***verzameling scherven*** als invoer. Bevolk de verzameling scherven van een shardkaart. De query wordt vervolgens uitgevoerd op het verzamelen van scherven met behulp van **UNION ALL** semantiek om een enkel totaalresultaat samen te stellen. Optioneel kan de naam van de scherf waar de rij vandaan komt aan de uitvoer worden toegevoegd met behulp van de eigenschap **ExecutionOptions** op opdracht.

Let op de oproep naar **myShardMap.GetShards()**. Deze methode haalt alle scherven uit de shardkaart en biedt een eenvoudige manier om een query uit te voeren in alle relevante databases. De verzameling scherven voor een multi-shardquery kan verder worden verfijnd door een LINQ-query uit te voeren over de verzameling die is geretourneerd van de oproep naar **myShardMap.GetShards()**. In combinatie met het beleid voor gedeeltelijke resultaten is de huidige mogelijkheid voor multi-shardquery's ontworpen om goed te werken voor tientallen tot honderden scherven.

Een beperking met multi-shard query's is momenteel het ontbreken van validatie voor shards en shardlets die worden opgevraagd. Terwijl gegevensafhankelijke routering controleert of een bepaalde shard deel uitmaakt van de shardkaart op het moment van query's, voeren multishardquery's deze controle niet uit. Dit kan leiden tot multi-shard query's die worden uitgevoerd op databases die zijn verwijderd uit de shardkaart.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Multi-shardquery's en gesplitste samenvoegbewerkingen

Multi-shardquery's controleren niet of shardlets in de opgevraagde database deelnemen aan lopende split-merge-bewerkingen. (Zie [Schalen met het gereedschap Gesplitst samenvoegen van elastic database](sql-database-elastic-scale-overview-split-and-merge.md).) Dit kan leiden tot inconsistenties waarbij rijen van dezelfde shardlet worden weergegeven voor meerdere databases in dezelfde multi-shardquery. Wees u bewust van deze beperkingen en overweeg lopende split-merge-bewerkingen en wijzigingen in de shardmap af te voeren tijdens het uitvoeren van multi-shardquery's.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]