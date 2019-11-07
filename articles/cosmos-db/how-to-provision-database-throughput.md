---
title: Databasedoorvoer inrichten in Azure Cosmos DB
description: Meer informatie over het inrichten van doorvoer op databaseniveau in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 64a8bc9f4c9f5192dad22cb55cb4d2c4816d4fa5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575144"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Door Voer in te richten op een data base in Azure Cosmos DB

In dit artikel wordt uitgelegd hoe u de door Voer inricht voor een data base in Azure Cosmos DB. U kunt de doorvoer inrichten voor één [container](how-to-provision-container-throughput.md) of inrichten voor een database en de doorvoer delen met de containers in de database. Zie voor meer informatie over het gebruik van de door Voer voor het inrichten van containers en data bases door [Voer voor het maken van een doorvoer snelheid voor containers en data bases](set-throughput.md) . U kunt doorvoer op databaseniveau inrichten met behulp van de Azure-portal of Azure Cosmos DB-SDK's.

## <a name="provision-throughput-using-azure-portal"></a>Doorvoer inrichten met behulp van de Azure-portal

### <a id="portal-sql"></a>SQL (Core) API

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deelvenster **Data Explorer** en selecteer **Nieuwe database**. Geef de volgende gegevens op:

   * Voer een database-id in.
   * Selecteer **Doorvoer inrichten**.
   * Voer een doorvoer in, bijvoorbeeld 1000 RU's.
   * Selecteer **OK**.

    ![Schermafbeelding van het dialoogvenster Nieuwe database](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Door Voer in te richten met behulp van Azure CLI of Power shell

Als u een Data Base met gedeelde door voer wilt maken, raadpleegt u

* [Een Data Base maken met behulp van Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Een Data Base maken met behulp van Power shell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Doorvoer inrichten met behulp van .NET SDK

> [!Note]
> U kunt Cosmos Sdk's voor SQL API gebruiken om de door Voer voor alle Api's in te richten. U kunt ook het volgende voorbeeld voor Cassandra-API gebruiken.

### <a id="dotnet-all"></a>Alle API's

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

### <a name="net-v3-sdk"></a>.Net v3-SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra-API
Een vergelijk bare opdracht kan worden uitgevoerd via elk CQL-compatibel stuur programma. 
```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over ingerichte door Voer in Azure Cosmos DB:

* [Ingerichte door Voer voor wereld wijd schalen](scaling-throughput.md)
* [Door Voer voor het inrichten van containers en data bases](set-throughput.md)
* [Doorvoer inrichten voor een container](how-to-provision-container-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
