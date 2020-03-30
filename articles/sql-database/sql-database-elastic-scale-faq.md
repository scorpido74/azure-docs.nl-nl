---
title: Veelgestelde vragen over elastische schalen
description: Veelgestelde vragen over Azure SQL Database Elastic Scale.
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
ms.openlocfilehash: 3eedfb1e9ec59fbe12ee94a65d3702a7ef8ca95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823642"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Veelgestelde vragen over elastische databasetools (FAQ)

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Als ik een single-tenant per shard en geen sharding sleutel, hoe vul ik de sharding sleutel voor het schema info

Het object schemainfo wordt alleen gebruikt voor het splitsen van samenvoegscenario's. Als een toepassing inherent één tenant is, is het gereedschap Samenvoegen niet nodig en hoeft u het object schemainfo dus niet in te vullen.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Ik heb een database ingericht en ik heb al een Shard Map Manager, hoe registreer ik deze nieuwe database als een scherf

Zie [Een shard toevoegen aan een toepassing met behulp van de elastische databaseclientbibliotheek](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Hoeveel kosten elastische databasetools

Het gebruik van de elastische databaseclientbibliotheek brengt geen kosten met zich mee. Kosten worden alleen gegenereerd voor de Azure SQL-databases die u gebruikt voor shards en shardmapbeheer, evenals de web-/werknemerrollen die u indient voor het gereedschap Samenvoeging splitsen.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Waarom werken mijn referenties niet als ik een scherf van een andere server toevoeg

Gebruik geen referenties in de vorm vanusername@servername"User ID= ", in plaats daarvan gewoon gebruik maken van "Gebruikersnaam = gebruikersnaam".  Zorg er ook voor dat de "gebruikersnaam" login machtigingen op de scherf heeft.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Moet ik een Shard Map Manager maken en shards vullen elke keer dat ik mijn toepassingen start

Nee: het maken van de Shard Map Manager (bijvoorbeeld [ShardMapManagerFactory.CreateSqlShardMapManager)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)is een eenmalige bewerking.  Uw toepassing moet de aanroep [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) gebruiken bij het opstarten van de toepassing.  Er mag slechts één dergelijke oproep per toepassingsdomein worden aangenomen.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Ik heb vragen over het gebruik van elastische database tools, hoe krijg ik ze beantwoord

Neem contact met ons op op de [SQL Database forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Als ik een databaseverbinding krijg met behulp van een shardingssleutel, kan ik nog steeds gegevens opvragen voor andere shardingsleutels op dezelfde shard.  Is dit door het ontwerp

De Elastic Scale API's geven u een verbinding met de juiste database voor uw shardingsleutel, maar bieden geen sharding key filtering.  Voeg **WAAR-clausules** toe aan uw query om het bereik te beperken tot de meegeleverde shardingssleutel, indien nodig.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Kan ik een andere SQL Database-editie gebruiken voor elke shard in mijn shardset

Ja, een scherf is een individuele database, en dus een scherf kan een Premium-editie, terwijl een ander een Standard-editie. Verder kan de editie van een scherf meerdere keren omhoog of omlaag schalen tijdens de levensduur van de scherf.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Bevat het gereedschap Samenvoegen een database tijdens een gesplitste of samenvoegbewerking

Nee. Voor **gesplitste** bewerkingen moet de doeldatabase bestaan met het juiste schema en worden geregistreerd bij shardkaartbeheer.  Voor **samenvoegbewerkingen** moet u de scherf verwijderen uit de beheerbeheer voor de shardkaart en vervolgens de database verwijderen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]