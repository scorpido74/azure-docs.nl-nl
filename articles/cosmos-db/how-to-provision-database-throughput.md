---
title: Databasedoorvoer inrichten in Azure Cosmos DB
description: Meer informatie over het inrichten van doorvoer op databaseniveau in Azure Cosmos DB met Azure portal, CLI, PowerShell en diverse andere SDK's.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: ef7d06dfb074a3453f5589284cbdaf079c48d111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933766"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Doorvoer voorziening voor een database in Azure Cosmos DB

In dit artikel wordt uitgelegd hoe u doorvoer indient op een database in Azure Cosmos DB. U kunt de doorvoer inrichten voor één [container](how-to-provision-container-throughput.md) of inrichten voor een database en de doorvoer delen met de containers in de database. Zie De doorvoer [van gebruiksaanvragen voor het inrichten van doorvoer op containers en databases](set-throughput.md) voor meer informatie over het gebruik van containerniveau en doorvoer op databaseniveau. U kunt doorvoer op databaseniveau inrichten met behulp van de Azure-portal of Azure Cosmos DB-SDK's.

## <a name="provision-throughput-using-azure-portal"></a>Doorvoer inrichten met behulp van de Azure-portal

### <a name="sql-core-api"></a><a id="portal-sql"></a>SQL (Core) API

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deelvenster **Gegevensverkenner** en selecteer **Nieuwe database**. Geef de volgende gegevens op:

   * Voer een database-id in.
   * Selecteer **Inleveringsdoorvoer**.
   * Voer een doorvoer in, bijvoorbeeld 1000 RU's.
   * Selecteer **OK**.

    ![Schermafbeelding van het dialoogvenster Nieuwe database](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Doorvoer voorzien van Azure CLI of PowerShell

Als u een database met gedeelde doorvoer wilt maken, ziet u

* [Een database maken met Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Een database maken met Powershell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Doorvoer inrichten met behulp van .NET SDK

> [!Note]
> U Cosmos SDK's voor SQL API gebruiken om doorvoer voor alle API's in te richten. U kunt ook het volgende voorbeeld voor Cassandra-API gebruiken.

### <a name="all-apis"></a><a id="dotnet-all"></a>Alle API's

### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra-API
Vergelijkbare opdracht kan worden uitgevoerd via elk CQL-compatibel stuurprogramma. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over de ingerichte doorvoer in Azure Cosmos DB:

* [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Doorvoer inrichten voor een container](how-to-provision-container-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
