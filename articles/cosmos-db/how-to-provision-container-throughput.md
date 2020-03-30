---
title: Containerdoorvoer inrichten in Azure Cosmos DB
description: Meer informatie over het inrichten van doorvoer op containerniveau in Azure Cosmos DB met Azure portal, CLI, PowerShell en diverse andere SDK's.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273300"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Doorvoer inrichten voor een Azure Cosmos-container

In dit artikel wordt uitgelegd hoe u doorvoer ineenbiedt voor een container (verzameling, grafiek of tabel) in Azure Cosmos DB. U doorvoer inrichten op één container of [doorvoer inrichten op een database](how-to-provision-database-throughput.md) en deze delen tussen de containers in de database. U doorvoer op een container inrichten met Azure-portal, Azure CLI of Azure Cosmos DB SDKs.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe verzameling**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-id (of tabel of grafiek) in.
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/userid`).
   * Voer een doorvoer in die u wilt inrichten (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermopname van Data Explorer met Nieuwe verzameling gemarkeerd](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI of PowerShell

Als u een container wilt maken met speciale doorvoer, ziet u

* [Een container maken met behulp van Azure CLI](manage-with-cli.md#create-a-container)
* [Een container maken met Powershell](manage-with-powershell.md#create-container)

> [!Note]
> Als u doorvoer indient op een container in een Azure Cosmos-account dat is `/myShardKey` geconfigureerd met de Azure Cosmos DB API voor MongoDB, gebruikt u het pad met de partitiesleutel. Als u doorvoer indient op een container in een Azure `/myPrimaryKey` Cosmos-account dat is geconfigureerd met Cassandra API, gebruikt u het pad met de partitiesleutel.

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Gebruik de Cosmos SDKs voor SQL API om doorvoer te voorzien voor alle Cosmos DB API's, behalve Cassandra API.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL-, MongoDB-, Gremlin- en Table-API's
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API

Vergelijkbare opdrachten kunnen worden uitgegeven via elk CQL-compatibel stuurprogramma.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Doorvoer voor cassandratabel wijzigen of wijzigen

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Doorvoer inrichten op een database](how-to-provision-database-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
