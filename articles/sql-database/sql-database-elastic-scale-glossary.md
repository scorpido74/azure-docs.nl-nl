---
title: Woordenlijst met elastische databasegereedschappen
description: Uitleg van termen die worden gebruikt voor elastische databasetools
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823631"
---
# <a name="elastic-database-tools-glossary"></a>Woordenlijst met elastische databasegereedschappen

De volgende termen zijn gedefinieerd voor de [tools voor Elastic Database](sql-database-elastic-scale-introduction.md), een functie van Azure SQL Database. De hulpprogramma's worden gebruikt voor het beheren [van shardkaarten](sql-database-elastic-scale-shard-map-management.md)en omvatten de [clientbibliotheek,](sql-database-elastic-database-client-library.md)het [gereedschap splitsen samenvoegen,](sql-database-elastic-scale-overview-split-and-merge.md) [elastische pools](sql-database-elastic-pool.md)en [query's](sql-database-elastic-query-overview.md). 

Deze termen worden gebruikt bij [het toevoegen van een scherf met behulp van Elastic Database-gereedschappen](sql-database-elastic-scale-add-a-shard.md) en het gebruik van de klasse [RecoveryManager om problemen met de shardkaart op te lossen.](sql-database-elastic-database-recovery-manager.md)

![Elastische schaaltermen][1]

**Database:** een Azure SQL-database. 

**Gegevensafhankelijke routering:** de functionaliteit waarmee een toepassing verbinding kan maken met een shard gegeven door een specifieke shardingsleutel. Zie [Routebeschrijving sme.nl afhankelijk](sql-database-elastic-scale-data-dependent-routing.md)van gegevens . Vergelijk met **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)**.

**Globale shardkaart**: De kaart tussen shardingsleutels en hun respectieve scherven binnen een **shardreeks.** De globale shardkaart wordt opgeslagen in de **shardmapmanager.** Vergelijk met **de lokale shardkaart.**

**Lijst shard kaart**: Een shard kaart waarin sharding toetsen individueel worden toegewezen. Vergelijk met **Range Shard Map**.   

**Lokale scherfkaart**: Opgeslagen op een scherf, bevat de lokale shardkaart toewijzingen voor de shardlets die zich op de scherf bevinden.

**Multi-shard query:** de mogelijkheid om een query uit te geven tegen meerdere scherven; resultatensets worden geretourneerd met union all-semantiek (ook wel 'fan-outquery' genoemd). Vergelijk met **routebepaling van gegevensafhankelijk**.

**Multi-tenant** en **single-tenant:** dit toont een single-tenant database en een multi-tenant database:

![Databases met één en meerdere tenant](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Hier is een weergave van **geshard** single en multi-tenant databases. 

![Databases met één en meerdere tenant](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**De shardkaart**van het bereik : Een scherfkaart waarin de sharddistributiestrategie op veelvoudige waaiers van aaneengesloten waarden wordt gebaseerd. 

**Referentietabellen:** tabellen die niet zijn gehard, maar die worden gerepliceerd over shards. Postcodes kunnen bijvoorbeeld worden opgeslagen in een referentietabel. 

**Shard:** een Azure SQL-database die gegevens opslaat van een geshard gegevensset. 

**Shardelasticiteit**: De mogelijkheid om zowel **horizontale schaling** als **verticale schaling**uit te voeren.

**Geshardtabellen**: tabellen die zijn geshard, d.w.z. waarvan de gegevens over shards worden verdeeld op basis van de kernwaarden voor sharding. 

**Sharding-toets:** een kolomwaarde die bepaalt hoe gegevens over shards worden verdeeld. Het waardetype kan een van de volgende zijn: **int,** **bigint,** **varbinary**, of **uniqueidentifier**. 

**Shard set**: De verzameling van scherven die worden toegeschreven aan dezelfde shard kaart in de shard map manager.  

**Shardlet**: Alle gegevens die zijn gekoppeld aan een enkele waarde van een shardingssleutel op een shard. Een shardlet is de kleinste eenheid van gegevensverplaatsing mogelijk bij het herverdelen van gehard tabellen. 

**Shard kaart**: De set van mappings tussen sharding toetsen en hun respectieve scherven.

**Shardmapmanager**: een beheerobject en gegevensarchief dat de shardmap(s), shardlocaties en toewijzingen voor een of meer shardsets bevat.

![Toewijzingen][2]

## <a name="verbs"></a>Termen
**Horizontale schaling**: De handeling van het uitschalen (of in) van een verzameling scherven door het toevoegen of verwijderen van scherven aan een shardkaart, zoals hieronder weergegeven.

![Horizontaal en verticaal schalen][3]

**Samenvoegen**: De handeling van het verplaatsen van shardlets van twee scherven naar een scherf en het bijwerken van de shard kaart dienovereenkomstig.

**Shardlet move**: De handeling van het verplaatsen van een enkele shardlet naar een andere scherf. 

**Shard**: De handeling van het horizontaal partitioneren van identiek gestructureerde gegevens over meerdere databases op basis van een sharding sleutel.

**Split**: De handeling van het verplaatsen van verschillende shardlets van de ene scherf naar de andere (meestal nieuwe) scherf. Een sharding sleutel wordt geleverd door de gebruiker als het split point.

**Verticale schaling:** de handeling van het opschalen (of omlaag) van de rekengrootte van een afzonderlijke shard. Bijvoorbeeld het wijzigen van een scherf van Standaard naar Premium (wat resulteert in meer computerbronnen). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

