---
title: Een container maken in Azure Cosmos DB
description: Meer informatie over het maken van een container in Azure Cosmos DB met behulp van Azure Portal, .NET, Java, Python, Node.js en andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019994"
---
# <a name="create-an-azure-cosmos-container"></a>Een Azure Cosmos-container maken

In dit artikel worden de verschillende manieren beschreven om een Azure Cosmos-container (verzameling, tabel of grafiek) te maken met behulp van Azure Portal, Azure CLI, Power shell of ondersteunde Sdk's. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="create-a-container-using-azure-portal"></a>Een container maken met behulp van de Azure-portal

### <a name="sql-api"></a><a id="portal-sql"></a>SQL-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een partitiesleutel in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Scherm afbeelding van Data Explorer deel venster, met nieuwe container gemarkeerd":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>Azure Cosmos DB-API voor MongoDB

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een shardsleutel in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Scherm opname van Azure Cosmos DB-API voor MongoDB, container toevoegen dialoog venster":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-cassandra-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe tabel**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe keyspace maakt of een bestaande keyspace gebruikt.
   * Voer een tabelnaam in.
   * Voer de eigenschappen in en geef een primaire sleutel op.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Schermopname van Cassandra-API, dialoogvenster Tabel toevoegen":::

> [!NOTE]
> Voor de Cassandra-API wordt de primaire sleutel gebruikt als de partitiesleutel.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-graph-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe grafiek**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een grafiek-ID in.
   * Selecteer **onbeperkte** opslagcapaciteit.
   * Voer een partitiesleutel in voor hoekpunten.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Schermopname van Gremlin-API, dialoogvenster Grafiek toevoegen":::

### <a name="table-api"></a><a id="portal-table"></a>Tabel-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-table-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe tabel**. Geef de volgende gegevens op:

   * Voer een tabel-ID in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Schermopname van Table-API, dialoogvenster Grafiek toevoegen":::

> [!Note]
> Voor de Table-API wordt de partitiesleutel opgegeven telkens wanneer u een nieuwe rij toevoegt.

## <a name="create-a-container-using-azure-cli"></a>Een container maken met behulp van Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

De onderstaande koppelingen laten zien hoe u container bronnen voor Azure Cosmos DB maakt met behulp van Azure CLI.

Zie [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md)voor een overzicht van alle Azure CLI-voor beelden in alle Azure Cosmos DB api's.

* [Een container maken met Azure CLI](manage-with-cli.md#create-a-container)
* [Een verzameling voor Azure Cosmos DB voor MongoDB-API maken met Azure CLI](./scripts/cli/mongodb/create.md)
* [Een Cassandra-tabel maken met Azure CLI](./scripts/cli/cassandra/create.md)
* [Een Gremlin-grafiek maken met Azure CLI](./scripts/cli/gremlin/create.md)
* [Een Table-API tabel maken met Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Een container maken met behulp van Power shell

De onderstaande koppelingen laten zien hoe u container resources voor Azure Cosmos DB maakt met behulp van Power shell.

Zie [Power shell](powershell-samples.md) -voor beelden voor een lijst met alle Power shell-voor beelden in alle Azure Cosmos DB api's

* [Een container maken met Power shell](manage-with-powershell.md#create-container)
* [Een verzameling maken voor Azure Cosmos DB voor MongoDB-API met Power shell](./scripts/powershell/mongodb/create.md)
* [Een Cassandra-tabel maken met Power shell](./scripts/powershell/cassandra/create.md)
* [Een Gremlin-grafiek maken met Power shell](./scripts/powershell/gremlin/create.md)
* [Een Table-API tabel maken met Power shell](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Een container maken met behulp van .NET SDK

Als er een time-outuitzondering optreedt bij het maken van een verzameling, moet u een lees bewerking uitvoeren om te controleren of de verzameling is gemaakt. Met de Lees bewerking wordt een uitzonde ring gegenereerd totdat de bewerking voor het maken van de verzameling is voltooid. Zie de [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikel voor een lijst met status codes die door de maak bewerking worden ondersteund.

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL-API en Gremlin-API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>Azure Cosmos DB-API voor MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Het MongoDB-draad protocol begrijpt het concept van [aanvraag eenheden](request-units.md)niet. Als u een nieuwe verzameling wilt maken met een ingerichte door Voer, gebruikt u de Azure Portal-of Cosmos DB Sdk's voor SQL API.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](account-overview.md)
