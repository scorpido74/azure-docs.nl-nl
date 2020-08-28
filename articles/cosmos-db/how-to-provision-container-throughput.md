---
title: Containerdoorvoer inrichten in Azure Cosmos DB
description: Meer informatie over het inrichten van de door Voer op het niveau van de container in Azure Cosmos DB met behulp van Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mjbrown
ms.custom: devx-track-javascript, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cf2bd616fc739d233826769baaa14298d0fcec2d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017121"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Standaard doorvoer (hand matig) door Voer voor een Azure Cosmos-container

In dit artikel wordt uitgelegd hoe u de standaard doorvoer (hand matig) kunt inrichten voor een container (verzameling, grafiek of tabel) in Azure Cosmos DB. U kunt de door Voer voor één container inrichten of [door Voer in te richten op een Data Base](how-to-provision-database-throughput.md) en deze te delen tussen de containers in de data base. U kunt de door Voer voor een container inrichten met behulp van Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe verzameling**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een ID in voor de container (of tabel of grafiek).
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/userid`).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png" alt-text="Schermopname van Data Explorer met Nieuwe verzameling gemarkeerd":::

## <a name="azure-cli-or-powershell"></a>Azure CLI of Power shell

Als u een container met specifieke door voer wilt maken, raadpleegt u

* [Een container maken met behulp van Azure CLI](manage-with-cli.md#create-a-container)
* [Een container maken met behulp van Power shell](manage-with-powershell.md#create-container)

> [!Note]
> Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met de Azure Cosmos DB-API voor MongoDB, gebruikt u `/myShardKey` voor het pad van de partitie sleutel. Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met Cassandra-API, gebruikt u `/myPrimaryKey` voor het pad van de partitie sleutel.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Gebruik de Cosmos Sdk's voor SQL API om de door Voer voor alle Cosmos DB Api's in te richten, behalve de Cassandra-en MongoDB-API.

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL-, Gremlin-en Table-Api's

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="mongodb-api"></a><a id="dotnet-mongodb"></a>MongoDB-API

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API

Vergelijk bare opdrachten kunnen worden uitgegeven via elk CQL-compatibel stuur programma.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>De door Voer voor de tabel Cassandra wijzigen of wijzigen

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Standaard doorvoer (hand matig) inrichten voor een Data Base](how-to-provision-database-throughput.md)
* [De door Voer van automatisch schalen inrichten voor een Data Base](how-to-provision-autoscale-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
