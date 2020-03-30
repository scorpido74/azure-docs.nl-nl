---
title: Schaalbare clouddatabases bouwen
description: Schaalbare .NET-database-apps bouwen met de elastische databaseclientbibliotheek
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: ae26f669ddbe2cc2c5b6e25a9c1c0229e88dc2e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823909"
---
# <a name="building-scalable-cloud-databases"></a>Schaalbare clouddatabases bouwen

Het uitschalen van databases kan eenvoudig worden uitgevoerd met schaalbare hulpprogramma's en functies voor Azure SQL Database. U met name de **clientbibliotheek Elastic Database** gebruiken om geschaalde databases te maken en te beheren. Met deze functie u eenvoudig geshardtoepassingen ontwikkelen met honderden of zelfs duizenden Azure SQL-databases.

Downloaden:

* De Java-versie van de bibliotheek, zie [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* De .NET-versie van de bibliotheek, zie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentatie

1. [Aan de slag met tools voor Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Eigenschappen van elastische database](sql-database-elastic-scale-introduction.md)
3. [Shard-toewijzingsbeheer](sql-database-elastic-scale-shard-map-management.md)
4. [Bestaande databases migreren om uit te schalen](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md)
6. [Multi-shard query's](sql-database-elastic-scale-multishard-querying.md)
7. [Een shard toevoegen met behulp van hulpmiddelen voor elastic database](sql-database-elastic-scale-add-a-shard.md)
8. [Multi-tenant toepassingen met elastische databasetools en beveiliging op rijniveau](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Apps voor clientbibliotheek upgraden](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Overzicht van elastische query's](sql-database-elastic-query-overview.md)
11. [Woordenlijst voor hulpprogramma's voor Elastic Database](sql-database-elastic-scale-glossary.md)
12. [Elastic Database-clientbibliotheek met Entiteitskader](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastische databaseclientbibliotheek met Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Gereedschap Splitsen](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestatiemeteritems voor shard-toewijzingsbeheer](sql-database-elastic-database-client-library.md) 
16. [Veelgestelde vragen over elastische databasehulpprogramma's](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Clientmogelijkheden

Het uitschalen van toepassingen met *sharding* brengt uitdagingen met zich mee voor zowel de ontwikkelaar als de beheerder. De clientbibliotheek vereenvoudigt de beheertaken door tools te bieden waarmee zowel ontwikkelaars als beheerders geschaalde databases kunnen beheren. In een typisch voorbeeld zijn er veel databases, bekend als 'shards', om te beheren. Klanten bevinden zich in dezelfde database en er is één database per klant (een systeem met één tenant). De clientbibliotheek bevat de volgende functies:

- **Shard Map Management**: Een speciale database genaamd de "shard map manager" wordt gemaakt. Shard map management is de mogelijkheid voor een toepassing om metadata over de scherven te beheren. Ontwikkelaars kunnen deze functionaliteit gebruiken om databases te registreren als shards, toewijzingen van individuele shardingsleutels of sleutelbereiken voor die databases te beschrijven en deze metagegevens te onderhouden terwijl het aantal en de samenstelling van databases evolueert om capaciteitswijzigingen weer te geven. Zonder de elastische databaseclientbibliotheek zou u veel tijd moeten besteden aan het schrijven van de beheercode bij het implementeren van sharding. Zie [Shard-kaartbeheer](sql-database-elastic-scale-shard-map-management.md)voor meer informatie.

- **Gegevensafhankelijke routering**: Stel je een aanvraag voor die in de toepassing komt. Op basis van de sharding sleutelwaarde van de aanvraag, moet de toepassing de juiste database bepalen op basis van de sleutelwaarde. Vervolgens wordt een verbinding met de database geopend om de aanvraag te verwerken. Gegevensafhankelijke routering biedt de mogelijkheid om verbindingen te openen met één eenvoudige oproep in de shardkaart van de toepassing. Gegevensafhankelijke routering was een ander gebied van infrastructuurcode dat nu wordt gedekt door functionaliteit in de elastische databaseclientbibliotheek. Zie [Routering voor gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md)voor meer informatie.
- **Multi-shard query's (MSQ)**: Multi-shard querying werkt wanneer een aanvraag meerdere (of alle) shards omvat. Een multi-shardquery voert dezelfde T-SQL-code uit op alle shards of een set shards. De resultaten van de deelnemende scherven worden samengevoegd tot een algemeen resultaat met behulp van UNION ALL semantiek. De functionaliteit zoals die wordt weergegeven in de clientbibliotheek, behandelt vele taken, waaronder: verbindingsbeheer, threadbeheer, foutafhandeling en tussentijdse verwerking van resultaten. MSQ kan tot honderden scherven opvragen. Zie [Multishard query's voor](sql-database-elastic-scale-multishard-querying.md)meer informatie.

In het algemeen kunnen klanten die elastische databasetools gebruiken, verwachten dat ze volledige T-SQL-functionaliteit krijgen bij het indienen van shard-lokale bewerkingen in tegenstelling tot cross-shardbewerkingen die hun eigen semantiek hebben.



## <a name="next-steps"></a>Volgende stappen

- Elastic Database Client Library ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - om de bibliotheek te **downloaden.**

- [Ga aan de slag met elastische databasetools](sql-database-elastic-scale-get-started.md) - om de **voorbeeld-app** te proberen die clientfuncties demonstreert.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - om bijdragen te leveren aan de code.
- [Overzicht van elastische query's in Azure SQL Database](sql-database-elastic-query-overview.md) - om elastische query's te gebruiken.

- [Gegevens verplaatsen tussen geschaalde clouddatabases](sql-database-elastic-scale-overview-split-and-merge.md) - voor instructies over het gebruik van het **hulpprogramma voor gesplitste samenvoegen**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

