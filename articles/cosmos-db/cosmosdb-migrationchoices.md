---
title: Cosmos DB migratie opties
description: In dit document worden de verschillende opties beschreven voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882392"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opties voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB

U kunt gegevens uit verschillende gegevens bronnen laden naar Azure Cosmos DB. Omdat Azure Cosmos DB meerdere Api's ondersteunt, kunnen de doelen bovendien een van de bestaande Api's zijn. Voor het ondersteunen van migratie paden van de verschillende bronnen naar de verschillende Azure Cosmos DB Api's, zijn er meerdere oplossingen die gespecialiseerde verwerking bieden voor elk migratie traject. In dit document vindt u een lijst met de beschik bare oplossingen en worden de voor delen en beperkingen beschreven.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Factoren die van invloed zijn op de keuze van het migratie hulpprogramma

De volgende factoren bepalen de keuze van het migratie programma:
* **Online versus offline migratie**: veel hulpprogram ma's voor migratie bieden een pad voor het uitvoeren van een eenmalige migratie. Dit betekent dat de toepassingen die toegang hebben tot de Data Base een periode van downtime kunnen ondervinden. Sommige migratie oplossingen bieden een manier om een Livemigratie uit te voeren waarbij een replicatie pijplijn is ingesteld tussen de bron en het doel.

* **Gegevens bron**: de bestaande gegevens kunnen zich in verschillende gegevens bronnen bevinden, zoals Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, enzovoort. De gegevens kunnen zich ook in een bestaand Azure Cosmos DB account bevinden en het doel van de migratie kan het gegevens model wijzigen of de gegevens in een container opnieuw partitioneren met een andere partitie sleutel.

* **Azure Cosmos DB-API**: voor de SQL-api in azure Cosmos DB bestaan er diverse hulpprogram ma's die zijn ontwikkeld door het Azure Cosmos DB team en die ondersteuning bieden voor de verschillende migratie scenario's. Alle andere Api's hebben hun eigen gespecialiseerde set hulpprogram ma's die door de community zijn ontwikkeld en onderhouden. Omdat Azure Cosmos DB deze Api's ondersteunt op een niveau van een kabel protocol, moeten deze hulpprogram ma's werken zoals bij het migreren van gegevens naar Azure Cosmos DB. Het is echter mogelijk dat er aangepaste verwerking vereist is, omdat dit concept specifiek is voor Azure Cosmos DB.

* **Grootte van gegevens**: de meeste migratie hulpprogramma's werken zeer goed voor kleinere gegevens sets. Wanneer de gegevensset meer dan honderd GB overschrijdt, zijn de opties voor migratie hulpprogramma's beperkt. 

* **Verwachte migratie duur**: migraties kunnen worden geconfigureerd om te worden uitgevoerd met een traag, incrementeel tempo dat minder door Voer verbruikt of de volledige door Voer die is ingericht op de doel Azure Cosmos DB container verbruikt en de migratie in minder tegelijk.

## <a name="azure-cosmos-db-sql-api"></a>SQL-API voor Azure Cosmos DB
|**Type migratie**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Breken|[Hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull; is niet geschikt voor grote gegevens sets|
|Breken|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull; maakt gebruik van de Azure Cosmos DB bulk-uitvoerder bibliotheek <br/>&bull; geschikt voor grote gegevens sets <br/>&bull; geen controle punt meer: Dit betekent dat als er een probleem optreedt tijdens de migratie, het hele migratie proces opnieuw moet worden gestart<br/>&bull; geen wachtrij met onbestelbare berichten. Dit betekent dat een aantal foutieve bestanden het gehele migratie proces kan stoppen.|
|Breken|[Azure Cosmos DB Spark-connector](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; maakt gebruik van de Azure Cosmos DB bulk-uitvoerder bibliotheek <br/>&bull; geschikt voor grote gegevens sets <br/>&bull; moet een aangepaste Spark-installatie hebben <br/>&bull; Spark is gevoelig voor inconsistenties in het schema en dit kan een probleem zijn tijdens de migratie |
|Breken|[Aangepast hulp programma met Cosmos DB bibliotheek voor bulksgewijs uitvoerder](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; biedt controle punten, functies voor onbestelbare berichten die de migratie tolerantie verhogen <br/>&bull; geschikt voor zeer grote gegevens sets (10 TB +)  <br/>voor &bull; moet aangepaste installatie van dit hulp programma worden uitgevoerd als een App Service |
|Online|[Cosmos DB functions + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; eenvoudig in te stellen <br/>&bull; werkt alleen als de bron een Azure Cosmos DB container is <br/>&bull; is niet geschikt voor grote gegevens sets <br/>&bull; legt geen verwijderingen vast van de bron container |
|Online|[Aangepaste migratie service met ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull; biedt voortgangs tracering <br/>&bull; werkt alleen als de bron een Azure Cosmos DB container is <br/>&bull; werkt ook voor grotere gegevens sets <br/>&bull; vereist dat de gebruiker een App Service instelt om de Change feed-processor te hosten <br/>&bull; legt geen verwijderingen vast van de bron container|
|Online|[Realtimeplatform](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; werkt met een groot aantal verschillende bronnen, zoals Oracle, DB2 SQL Server <br/>&bull; eenvoudig ETL-pijp lijnen bouwen en een dash board voor bewaking bieden <br/>&bull; ondersteunt grotere gegevens sets <br/>&bull; omdat dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo-API
|**Type migratie**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Breken|[Hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull; is niet geschikt voor grote gegevens sets|
|Breken|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull; maakt gebruik van de Azure Cosmos DB bulk-uitvoerder bibliotheek <br/>&bull; geschikt voor grote gegevens sets <br/>&bull; zonder controle punt houdt in dat er tijdens de migratie een probleem is met het opnieuw opstarten van het hele migratie proces.<br/>&bull; geen wachtrij met onbestelbare berichten zou betekenen dat een aantal foutieve bestanden het hele migratie proces zou kunnen stoppen <br/>&bull; heeft aangepaste code nodig om de Lees doorvoer voor bepaalde gegevens bronnen te verg Roten|
|Breken|[Bestaande Mongo-Hulpprogram Ma's (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; eenvoudig in te stellen en te integreren <br/>&bull; moet aangepaste verwerking hebben voor gashendel|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; maakt gebruik van de Azure Cosmos DB bulk-uitvoerder bibliotheek <br/>&bull; geschikt voor grote gegevens sets en zorgt voor het repliceren van Live wijzigingen <br/>&bull; werkt alleen met andere MongoDB-bronnen|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra-API
|**Type migratie**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Breken|[cqlsh kopiëren opdracht](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; eenvoudig in te stellen <br/>&bull; is niet geschikt voor grote gegevens sets <br/>&bull; werkt alleen wanneer de bron een Cassandra-tabel is|
|Breken|[Tabel kopiëren met Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; kan gebruikmaken van Spark-mogelijkheden om trans formatie en opname te parallelliseren <br/>&bull; moet worden geconfigureerd met een aangepast beleid voor opnieuw proberen om beperking te verwerken|
|Online|[Realtimeplatform (uit Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; werkt met een groot aantal verschillende bronnen, zoals Oracle, DB2 SQL Server <br/>&bull; eenvoudig ETL-pijp lijnen bouwen en een dash board voor bewaking bieden <br/>&bull; ondersteunt grotere gegevens sets <br/>&bull; omdat dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|
|Online|[Blitzz (uit Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; ondersteunt grotere gegevens sets <br/>&bull; omdat dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|

## <a name="other-apis"></a>Andere Api's
Voor andere Api's dan de SQL-API, Mongo-API en het Cassandra-API, zijn er verschillende hulpprogram ma's die worden ondersteund door elk van de bestaande ecosystemen van de API. 

**Tabel-API** 
* [Hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin-API**
* [Bibliotheek voor bulksgewijs uitvoeren grafiek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het uitproberen van de voorbeeld toepassingen die de bulk-uitvoerder bibliotheek in [.net](bulk-executor-dot-net.md) en [Java](bulk-executor-java.md)gebruiken. 
* De bibliotheek bulk-uitvoerder is geïntegreerd in de Cosmos DB Spark-connector, Zie [Azure Cosmos DB artikel Spark-connector](spark-connector.md) voor meer informatie.  
* Neem contact op met het product team van Azure Cosmos DB door een ondersteunings ticket te openen onder het probleem type ' algemeen advies ' en ' grote (TB +) ' subtype ' voor meer informatie over grootschalige migraties.
