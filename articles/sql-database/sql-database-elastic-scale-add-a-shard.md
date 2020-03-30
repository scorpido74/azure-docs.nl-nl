---
title: Een scherf toevoegen met behulp van elastische databasegereedschappen
description: Hoe elastische schaal API's te gebruiken om nieuwe scherven toe te voegen aan een shardset.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823716"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Een shard toevoegen met behulp van hulpmiddelen voor elastic database

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Een scherf toevoegen voor een nieuw bereik of sleutel

Toepassingen moeten vaak nieuwe shards toevoegen om gegevens te verwerken die worden verwacht van nieuwe sleutels of sleutelbereiken, voor een shardkaart die al bestaat. Een toepassing die is geshard door Tenant ID moet bijvoorbeeld een nieuwe shard inrichten voor een nieuwe tenant, of gegevens die maandelijks worden geshard, hebben mogelijk een nieuwe shard nodig die vóór het begin van elke nieuwe maand is ingericht.

Als het nieuwe bereik van de belangrijkste waarden nog geen deel uitmaakt van een bestaande toewijzing, is het eenvoudig om de nieuwe shard toe te voegen en de nieuwe toets of het nieuwe bereik aan die shard te koppelen.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Voorbeeld: een scherf en het bereik ervan toevoegen aan een bestaande shardkaart

In dit voorbeeld wordt gebruik gemaakt van de TryGetShard[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) de CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET-methoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) en wordt een instantie gemaakt van de klasse ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation) In het onderstaande voorbeeld is een database met de naam **sample_shard_2** en alle benodigde schemaobjecten erin gemaakt om bereik vast te houden [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

Voor de .NET-versie u PowerShell ook gebruiken als alternatief om een nieuwe Shard Map Manager te maken. Een voorbeeld is [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)beschikbaar.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Een scherf toevoegen voor een leeg deel van een bestaand bereik

In sommige omstandigheden hebt u een bereik mogelijk al toegewezen aan een scherf en gedeeltelijk gevuld met gegevens, maar u wilt nu dat aankomende gegevens naar een andere shard worden geleid. U shardt bijvoorbeeld per dagbereik en hebt al 50 dagen toegewezen aan een scherf, maar op dag 24 wilt u dat toekomstige gegevens in een andere scherf worden belanden. De elastic database [split-merge tool](sql-database-elastic-scale-overview-split-and-merge.md) kan deze bewerking uitvoeren, maar als gegevensbeweging niet nodig is (bijvoorbeeld gegevens voor het bereik van dagen [25, 50), dat wil zeggen, dag 25 inclusief tot 50 exclusief, bestaat nog niet) u dit volledig rechtstreeks uitvoeren met behulp van de Shard Map Management API's.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Voorbeeld: een bereik splitsen en het lege gedeelte toewijzen aan een nieuw toegevoegde shard

Er is een database met de naam 'sample_shard_2' en alle benodigde schemaobjecten erin gemaakt.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Belangrijk:** Gebruik deze techniek alleen als u zeker weet dat het bereik voor de bijgewerkte toewijzing leeg is.  De voorgaande methoden controleren geen gegevens op het bereik dat wordt verplaatst, dus het is het beste om controles in uw code op te nemen.  Als er rijen zijn in het bereik dat wordt verplaatst, komt de werkelijke gegevensverdeling niet overeen met de bijgewerkte shardkaart. Gebruik het [gereedschap Splitsen samenvoegen](sql-database-elastic-scale-overview-split-and-merge.md) om de bewerking in deze gevallen uit te voeren.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
