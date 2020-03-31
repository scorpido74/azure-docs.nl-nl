---
title: Cosmos DB-migratieopties
description: In dit document worden de verschillende opties beschreven om uw on-premises of cloudgegevens te migreren naar Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72882392"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opties om uw on-premises of cloudgegevens te migreren naar Azure Cosmos DB

U gegevens uit verschillende gegevensbronnen laden naar Azure Cosmos DB. Aangezien Azure Cosmos DB meerdere API's ondersteunt, kunnen de doelen bovendien een van de bestaande API's zijn. Om migratiepaden van de verschillende bronnen naar de verschillende Azure Cosmos DB API's te ondersteunen, zijn er meerdere oplossingen die gespecialiseerde afhandeling bieden voor elk migratiepad. Dit document bevat de beschikbare oplossingen en beschrijft hun voordelen en beperkingen.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Factoren die van invloed zijn op de keuze van het migratieinstrument

De volgende factoren bepalen de keuze van het migratiehulpprogramma:
* **Online versus offline migratie**: Veel migratietools bieden een pad om alleen een eenmalige migratie uit te brengen. Dit betekent dat de toepassingen die toegang hebben tot de database een periode van downtime kunnen ervaren. Sommige migratieoplossingen bieden een manier om een live migratie uit te brengen wanneer er een replicatiepijplijn is ingesteld tussen de bron en het doel.

* **Gegevensbron**: De bestaande gegevens kunnen worden in verschillende gegevensbronnen zoals Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, enz. De gegevens kunnen ook in een bestaand Azure Cosmos DB-account zijn en de bedoeling van migratie kan zijn om het gegevensmodel te wijzigen of de gegevens in een container opnieuw te verdelen met een andere partitiesleutel.

* **Azure Cosmos DB API**: Voor de SQL API in Azure Cosmos DB zijn er verschillende hulpprogramma's ontwikkeld door het Azure Cosmos DB-team die helpen bij de verschillende migratiescenario's. Alle andere API's hebben hun eigen gespecialiseerde set van tools ontwikkeld en onderhouden door de gemeenschap. Aangezien Azure Cosmos DB deze API's op draadprotocolniveau ondersteunt, moeten deze hulpprogramma's ook werken zoals ze gegevens migreren naar Azure Cosmos DB. Ze kunnen echter aangepaste behandeling voor gashendels vereisen, omdat dit concept specifiek is voor Azure Cosmos DB.

* **Grootte van gegevens**: De meeste migratietools werken erg goed voor kleinere gegevenssets. Wanneer de gegevensset een paar honderd gigabyte overschrijdt, zijn de keuzemogelijkheden van migratietools beperkt. 

* **Verwachte migratieduur:** Migraties kunnen worden geconfigureerd om in een langzaam, incrementeel tempo plaats te vinden dat minder doorvoer verbruikt of de volledige doorvoer kan verbruiken die is ingericht op de doel-Azure Cosmos DB-container en de migratie in minder tijd kan voltooien.

## <a name="azure-cosmos-db-sql-api"></a>SQL-API voor Azure Cosmos DB
|**Migratietype**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Offline|[Hulpprogramma voor gegevensmigratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Niet geschikt voor grote datasets|
|Offline|[Azure-gegevensfabriek](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Maakt gebruik van de Azure Cosmos DB bulk executor library <br/>&bull;Geschikt voor grote datasets <br/>&bull;Gebrek aan controlepunten - Dit betekent dat als er een probleem optreedt tijdens de migratie, u het hele migratieproces opnieuw moet starten<br/>&bull;Gebrek aan een dode letter wachtrij - Het betekent dat een paar foutieve bestanden kan het hele migratieproces te stoppen.|
|Offline|[Azure Cosmos DB Spark-connector](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Maakt gebruik van de Azure Cosmos DB bulk executor library <br/>&bull;Geschikt voor grote datasets <br/>&bull;Heeft een aangepaste Spark-installatie nodig <br/>&bull;Spark is gevoelig voor schema-inconsistenties en dit kan een probleem zijn tijdens migratie |
|Offline|[Aangepast gereedschap met cosmos DB-bulkuitvoerderbibliotheek](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Biedt controlepunten, dead-lettering mogelijkheden die migratie tolerantie verhoogt <br/>&bull;Geschikt voor zeer grote datasets (10 TB+)  <br/>&bull;Vereist aangepaste installatie van dit hulpprogramma dat als app-service wordt uitgevoerd |
|Online|[Cosmos DB-functies + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Eenvoudig in te stellen <br/>&bull;Werkt alleen als de bron een Azure Cosmos DB-container is <br/>&bull;Niet geschikt voor grote datasets <br/>&bull;Verwijdert geen verwijderen uit de broncontainer |
|Online|[Aangepaste migratieservice met ChangeFeed](https://aka.ms/CosmosDBMigrationSample)|&bull;Biedt voortgangstracking <br/>&bull;Werkt alleen als de bron een Azure Cosmos DB-container is <br/>&bull;Werkt ook voor grotere datasets <br/>&bull;De gebruiker moet een App-service instellen om de feedprocessor wijzigen te hosten <br/>&bull;Verwijdert geen verwijderen uit de broncontainer|
|Online|[Striim Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Werkt met een grote verscheidenheid aan bronnen zoals Oracle, DB2, SQL Server <br/>&bull;Eenvoudig te bouwen ETL-pijpleidingen en biedt een dashboard voor bewaking <br/>&bull;Ondersteunt grotere gegevenssets <br/>&bull;Aangezien dit een tool van derden is, moet het worden gekocht van de markt en geïnstalleerd in de omgeving van de gebruiker|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Migratietype**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Offline|[Hulpprogramma voor gegevensmigratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Niet geschikt voor grote datasets|
|Offline|[Azure-gegevensfabriek](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Maakt gebruik van de Azure Cosmos DB bulk executor library <br/>&bull;Geschikt voor grote datasets <br/>&bull;Gebrek aan controlepunten betekent dat elk probleem tijdens de migratie een herstart van het hele migratieproces vereist<br/>&bull;Het ontbreken van een dode letter wachtrij zou betekenen dat een paar foutieve bestanden kan het hele migratieproces te stoppen <br/>&bull;Aangepaste code nodig om de leesdoorvoer voor bepaalde gegevensbronnen te verhogen|
|Offline|[Bestaande Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Eenvoudig in te stellen en te integreren <br/>&bull;Heeft aangepaste handling nodig voor gashendels|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Maakt gebruik van de Azure Cosmos DB bulk executor library <br/>&bull;Geschikt voor grote datasets en zorgt voor het repliceren van live changes <br/>&bull;Werkt alleen met andere MongoDB bronnen|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra-API
|**Migratietype**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Offline|[cqlsh COPY, opdracht](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Eenvoudig in te stellen <br/>&bull;Niet geschikt voor grote datasets <br/>&bull;Werkt alleen als de bron een Cassandra-tafel is|
|Offline|[Tabel kopiëren met Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Kan gebruik maken van Spark-mogelijkheden om transformatie en inname te paralleleren <br/>&bull;Configuratie nodig met een aangepast beleid voor het opnieuw proberen om gashendels te verwerken|
|Online|[Striim (van Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Werkt met een grote verscheidenheid aan bronnen zoals Oracle, DB2, SQL Server <br/>&bull;Eenvoudig te bouwen ETL-pijpleidingen en biedt een dashboard voor bewaking <br/>&bull;Ondersteunt grotere gegevenssets <br/>&bull;Aangezien dit een tool van derden is, moet het worden gekocht van de markt en geïnstalleerd in de omgeving van de gebruiker|
|Online|[Blitzz (van Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Ondersteunt grotere gegevenssets <br/>&bull;Aangezien dit een tool van derden is, moet het worden gekocht van de markt en geïnstalleerd in de omgeving van de gebruiker|

## <a name="other-apis"></a>Overige API's
Voor andere API's dan de SQL API, Mongo API en de Cassandra API zijn er verschillende tools die worden ondersteund door elk van de bestaande ecosystemen van de API. 

**Tabel-API** 
* [Hulpprogramma voor gegevensmigratie](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy (AzCopy)](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin-API**
* [Bibliotheek voor bulkuitvoervan grafiek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie door de voorbeeldtoepassingen uit te proberen die de bibliotheek voor bulkuitvoerbestanden in [.NET](bulk-executor-dot-net.md) en [Java gebruiken](bulk-executor-java.md). 
* De bulkexecutorbibliotheek is geïntegreerd in de Cosmos DB Spark-connector, zie het artikel [azure cosmos DB Spark-connector.](spark-connector.md)  
* Neem contact op met het Azure Cosmos DB-productteam door een ondersteuningsticket te openen onder het probleemtype 'Algemeen advies' en het probleemsubtype 'Grote (TB+) migraties' voor extra hulp bij grootschalige migraties.
