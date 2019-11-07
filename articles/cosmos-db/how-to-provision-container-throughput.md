---
title: Containerdoorvoer inrichten in Azure Cosmos DB
description: Meer informatie over het inrichten van doorvoer op containerniveau in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 06de71776cdf503ff0df9fbf3b28cf9e01a12e01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575276"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Doorvoer inrichten voor een Azure Cosmos-container

In dit artikel wordt uitgelegd hoe u de door Voer kunt inrichten voor een container (verzameling, grafiek of tabel) in Azure Cosmos DB. U kunt de door Voer voor één container inrichten of [door Voer in te richten op een Data Base](how-to-provision-database-throughput.md) en deze te delen tussen de containers in de data base. U kunt de door Voer voor een container inrichten met behulp van Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

## <a name="provision-throughput-using-azure-portal"></a>Doorvoer inrichten met behulp van de Azure-portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deelvenster **Data Explorer** en selecteer **Nieuwe verzameling**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een ID in voor de container (of tabel of grafiek).
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/userid`).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    ![Schermopname van Data Explorer met Nieuwe verzameling gemarkeerd](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Door Voer in te richten met behulp van Azure CLI of Power shell

Als u een container met specifieke door voer wilt maken, raadpleegt u

* [Een container maken met behulp van Azure CLI](manage-with-cli.md#create-a-container)
* [Een container maken met behulp van Power shell](manage-with-powershell.md#create-container)

> [!Note]
> Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met de Azure Cosmos DB-API voor MongoDB, gebruikt u `/myShardKey` voor het pad van de partitie sleutel. Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met Cassandra-API, gebruikt u `/myPrimaryKey` voor het pad van de partitie sleutel.

## <a name="provision-throughput-by-using-net-sdk"></a>Doorvoer inrichten met behulp van .NET SDK

> [!Note]
> Gebruik de Cosmos Sdk's voor SQL API om de door Voer voor alle Cosmos DB Api's in te richten, met uitzonde ring van Cassandra-API.

### <a id="dotnet-most"></a>SQL-, MongoDB-, Gremlin- en Table-API's
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

### <a name="net-v3-sdk"></a>.Net v3-SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra-API
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

* [De door Voer voor een Data Base inrichten](how-to-provision-database-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
