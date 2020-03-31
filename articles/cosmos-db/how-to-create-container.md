---
title: Een container maken in Azure Cosmos DB
description: Meer informatie over het maken van een container in Azure Cosmos DB met Azure portal, .Net, Java, Python, Node.js en andere SDK's.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873706"
---
# <a name="create-an-azure-cosmos-container"></a>Een Azure Cosmos-container maken

In dit artikel worden de verschillende manieren uitgelegd om een Azure Cosmos-container (verzameling, tabel of grafiek) te maken. U hiervoor Azure portal, Azure CLI of ondersteunde SDK's gebruiken. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

## <a name="create-a-container-using-azure-portal"></a>Een container maken met behulp van de Azure-portal

### <a name="sql-api"></a><a id="portal-sql"></a>SQL-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een partitiesleutel in.
   * Voer een te bedieningsdoorvoer in (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermafbeelding van het deelvenster Gegevensverkenner met nieuwe container gemarkeerd](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>Azure Cosmos DB-API voor MongoDB

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een shardsleutel in.
   * Voer een te bedieningsdoorvoer in (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermafbeelding van Azure Cosmos DB API voor MongoDB, dialoogvenster Container toevoegen](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-cassandra-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe tabel**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe keyspace maakt of een bestaande keyspace gebruikt.
   * Voer een tabelnaam in.
   * Voer de eigenschappen in en geef een primaire sleutel op.
   * Voer een te bedieningsdoorvoer in (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermopname van Cassandra-API, dialoogvenster Tabel toevoegen](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Voor de Cassandra-API wordt de primaire sleutel gebruikt als de partitiesleutel.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-graph-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe grafiek**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een grafiek-id in.
   * Selecteer **onbeperkte** opslagcapaciteit.
   * Voer een partitiesleutel in voor hoekpunten.
   * Voer een te bedieningsdoorvoer in (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermopname van Gremlin-API, dialoogvenster Grafiek toevoegen](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>Table-API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-table-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe tabel**. Geef de volgende gegevens op:

   * Voer een tabel-id in.
   * Voer een te bedieningsdoorvoer in (bijvoorbeeld 1000 RU's).
   * Selecteer **OK**.

    ![Schermopname van Table-API, dialoogvenster Grafiek toevoegen](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Voor de Table-API wordt de partitiesleutel opgegeven telkens wanneer u een nieuwe rij toevoegt.

## <a name="create-a-container-using-azure-cli"></a>Een container maken met Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

In de onderstaande koppelingen wordt weergegeven hoe u containerresources voor Azure Cosmos DB maakt met Azure CLI.

Voor een lijst van alle Azure CLI-samples in alle Azure Cosmos DB API's zie SQL [API,](cli-samples.md) [Cassandra API,](cli-samples-cassandra.md) [MongoDB API,](cli-samples-mongodb.md) [Gremlin API](cli-samples-gremlin.md)en [Table API](cli-samples-table.md)

* [Een container maken met Azure CLI](manage-with-cli.md#create-a-container)
* [Een verzameling maken voor Azure Cosmos DB voor MongoDB API met Azure CLI](./scripts/cli/mongodb/create.md)
* [Een Cassandra-tabel maken met Azure CLI](./scripts/cli/cassandra/create.md)
* [Een Gremlin-grafiek maken met Azure CLI](./scripts/cli/gremlin/create.md)
* [Een tabel-API-tabel maken met Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Een container maken met PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

In de onderstaande koppelingen wordt weergegeven hoe u containerresources voor Azure Cosmos DB maakt met PowerShell.

Voor een lijst van alle Azure CLI-samples in alle Azure Cosmos DB API's zie SQL [API,](powershell-samples-sql.md) [Cassandra API,](powershell-samples-cassandra.md) [MongoDB API,](powershell-samples-mongodb.md) [Gremlin API](powershell-samples-gremlin.md)en [Table API](powershell-samples-table.md)

* [Een container maken met Powershell](manage-with-powershell.md#create-container)
* [Een verzameling maken voor Azure Cosmos DB voor MongoDB API met Powershell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Een Cassandra-tafel maken met Powershell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Een Gremlin-grafiek maken met Powershell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Een tabelAPI-tabel maken met Powershell](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Een container maken met behulp van .NET SDK

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
> MongoDB draadprotocol begrijpt het concept van [Request Units](request-units.md)niet. Als u een nieuwe verzameling wilt maken met ingerichte doorvoer, gebruikt u de Azure-portal of Cosmos DB SDKs voor SQL API.

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
