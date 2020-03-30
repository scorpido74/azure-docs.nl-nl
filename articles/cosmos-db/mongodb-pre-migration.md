---
title: Stappen voor premigratie voor gegevensmigratie naar azure cosmos DB's API voor MongoDB
description: Dit document geeft een overzicht van de vereisten voor een gegevensmigratie van MongoDB naar Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942081"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Pre-migratiestappen voor gegevensmigraties van MongoDB naar Azure Cosmos DB's API voor MongoDB

Voordat u uw gegevens migreert van MongoDB (on-premises of in de cloud) naar de API van Azure Cosmos DB voor MongoDB, moet u het sein en de

1. [Lees de belangrijkste overwegingen over het gebruik van Azure Cosmos DB's API voor MongoDB](#considerations)
2. [Een optie kiezen om uw gegevens te migreren](#options)
3. [Schat de doorvoer die nodig is voor uw workloads](#estimate-throughput)
4. [Kies een optimale partitiesleutel voor uw gegevens](#partitioning)
5. [Inzicht krijgen in het indexeringsbeleid dat u op uw gegevens instellen](#indexing)

Als u de bovenstaande vereisten voor migratie al hebt voltooid, u [MongoDB-gegevens migreren naar de API van Azure Cosmos DB voor MongoDB met behulp van de Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md). Als u geen account hebt gemaakt, u bovendien door een van de [Quickstarts](create-mongodb-dotnet.md) bladeren die de stappen weergeven om een account te maken.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Overwegingen bij het gebruik van de API van Azure Cosmos DB voor MongoDB

Hieronder volgen specifieke kenmerken van de API van Azure Cosmos DB voor MongoDB:

- **Capaciteitsmodel:** Databasecapaciteit op Azure Cosmos DB is gebaseerd op een op doorvoer gebaseerd model. Dit model is gebaseerd op [aanvraageenheden per seconde,](request-units.md)een eenheid die het aantal databasebewerkingen vertegenwoordigt dat per seconde tegen een verzameling kan worden uitgevoerd. Deze capaciteit kan worden toegewezen op [database- of verzamelniveau](set-throughput.md)en kan worden ingericht op een toewijzingsmodel of met behulp van het [AutoPilot-model](provision-throughput-autopilot.md).

- **Aanvraageenheden:** elke databasebewerking heeft een bijbehorende Aanvraageenheden (RU's) in Azure Cosmos DB. Wanneer dit wordt uitgevoerd, wordt dit afgetrokken van het niveau van de beschikbare aanvraageenheden op een bepaalde seconde. Als een aanvraag meer RU's vereist dan de momenteel toegewezen RU/s, zijn er twee opties om het probleem op te lossen - verhoog het aantal RU's of wacht tot de volgende seconde wordt gestart en probeer de bewerking opnieuw.

- **Elastische capaciteit**: De capaciteit voor een bepaalde verzameling of database kan op elk gewenst moment veranderen. Hierdoor kan de database zich elastisch aanpassen aan de doorvoervereisten van uw werkbelasting.

- **Automatische sharding**: Azure Cosmos DB biedt een automatisch partitioneringssysteem waarvoor alleen een shard (of een partitiesleutel) nodig is. Het [automatische scheidingsmechanisme](partition-data.md) wordt gedeeld over alle Azure Cosmos DB API's en maakt naadloze gegevens en gedurende het schalen door middel van horizontale distributie mogelijk.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Migratieopties voor de API van Azure Cosmos DB voor MongoDB

De [API voor Azure Database Migration Service voor Azure Cosmos DB voor MongoDB](../dms/tutorial-mongodb-cosmos-db.md) biedt een mechanisme dat gegevensmigratie vereenvoudigt door een volledig beheerd hostingplatform, migratiebewakingsopties en automatische beperkingsafhandeling te bieden. De volledige lijst met opties zijn de volgende:

|**Migratietype**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Offline|[Hulpprogramma voor gegevensmigratie](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Niet geschikt voor grote datasets.|
|Offline|[Azure-gegevensfabriek](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull;Maakt gebruik van de Azure Cosmos DB bulk executor library <br/>&bull;Geschikt voor grote datasets <br/>&bull;Gebrek aan controlepunten betekent dat elk probleem tijdens de migratie een herstart van het hele migratieproces vereist<br/>&bull;Het ontbreken van een dode letter wachtrij zou betekenen dat een paar foutieve bestanden kan het hele migratieproces te stoppen <br/>&bull;Aangepaste code nodig om de leesdoorvoer voor bepaalde gegevensbronnen te verhogen|
|Offline|[Bestaande Mongo Tools (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Eenvoudig in te stellen en te integreren <br/>&bull;Heeft aangepaste handling nodig voor gashendels|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Volledig beheerde migratieservice.<br/>&bull;Biedt hosting- en monitoringoplossingen voor de migratietaak. <br/>&bull;Geschikt voor grote datasets en zorgt voor het repliceren van live changes <br/>&bull;Werkt alleen met andere MongoDB bronnen|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Schat de doorvoerbehoefte voor uw workloads

In Azure Cosmos DB wordt de doorvoer vooraf ingericht en gemeten in Request Units (RU's) per seconde. In tegenstelling tot VM's of on-premises servers, zijn ROM's eenvoudig op en neer te schalen op elk gewenst moment. U het aantal ingerichte RU's direct wijzigen. Zie [Eenheden aanvragen in Azure Cosmos DB voor](request-units.md)meer informatie.

U de [Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) gebruiken om de hoeveelheid aanvraageenheden te bepalen op basis van de configuratie van uw databaseaccount, de hoeveelheid gegevens, de documentgrootte en de vereiste lees- en schrijfbewerkingen per seconde.

De volgende zijn belangrijke factoren die van invloed zijn op het aantal vereiste R's:
- **Documentgrootte**: Naarmate de grootte van een item/document toeneemt, neemt ook het aantal R's dat wordt verbruikt om het item/document te lezen of te schrijven toe.

- **Aantal documenteigenschappen**:Het aantal R's dat is verbruikt om een document te maken of bij te werken, is gerelateerd aan het aantal, de complexiteit en de lengte van de eigenschappen. U het verbruik van de aanvraageenheid voor schrijfbewerkingen verminderen door [het aantal geïndexeerde eigenschappen te beperken.](mongodb-indexing.md)

- **Querypatronen:** de complexiteit van een query is van invloed op het aantal aanvraageenheden dat door de query wordt verbruikt. 

De beste manier om de kosten van query's te begrijpen, is door voorbeeldgegevens in Azure Cosmos DB te gebruiken [en voorbeeldquery's van de MongoDB Shell uit](connect-mongodb-account.md) te voeren met behulp van de `getLastRequestStastistics` opdracht om de aanvraagkosten te krijgen, waardoor het aantal verbruikte R's wordt uitgevoerd:

`db.runCommand({getLastRequestStatistics: 1})`

Met deze opdracht wordt een JSON-document uitgevoerd dat vergelijkbaar is met het volgende:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

U [ook de diagnostische instellingen](cosmosdb-monitor-resource-logs.md) gebruiken om inzicht te krijgen in de frequentie en patronen van de query's die zijn uitgevoerd tegen Azure Cosmos DB. De resultaten van de diagnostische logboeken kunnen worden verzonden naar een opslagaccount, een EventHub-instantie of [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Kies uw partitiesleutel
Partitionering, ook wel bekend als Sharding, is een belangrijk aandachtspunt voordat gegevens worden gemigreerd. Azure Cosmos DB maakt gebruik van volledig beheerde partitionering om de capaciteit in een database te vergroten om te voldoen aan de opslag- en doorvoervereisten. Deze functie heeft geen hosting of configuratie van routerservers nodig.   

Op een vergelijkbare manier voegt de partitioneringsmogelijkheid automatisch capaciteit toe en herbalanceert de gegevens dienovereenkomstig. Zie [het artikel Een partitiesleutel kiezen](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)voor meer informatie en aanbevelingen over het kiezen van de juiste partitiesleutel voor uw gegevens. 

## <a name="index-your-data"></a><a id="indexing"></a>Uw gegevens indexeren
Azure Cosmos DB biedt standaard automatische indexering van alle ingevoegde gegevens. De indexeringsmogelijkheden van Azure Cosmos DB omvatten het toevoegen van samengestelde indexen, unieke indexen en time-to-live (TTL) indexen. De indexbeheerinterface wordt toegewezen `createIndex()` aan de opdracht. Meer informatie over [Indexing in Azure Cosmos DB's API voor MongoDB](mongodb-indexing.md).

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migreert automatisch MongoDB-verzamelingen met unieke indexen. De unieke indexen moeten echter vóór de migratie worden gemaakt. Azure Cosmos DB ondersteunt geen unieke indexen, wanneer er al gegevens in uw verzamelingen zijn. Zie [Unieke sleutels in Azure Cosmos DB](unique-keys.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Migreer uw MongoDB-gegevens naar Cosmos DB met behulp van de Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Doorvoer voorziening op Azure Cosmos-containers en -databases](set-throughput.md)
* [Partitionering in Azure Cosmos DB](partition-data.md)
* [Globale distributie in Azure Cosmos DB](distribute-data-globally.md)
* [Indexering in Azure Cosmos DB](index-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
