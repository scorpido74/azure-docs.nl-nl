---
title: Schaalbare clouddatabases bouwen
description: Schaal bare .NET Data base-apps bouwen met de client bibliotheek voor Elastic data base
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
ms.openlocfilehash: 0b5b3c924a644c065327db36a6a8d64b4a552d40
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690524"
---
# <a name="building-scalable-cloud-databases"></a>Schaalbare clouddatabases bouwen

Het schalen van data bases kan eenvoudig worden gerealiseerd met schaal bare hulp middelen en functies voor Azure SQL Database. Met name kunt u de Elastic Database- **client bibliotheek** gebruiken om uitgeschaalde data bases te maken en te beheren. Met deze functie kunt u eenvoudig Shard-toepassingen ontwikkelen met honderden, of zelfs duizenden, van Azure SQL-data bases.

Downloaden:

* De Java-versie van de-bibliotheek, Zie [maven Central repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools)(Engelstalig).
* Zie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)voor de .net-versie van de bibliotheek.

## <a name="documentation"></a>Documentatie

1. [Aan de slag met tools voor Elastic Database](sql-database-elastic-scale-get-started.md)
2. [Elastic Database functies](sql-database-elastic-scale-introduction.md)
3. [Shard-toewijzingsbeheer](sql-database-elastic-scale-shard-map-management.md)
4. [Bestaande data bases migreren om uit te schalen](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md)
6. [Query's voor meerdere Shard](sql-database-elastic-scale-multishard-querying.md)
7. [Een Shard toevoegen met behulp van Elastic Database-hulpprogram ma's](sql-database-elastic-scale-add-a-shard.md)
8. [Multi tenant-toepassingen met Elastic data base-hulpprogram ma's en beveiliging op rijniveau](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Client bibliotheek-apps bijwerken](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Overzicht van elastische query's](sql-database-elastic-query-overview.md)
11. [Woordenlijst voor hulpprogramma's voor Elastic Database](sql-database-elastic-scale-glossary.md)
12. [Client bibliotheek Elastic Database met Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Client bibliotheek voor Elastic data base met dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Hulp programma voor splitsen en samen voegen](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Prestatiemeteritems voor shard-toewijzingsbeheer](sql-database-elastic-database-client-library.md) 
16. [Veelgestelde vragen over hulpprogram ma's voor Elastic data base](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Client mogelijkheden

Door toepassingen te schalen met behulp van *sharding* presenteert u uitdagingen voor zowel de ontwikkelaar als de beheerder. De client bibliotheek vereenvoudigt de beheer taken door hulpprogram ma's te bieden waarmee zowel ontwikkel aars als beheerders uitgeschaalde data bases kunnen beheren. In een typisch voor beeld zijn er veel data bases, ook wel ' Shards ', die u kunt beheren. Klanten bevinden zich in dezelfde data base en er is één data base per klant (een schema met één Tenant). De client bibliotheek bevat de volgende functies:

- **Shard-toewijzings beheer**: er wordt een speciale data base met de naam ' Shard-toewijzings beheer ' gemaakt. Shard-toewijzings beheer is de mogelijkheid voor een toepassing om meta gegevens over de Shards te beheren. Ontwikkel aars kunnen deze functie gebruiken om data bases als Shards te registreren, toewijzingen van afzonderlijke sharding sleutels of sleutel bereik te beschrijven naar deze data bases, en deze meta gegevens te behouden als het aantal en de samen stelling van data bases zich ontwikkelen om de capaciteits wijzigingen weer te geven. Zonder de client bibliotheek voor Elastic data base moet u veel tijd best Eden aan het schrijven van de beheer code bij het implementeren van sharding. Zie [Shard-toewijzings beheer](sql-database-elastic-scale-shard-map-management.md)voor meer informatie.

- **Gegevens afhankelijke route ring**: Stel een aanvraag naar de toepassing voor. Op basis van de sharding-sleutel waarde van de aanvraag moet de toepassing de juiste data base bepalen op basis van de sleutel waarde. Vervolgens wordt er een verbinding met de data base geopend om de aanvraag te verwerken. Met gegevens afhankelijke route ring kunt u verbindingen met één eenvoudige aanroep in de Shard-toewijzing van de toepassing openen. Gegevens afhankelijke route ring is een ander gebied met een infrastructuur code die nu wordt gedekt door de functionaliteit van de client bibliotheek voor Elastic data base. Zie [gegevens afhankelijke route ring](sql-database-elastic-scale-data-dependent-routing.md)voor meer informatie.
- **Query's voor meerdere Shard (MSQ)** : query's met meerdere Shard werken wanneer een aanvraag meerdere (of alle) Shards omvat. Een multi-Shard-query voert dezelfde T-SQL-code uit op alle Shards of een set Shards. De resultaten van de deelnemende Shards worden samengevoegd in een algemene resultatenset met behulp van UNION alle semantiek. De functionaliteit die wordt weer gegeven via de client bibliotheek, behandelt veel taken, waaronder: verbindings beheer, thread beheer, fout afhandeling en tussentijdse resultaten verwerking. MSQ kan Maxi maal honderden Shards opvragen. Zie [multi-Shard query's](sql-database-elastic-scale-multishard-querying.md)voor meer informatie.

Over het algemeen kunnen klanten die gebruikmaken van Elastic data base-hulpprogram ma's, de volledige T-SQL-functionaliteit krijgen bij het verzenden van Shard-lokale bewerkingen, in tegens telling tot Shard-bewerkingen die hun eigen semantiek hebben.



## <a name="next-steps"></a>Volgende stappen

- Elastic Database-client bibliotheek ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)): voor het **downloaden** van de bibliotheek.

- Aan de [slag met hulpprogram ma's voor Elastic data base](sql-database-elastic-scale-get-started.md) : om de voor **beeld-app** te proberen die client functies demonstreert.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.net](https://github.com/Azure/elastic-db-tools)): om bijdragen aan de code aan te brengen.
- [Overzicht van elastische](sql-database-elastic-query-overview.md) query's voor Azure SQL database-voor het gebruik van Elastic queries.

- [Gegevens verplaatsen tussen uitgeschaalde Cloud databases](sql-database-elastic-scale-overview-split-and-merge.md) : voor instructies over het gebruik van het **hulp programma voor splitsen en samen voegen**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

