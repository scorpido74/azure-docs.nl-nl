---
title: Cosmos DB migratie opties
description: In dit document worden de verschillende opties beschreven voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 3945048060084874cf9d213aae83c3504312875c
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465742"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opties voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB

U kunt gegevens uit verschillende gegevens bronnen laden naar Azure Cosmos DB. Omdat Azure Cosmos DB meerdere Api's ondersteunt, kunnen de doelen bovendien een van de bestaande Api's zijn. Voor het ondersteunen van migratie paden van de verschillende bronnen naar de verschillende Azure Cosmos DB Api's, zijn er meerdere oplossingen die gespecialiseerde verwerking bieden voor elk migratie traject. In dit document vindt u een lijst met de beschik bare oplossingen en worden de voor delen en beperkingen beschreven.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Factoren die van invloed zijn op de keuze van het migratie hulpprogramma

De volgende factoren bepalen de keuze van het migratie programma:
* **Online versus offline migratie**: veel hulpprogram ma's voor migratie bieden een pad voor het uitvoeren van een eenmalige migratie. Dit betekent dat de toepassingen die toegang hebben tot de Data Base een periode van downtime kunnen ondervinden. Sommige migratie oplossingen bieden een manier om een Livemigratie uit te voeren waarbij een replicatie pijplijn is ingesteld tussen de bron en het doel.

* **Gegevens bron**: de bestaande gegevens kunnen zich in verschillende gegevens bronnen bevinden, zoals Oracle DB2, Datastax Cassanda, Azure SQL database, PostgreSQL, enzovoort. De gegevens kunnen zich ook in een bestaand Azure Cosmos DB account bevinden en het doel van de migratie kan het gegevens model wijzigen of de gegevens in een container opnieuw partitioneren met een andere partitie sleutel.

* **Azure Cosmos DB-API**: voor de SQL-api in azure Cosmos DB bestaan er diverse hulpprogram ma's die zijn ontwikkeld door het Azure Cosmos DB team en die ondersteuning bieden voor de verschillende migratie scenario's. Alle andere Api's hebben hun eigen gespecialiseerde set hulpprogram ma's die door de community zijn ontwikkeld en onderhouden. Omdat Azure Cosmos DB deze Api's ondersteunt op een niveau van een kabel protocol, moeten deze hulpprogram ma's werken zoals bij het migreren van gegevens naar Azure Cosmos DB. Het is echter mogelijk dat er aangepaste verwerking vereist is, omdat dit concept specifiek is voor Azure Cosmos DB.

* **Grootte van gegevens**: de meeste migratie hulpprogramma's werken zeer goed voor kleinere gegevens sets. Wanneer de gegevensset meer dan honderd GB overschrijdt, zijn de opties voor migratie hulpprogramma's beperkt. 

* **Verwachte migratie duur**: migraties kunnen worden geconfigureerd om te worden uitgevoerd met een traag, incrementeel tempo dat minder door Voer verbruikt of de volledige door Voer die is ingericht op de doel Azure Cosmos DB container verbruikt en de migratie in minder tijd kan volt ooien.

## <a name="azure-cosmos-db-sql-api"></a>SQL-API voor Azure Cosmos DB
|**Type migratie**|**Oplossen**|**Overwegingen**|
|---------|---------|---------|
|Offline|[Hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Niet geschikt voor grote gegevens sets|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull;Geschikt voor grote gegevens sets <br/>&bull;Geen controle punten: Dit betekent dat als er een probleem optreedt tijdens de migratie, het hele migratie proces opnieuw moet worden gestart<br/>&bull;Geen wachtrij met onbestelbare berichten: Dit betekent dat een aantal foutieve bestanden het gehele migratie proces kan stoppen.|
|Offline|[Azure Cosmos DB Spark-connector](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull;Geschikt voor grote gegevens sets <br/>&bull;Vereist een aangepaste Spark-installatie <br/>&bull;Spark is gevoelig voor inconsistenties in het schema en dit kan een probleem zijn tijdens de migratie |
|Offline|[Aangepast hulp programma met Cosmos DB bibliotheek voor bulksgewijs uitvoerder](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Voorziet in controle punten, functies voor onbestelbare berichten waardoor migratie tolerantie wordt verhoogd <br/>&bull;Geschikt voor zeer grote gegevens sets (10 TB +)  <br/>&bull;Aangepaste Setup van dit hulp programma moet worden uitgevoerd als een App Service |
|Online|[Cosmos DB functions + ChangeFeed-API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Eenvoudig in te stellen <br/>&bull;Werkt alleen als de bron een Azure Cosmos DB container is <br/>&bull;Niet geschikt voor grote gegevens sets <br/>&bull;Er worden geen verwijderingen van de bron container vastgelegd |
|Online|[Aangepaste migratie service met ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Biedt voortgangs tracering <br/>&bull;Werkt alleen als de bron een Azure Cosmos DB container is <br/>&bull;Werkt ook voor grotere gegevens sets <br/>&bull;Vereist dat de gebruiker een App Service instelt voor het hosten van de Change feed-processor <br/>&bull;Er worden geen verwijderingen van de bron container vastgelegd|
|Online|[Realtimeplatform](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Werkt met een groot aantal verschillende bronnen, zoals Oracle, DB2, SQL Server <br/>&bull;Eenvoudig ETL-pijp lijnen bouwen en een dash board voor bewaking bieden <br/>&bull;Biedt ondersteuning voor grotere gegevens sets <br/>&bull;Aangezien dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo-API
|**Type migratie**|**Oplossen**|**Overwegingen**|
|---------|---------|---------|
|Offline|[Hulp programma voor gegevens migratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Niet geschikt voor grote gegevens sets|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull;Geschikt voor grote gegevens sets <br/>&bull;Geen controle punten houdt in dat een probleem tijdens de migratie het opnieuw opstarten van het hele migratie proces vereist.<br/>&bull;Geen wachtrij met onbestelbare berichten zou betekenen dat een paar foutieve bestanden het gehele migratie proces zouden kunnen stoppen <br/>&bull;Aangepaste code nodig om de Lees doorvoer voor bepaalde gegevens bronnen te verg Roten|
|Offline|[Bestaande Mongo-Hulpprogram Ma's (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Eenvoudig in te stellen en te integreren <br/>&bull;Aangepaste verwerking vereist voor beperking|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull;Geschikt voor grote gegevens sets en zorgt voor het repliceren van Live wijzigingen <br/>&bull;Werkt alleen met andere MongoDB-bronnen|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra-API
|**Type migratie**|**Oplossen**|**Overwegingen**|
|---------|---------|---------|
|Offline|[cqlsh kopiëren opdracht](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Eenvoudig in te stellen <br/>&bull;Niet geschikt voor grote gegevens sets <br/>&bull;Werkt alleen wanneer de bron een Cassandra-tabel is|
|Offline|[Tabel kopiëren met Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Kan gebruik maken van Spark-mogelijkheden om trans formatie en opname te parallelliseren <br/>&bull;Moet worden geconfigureerd met een aangepast beleid voor opnieuw proberen om beperkingen te verwerken|
|Online|[Realtimeplatform (uit Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Werkt met een groot aantal verschillende bronnen, zoals Oracle, DB2, SQL Server <br/>&bull;Eenvoudig ETL-pijp lijnen bouwen en een dash board voor bewaking bieden <br/>&bull;Biedt ondersteuning voor grotere gegevens sets <br/>&bull;Aangezien dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|
|Online|[Blitzz (uit Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Biedt ondersteuning voor grotere gegevens sets <br/>&bull;Aangezien dit een hulp programma van derden is, moet het worden aangeschaft via Marketplace en worden geïnstalleerd in de omgeving van de gebruiker.|

## <a name="other-apis"></a>Overige API's
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
