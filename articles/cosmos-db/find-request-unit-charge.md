---
title: De kosten voor de aanvraag eenheid (RU) zoeken in Azure Cosmos DB
description: Meer informatie over het vinden van de kosten voor de aanvraag eenheid (RU) voor elke bewerking die wordt uitgevoerd op een Azure Cosmos-container.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: bf109d3f15c9865a8e9ad1d27a1e8d320d172761
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261831"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>De kosten voor de aanvraag eenheid zoeken in Azure Cosmos DB

In dit artikel worden de verschillende manieren beschreven waarop u het gebruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op basis van een container in azure Cosmos db. Op dit moment kunt u dit verbruik alleen meten met behulp van de Azure Portal of door de reactie die via een van de Sdk's wordt teruggestuurd via Azure Cosmos DB te controleren.

## <a name="sql-core-api"></a>SQL (Core) API

Als u de SQL-API gebruikt, hebt u meerdere opties voor het vinden van het RU-verbruik voor een bewerking in een Azure Cosmos-container.

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

Op dit moment kunt u de aanvraag kosten in de Azure Portal alleen vinden voor een SQL-query.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account) en voer het in met gegevens of selecteer een bestaand Azure Cosmos-account dat al gegevens bevat.

1. Ga naar het deel venster **Data Explorer** en selecteer vervolgens de container waaraan u wilt werken.

1. Selecteer **New SQL Query**.

1. Voer een geldige query in en selecteer **query uitvoeren**.

1. Selecteer **query statistieken** om de werkelijke aanvraag kosten weer te geven voor de aanvraag die u hebt uitgevoerd.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Scherm opname van de kosten van een SQL-query-aanvraag in de Azure Portal":::

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Objecten die worden geretourneerd door de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) , bieden een `RequestCharge` eigenschap:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objecten die worden geretourneerd door de [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) , bieden een `RequestCharge` eigenschap:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Zie [Quick Start: een .net-Web-app bouwen met behulp van een SQL-API-account in azure Cosmos DB](create-sql-api-dotnet.md)voor meer informatie.

---

### <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Objecten die worden geretourneerd door de [Java-SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) , bieden een `getRequestCharge()` methode:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Zie [Quick Start: een Java-toepassing bouwen met behulp van een Azure Cosmos DB SQL-API-account](create-sql-api-java.md)voor meer informatie.

### <a name="use-the-nodejs-sdk"></a>De Node.js SDK gebruiken

Objecten die worden geretourneerd door de [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) geven een `headers` SubObject weer waarmee alle headers worden toegewezen die worden geretourneerd door de onderliggende http API. De aanvraag kosten zijn beschikbaar onder de `x-ms-request-charge` volgende sleutel:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Zie [Quick Start: een Node.js-app bouwen met behulp van een Azure Cosmos DB SQL-API-account](create-sql-api-nodejs.md)voor meer informatie. 

### <a name="use-the-python-sdk"></a>De Python-SDK gebruiken

Het `CosmosClient` object van de [PYTHON-SDK](https://pypi.org/project/azure-cosmos/) geeft een `last_response_headers` woorden lijst weer waarin alle headers worden toegewezen die worden geretourneerd door de onderliggende http API voor de laatste bewerking die is uitgevoerd. De aanvraag kosten zijn beschikbaar onder de `x-ms-request-charge` volgende sleutel:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Zie [Quick Start: een python-app bouwen met een Azure Cosmos DB SQL-API-account](create-sql-api-python.md)voor meer informatie. 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

De RU-kosten worden weer gegeven met een aangepaste [database opdracht](https://docs.mongodb.com/manual/reference/command/) met de naam `getLastRequestStatistics` . Met de opdracht wordt een document geretourneerd dat de naam bevat van de laatste bewerking die is uitgevoerd, de aanvraag kosten en de duur. Als u de Azure Cosmos DB-API voor MongoDB gebruikt, hebt u meerdere opties om de RU-kosten op te halen.

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken

Op dit moment kunt u de aanvraag kosten vinden in de Azure Portal alleen voor een query.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account) en voer het in met gegevens of selecteer een bestaand account dat al gegevens bevat.

1. Ga naar het deel venster **Data Explorer** en selecteer vervolgens de container waaraan u wilt werken.

1. Selecteer **New Query** (Nieuwe query).

1. Voer een geldige query in en selecteer **query uitvoeren**.

1. Selecteer **query statistieken** om de werkelijke aanvraag kosten weer te geven voor de aanvraag die u hebt uitgevoerd.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Scherm opname van een aanvraag voor een MongoDB-query in de Azure Portal":::

### <a name="use-the-mongodb-net-driver"></a>Het MongoDB .NET-stuur programma gebruiken

Wanneer u het [officiële MongoDb .net-stuur programma](https://docs.mongodb.com/ecosystem/drivers/csharp/)gebruikt, kunt u opdrachten uitvoeren door de `RunCommand` methode aan te roepen voor een `IMongoDatabase` object. Voor deze methode is een implementatie van de `Command<>` abstracte klasse vereist:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Zie [Quick Start: een .net-Web-app bouwen met een Azure Cosmos DB-API voor MongoDb](create-mongodb-dotnet.md)voor meer informatie.

### <a name="use-the-mongodb-java-driver"></a>Het MongoDB Java-stuur programma gebruiken


Wanneer u het [officiële MongoDb Java-stuur programma](https://mongodb.github.io/mongo-java-driver/)gebruikt, kunt u opdrachten uitvoeren door de `runCommand` methode aan te roepen voor een `MongoDatabase` object:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Zie [Quick Start: een web-app bouwen met behulp van de Azure Cosmos DB-API voor MongoDb en de Java-SDK](create-mongodb-java.md)voor meer informatie.

### <a name="use-the-mongodb-nodejs-driver"></a>Het MongoDB-stuur programma voor Node.js gebruiken

Wanneer u het [officiële MongoDB Node.js-stuur programma](https://mongodb.github.io/node-mongodb-native/)gebruikt, kunt u opdrachten uitvoeren door de `command` methode aan te roepen voor een `db` object:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Zie [Quick Start: een bestaande MongoDB Node.js web-app migreren naar Azure Cosmos DB](create-mongodb-nodejs.md)voor meer informatie.

## <a name="cassandra-api"></a>Cassandra-API

Wanneer u bewerkingen uitvoert op de Azure Cosmos DB Cassandra-API, wordt de RU-kosten geretourneerd in de binnenkomende nettolading als een veld met de naam `RequestCharge` . U hebt meerdere mogelijkheden voor het ophalen van de RU-kosten.

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Wanneer u de [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)gebruikt, kunt u de binnenkomende Payload ophalen onder de `Info` eigenschap van een `RowSet` object:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Zie [Quick Start: een Cassandra-app bouwen met behulp van de .NET SDK en Azure Cosmos DB](create-cassandra-dotnet.md)voor meer informatie.

### <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Wanneer u de [Java-SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)gebruikt, kunt u de binnenkomende Payload ophalen door de `getExecutionInfo()` methode aan te roepen voor een `ResultSet` object:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Zie [Quick Start: een Cassandra-app bouwen met behulp van de Java-SDK en Azure Cosmos DB](create-cassandra-java.md)voor meer informatie.

## <a name="gremlin-api"></a>Gremlin-API

Wanneer u de Gremlin-API gebruikt, hebt u meerdere opties voor het vinden van het RU-verbruik voor een bewerking in een Azure Cosmos-container. 

### <a name="use-drivers-and-sdk"></a>Stuur Programma's en SDK gebruiken

Headers die door de Gremlin-API worden geretourneerd, worden toegewezen aan aangepaste status kenmerken, die momenteel worden opgehaald door de Gremlin .NET en Java SDK. De aanvraag kosten zijn beschikbaar onder de- `x-ms-request-charge` sleutel.

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Wanneer u de [Gremlin.NET-SDK](https://www.nuget.org/packages/Gremlin.Net/)gebruikt, zijn status kenmerken beschikbaar onder de `StatusAttributes` eigenschap van `ResultSet<>` het object:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Zie [Quick Start: een .NET Framework-of kern toepassing bouwen met behulp van een Azure Cosmos DB Gremlin-API-account](create-graph-dotnet.md)voor meer informatie.

### <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Wanneer u de [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)gebruikt, kunt u status kenmerken ophalen door de `statusAttributes()` methode aan te roepen voor het `ResultSet` object:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Zie [Quick Start: Create a Graph data base in azure Cosmos DB met behulp van de Java-SDK](create-graph-java.md)voor meer informatie.

## <a name="table-api"></a>Tabel-API

Op dit moment is de enige SDK die de RU-kosten voor tabel bewerkingen retourneert, de [.NET Standard-SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Het `TableResult` object beschrijft een `RequestCharge` eigenschap die door de SDK wordt ingevuld wanneer u deze gebruikt voor de Azure Cosmos db table-API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Zie [Quick Start: een Table-API-app bouwen met behulp van de .NET SDK en Azure Cosmos DB](create-table-dotnet.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](optimize-cost-queries.md)
* [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Door Voer voor een container inrichten](how-to-provision-container-throughput.md)
* [Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB](use-metrics.md)
