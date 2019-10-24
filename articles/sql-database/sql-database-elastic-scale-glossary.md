---
title: Woorden lijst voor Elastic Database hulpprogram ma's | Microsoft Docs
description: Uitleg van de termen die worden gebruikt voor Elastic data base-hulpprogram ma's
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
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568457"
---
# <a name="elastic-database-tools-glossary"></a>Woorden lijst voor Elastic Database-hulpprogram ma's

De volgende termen worden gedefinieerd voor de [Elastic database-hulpprogram ma's](sql-database-elastic-scale-introduction.md), een functie van Azure SQL database. De hulpprogram ma's worden gebruikt voor het beheren van [Shard Maps](sql-database-elastic-scale-shard-map-management.md), en het toevoegen van de [client bibliotheek](sql-database-elastic-database-client-library.md), het [hulp programma voor splitsen en samen voegen](sql-database-elastic-scale-overview-split-and-merge.md), [elastische Pools](sql-database-elastic-pool.md)en [query's](sql-database-elastic-query-overview.md). 

Deze voor waarden worden gebruikt in het [toevoegen van een Shard met Elastic database-hulpprogram ma's](sql-database-elastic-scale-add-a-shard.md) en [het gebruik van de klasse toewijzingen oplossen om problemen met de Shard-kaart op te lossen](sql-database-elastic-database-recovery-manager.md)

![Termen voor Elastic Scale][1]

**Data Base**: Een Azure SQL-database. 

**Gegevens afhankelijke route ring**: De functionaliteit waarmee een toepassing verbinding kan maken met een Shard op basis van een specifieke sharding-sleutel. Zie [gegevens afhankelijke route ring](sql-database-elastic-scale-data-dependent-routing.md). Vergelijk met een **[multi-Shard-query](sql-database-elastic-scale-multishard-querying.md)** .

**Globale Shard toewijzing**: De kaart tussen sharding sleutels en hun respectieve Shards binnen een **Shard-set**. De globale Shard-toewijzing wordt opgeslagen in **Shard-toewijzings beheer**. Vergelijken met de **lokale Shard-toewijzing**.

**Lijst met Shard-toewijzing**: Een Shard-toewijzing waarin sharding sleutels afzonderlijk worden toegewezen. Vergelijkt met de **Shard-kaart**.   

**Lokale Shard-toewijzing**: De lokale Shard-toewijzing die is opgeslagen op een Shard, bevat toewijzingen voor de shardlets die zich op de Shard bevinden.

**Multi-Shard query**: De mogelijkheid om een query uit te geven op meerdere Shards; resultaten sets worden geretourneerd met behulp van UNION alle semantiek (ook wel ' ventilator-out-query ' genoemd). Vergelijkt met **gegevens afhankelijke route ring**.

**Multi tenant** en **single-tenant**: Hier ziet u een Data Base met één Tenant en een Data Base met meerdere tenants:

![Single-en multi tenant-data bases](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Hier volgt een representatie van **Shard** single-en multi tenant-data bases. 

![Single-en multi tenant-data bases](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Toewijzing van bereik Shard**: Een Shard-kaart waarin de Shard-distributie strategie is gebaseerd op meerdere bereiken aaneengesloten waarden. 

**Referentie tabellen**: Tabellen die niet worden Shard, maar worden gerepliceerd tussen Shards. Post codes kunnen bijvoorbeeld worden opgeslagen in een verwijzings tabel. 

**Shard**: Een Azure-SQL database die gegevens uit een Shard-gegevensset opslaat. 

**Shard-elasticiteit**: De mogelijkheid om **horizon taal schalen** en **verticaal schalen**uit te voeren.

**Shard-tabellen**: Tabellen die Shard zijn, dat wil zeggen, waarbij gegevens worden verdeeld over Shards op basis van hun sharding-sleutel waarden. 

**Sharding-sleutel**: Een kolom waarde die bepaalt hoe gegevens worden gedistribueerd over Shards. Het waardetype kan een van de volgende waarden hebben: **int**, **bigint**, **varbinary**of **uniqueidentifier**. 

**Shard ingesteld**: De verzameling van Shards die worden toegeschreven aan dezelfde Shard-toewijzing in het Shard-toewijzings beheer.  

**Shardlet**: Alle gegevens die zijn gekoppeld aan één waarde van een sharding-sleutel in een Shard. Een shardlet is de kleinste eenheid van gegevens verplaatsing tijdens het opnieuw distribueren van Shard-tabellen. 

**Shard-kaart**: De set met toewijzingen tussen sharding-sleutels en hun respectievelijke Shards.

**Shard-toewijzings beheer**: Een beheer object en gegevens opslag dat de Shard-kaart (en), Shard locaties en toewijzingen bevat voor een of meer Shard sets.

![Toewijzingen][2]

## <a name="verbs"></a>Termen
**Horizon taal schalen**: De handeling van het uitschalen (of in) een verzameling Shards door Shards toe te voegen aan of te verwijderen uit een Shard-kaart, zoals hieronder wordt weer gegeven.

![Horizon taal en verticaal schalen][3]

**Samen voegen**: De handeling van het verplaatsen van shardlets van twee Shards naar een Shard en het bijwerken van de Shard-kaart dienovereenkomstig.

**Shardlet verplaatsen**: De handeling waarbij één shardlet naar een andere Shard wordt verplaatst. 

**Shard**: Het Horizon taal partitioneren van identieke gestructureerde gegevens over meerdere data bases op basis van een sharding-sleutel.

**Splitsen**: Het verplaatsen van verschillende shardlets van een Shard naar een andere (doorgaans nieuwe) Shard. Een sharding-sleutel wordt door de gebruiker als het Splits punt verschaft.

**Verticaal schalen**: De reken grootte van een afzonderlijke Shard omhoog (of omlaag) schalen. U kunt bijvoorbeeld een Shard wijzigen van Standard in Premium (wat resulteert in meer computer bronnen). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

