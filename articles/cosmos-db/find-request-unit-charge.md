---
title: Ru-kosten (request unit) zoeken in Azure Cosmos DB
description: Meer informatie over het vinden van de ru-kosten (request unit) voor elke bewerking die wordt uitgevoerd met een Azure Cosmos-container.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585897"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>De kosten voor aanvraageenheden zoeken in Azure Cosmos DB

In dit artikel worden de verschillende manieren gepresenteerd waarop u het RU-verbruik [(request unit)](request-units.md) vinden voor elke bewerking die wordt uitgevoerd tegen een container in Azure Cosmos DB. Momenteel u dit verbruik alleen meten met behulp van de Azure-portal of door het antwoord te inspecteren dat is teruggestuurd van Azure Cosmos DB via een van de SDK's.

## <a name="sql-core-api"></a>SQL (Core) API

Als u de SQL API gebruikt, hebt u meerdere opties voor het zoeken naar het RU-verbruik voor een bewerking met een Azure Cosmos-container.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Momenteel u de aanvraagkosten alleen in de Azure-portal vinden voor een SQL-query.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account) en voer het met gegevens, of selecteer een bestaand Azure Cosmos-account dat al gegevens bevat.

1. Ga naar het deelvenster **Gegevensverkenner** en selecteer de container waaraan u wilt werken.

1. Selecteer **New SQL Query**.

1. Voer een geldige query in en selecteer **Query uitvoeren**.

1. Selecteer **Querystatistieken** om de werkelijke aanvraagkosten weer te geven voor het verzoek dat u hebt uitgevoerd.

![Schermafbeelding van een SQL-queryaanvraagdie in de Azure-portal in rekening wordt gebracht](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken
### <a name="net-v2-sdk"></a>.Net V2 SDK

Objecten die worden geretourneerd vanuit de [.NET SDK v2,](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) geven een `RequestCharge` eigenschap bloot:

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

Objecten die worden geretourneerd vanuit de [.NET SDK v3,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) geven een `RequestCharge` eigenschap bloot:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Zie [Quickstart: Een .NET-webapp bouwen met een SQL API-account in Azure Cosmos DB](create-sql-api-dotnet.md)voor meer informatie.

### <a name="use-the-java-sdk"></a>Gebruik de Java SDK

Objecten die worden geretourneerd vanuit `getRequestCharge()` de [Java SDK,](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) stellen een methode bloot:

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

Zie [Quickstart: Een Java-toepassing bouwen met een Azure Cosmos DB SQL API-account](create-sql-api-java.md)voor meer informatie.

### <a name="use-the-nodejs-sdk"></a>Gebruik de Node.js SDK

Objecten die worden geretourneerd van de SDK `headers` [Node.js,](https://www.npmjs.com/package/@azure/cosmos) tonen een subobject dat alle kopteksten in kaart brengt die door de onderliggende HTTP-API zijn geretourneerd. De aanvraagkosten zijn `x-ms-request-charge` beschikbaar onder de sleutel:

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

Zie [Quickstart: Een Node.js-app bouwen met een Azure Cosmos DB SQL API-account](create-sql-api-nodejs.md)voor meer informatie. 

### <a name="use-the-python-sdk"></a>De Python SDK gebruiken

Het `CosmosClient` object van de Python `last_response_headers` [SDK](https://pypi.org/project/azure-cosmos/) onthult een woordenboek dat alle kopteksten die door de onderliggende HTTP-API zijn geretourneerd, in kaart brengt voor de laatste bewerking die is uitgevoerd. De aanvraagkosten zijn `x-ms-request-charge` beschikbaar onder de sleutel:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Zie [Snelstart: Een Python-app bouwen met een Azure Cosmos DB SQL API-account](create-sql-api-python.md)voor meer informatie. 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

De RU-lading wordt weergegeven door `getLastRequestStatistics`een aangepaste [databaseopdracht](https://docs.mongodb.com/manual/reference/command/) met de naam . De opdracht retourneert een document met de naam van de laatste bewerking die is uitgevoerd, de aanvraagkosten en de duur ervan. Als u de Azure Cosmos DB API voor MongoDB gebruikt, hebt u meerdere opties voor het ophalen van de RU-kosten.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Momenteel u de aanvraagkosten alleen in de Azure-portal vinden voor een query.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account) en voer het met gegevens, of selecteer een bestaand account dat al gegevens bevat.

1. Ga naar het deelvenster **Gegevensverkenner** en selecteer de container waaraan u wilt werken.

1. Selecteer **New Query** (Nieuwe query).

1. Voer een geldige query in en selecteer **Query uitvoeren**.

1. Selecteer **Querystatistieken** om de werkelijke aanvraagkosten weer te geven voor het verzoek dat u hebt uitgevoerd.

![Schermafbeelding van een kosten van een MongoDB-queryaanvraag in de Azure-portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>De MongoDB .NET-driver gebruiken

Wanneer u het [officiële MongoDB .NET-stuurprogramma gebruikt,](https://docs.mongodb.com/ecosystem/drivers/csharp/) `RunCommand` kunt u `IMongoDatabase` opdrachten uitvoeren door de methode op een object aan te roepen. Deze methode vereist een `Command<>` implementatie van de abstracte klasse:

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

Zie [Quickstart: Een .NET-webapp bouwen met een Azure Cosmos DB API voor MongoDB voor](create-mongodb-dotnet.md)meer informatie.

### <a name="use-the-mongodb-java-driver"></a>Gebruik de MongoDB Java driver


Wanneer u de [officiële MongoDB Java-driver gebruikt,](https://mongodb.github.io/mongo-java-driver/) `runCommand` kunt u `MongoDatabase` opdrachten uitvoeren door de methode op een object aan te roepen:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Zie [Quickstart: Een web-app bouwen met de Azure Cosmos DB API voor MongoDB en de Java SDK voor](create-mongodb-java.md)meer informatie.

### <a name="use-the-mongodb-nodejs-driver"></a>Gebruik de MongoDB Node.js driver

Wanneer u het [officiële MongoDB Node.js-stuurprogramma](https://mongodb.github.io/node-mongodb-native/)gebruikt, `command` kunt u `db` opdrachten uitvoeren door de methode op een object aan te roepen:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Zie [Snelstart: Een bestaande Web-app MongoDB Node.js migreren naar Azure Cosmos DB](create-mongodb-nodejs.md)voor meer informatie.

## <a name="cassandra-api"></a>Cassandra-API

Wanneer u bewerkingen uitvoert met de Azure Cosmos DB Cassandra API, wordt `RequestCharge`de RU-kosten geretourneerd in de binnenkomende payload als een veld met de naam . U hebt meerdere mogelijkheden voor het ophalen van de RU-kosten.

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Wanneer u de [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)gebruikt, u `Info` de `RowSet` binnenkomende payload onder de eigenschap van een object ophalen:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Zie [Snelstart: Een Cassandra-app bouwen met de .NET SDK en Azure Cosmos DB](create-cassandra-dotnet.md)voor meer informatie.

### <a name="use-the-java-sdk"></a>Gebruik de Java SDK

Wanneer u de [Java SDK gebruikt,](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)u `getExecutionInfo()` de binnenkomende payload ophalen door de methode op een `ResultSet` object aan te roepen:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Zie [Quickstart: Een Cassandra-app bouwen met de Java SDK en Azure Cosmos DB](create-cassandra-java.md)voor meer informatie.

## <a name="gremlin-api"></a>Gremlin-API

Wanneer u de Gremlin API gebruikt, hebt u meerdere opties voor het vinden van het RU-verbruik voor een bewerking met een Azure Cosmos-container. 

### <a name="use-drivers-and-sdk"></a>Stuurprogramma's en SDK gebruiken

Kopteksten die worden geretourneerd door de Gremlin-API worden toegewezen aan aangepaste statuskenmerken, die momenteel worden opgedoken door de Gremlin .NET en Java SDK. De aanvraagkosten zijn `x-ms-request-charge` beschikbaar onder de sleutel.

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Wanneer u de [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)gebruikt, zijn `StatusAttributes` statuskenmerken `ResultSet<>` beschikbaar onder de eigenschap van het object:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Zie [Quickstart: Een .NET Framework- of Core-toepassing bouwen met een Azure Cosmos DB Gremlin API-account](create-graph-dotnet.md)voor meer informatie.

### <a name="use-the-java-sdk"></a>Gebruik de Java SDK

Wanneer u de [Gremlin Java SDK gebruikt,](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) `statusAttributes()` u statuskenmerken ophalen door de methode op het `ResultSet` object aan te roepen:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Zie [Snelstart: Een grafiekdatabase maken in Azure Cosmos DB met behulp van de Java SDK](create-graph-java.md)voor meer informatie.

## <a name="table-api"></a>Tabel-API

Momenteel is de enige SDK die de RU-kosten voor tabelbewerkingen retourneert de [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Het `TableResult` object stelt `RequestCharge` een eigenschap bloot die wordt ingevuld door de SDK wanneer u deze gebruikt tegen de Azure Cosmos DB Table API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Zie [Snelstart: Een tabel-API-app bouwen met de .NET SDK en Azure Cosmos DB](create-table-dotnet.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](optimize-cost-queries.md)
* [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Voorzieningsdoorvoer voor een container](how-to-provision-container-throughput.md)
* [Controleren en debuggen met statistieken in Azure Cosmos DB](use-metrics.md)
