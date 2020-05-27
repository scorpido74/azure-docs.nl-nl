---
title: Migreer uw toepassing om de Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos) te gebruiken
description: Leer hoe u uw bestaande Java-toepassing bijwerkt van met de oudere Azure Cosmos DB Java-Sdk's naar het nieuwere Java SDK 4,0 (com. Azure. Cosmos-pakket) voor Core-API (SQL).
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/26/2020
ms.reviewer: sngun
ms.openlocfilehash: d7028018501c5e6580d7345938a739ccc983ff48
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873262"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Uw toepassing migreren om de Azure Cosmos DB Java SDK v4 te gebruiken

> [!IMPORTANT]  
> Raadpleeg voor meer informatie over deze SDK de Azure Cosmos DB Java SDK v4- [release opmerkingen](sql-api-sdk-java-v4.md), de [maven-opslag plaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)en Azure Cosmos DB Java SDK v4 [Troubleshooting Guide (Engelstalig](troubleshoot-java-sdk-v4-sql.md)).
>

In dit artikel wordt uitgelegd hoe u een upgrade uitvoert van uw bestaande Java-toepassing die gebruikmaakt van een oudere Azure Cosmos DB Java-SDK naar de nieuwere Azure Cosmos DB Java SDK 4,0 voor Core-API (SQL). Azure Cosmos DB Java SDK v4 correspondeert met het `com.azure.cosmos` pakket. U kunt de instructies in dit document gebruiken als u uw toepassing migreert van een van de volgende Azure Cosmos DB Java-Sdk's: 

* Java SDK 2. x. x synchroniseren
* Asynchrone Java SDK 2. x. x
* Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB en pakket toewijzingen van de Java-SDK

De volgende tabel geeft een lijst van verschillende Azure Cosmos DB Java-Sdk's, de pakket naam en de release-informatie:

| Java-SDK| Release datum | Gebundelde Api's   | Maven jar  | Naam Java-pakket  |API-naslaginformatie   | Opmerkingen bij de release  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Juni 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Release opmerkingen](sql-api-sdk-async-java.md) |
| Synchronisatie 2. x. x     | Sept 2018    | Synchroniseren   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Release opmerkingen](sql-api-sdk-java.md)  |
| 3. x. x    | Juli 2019    | Async (reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | April 2020   | Async (reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Wijzigingen in de implementatie van het SDK-niveau

Hier volgen de belangrijkste implementatie verschillen tussen de verschillende Sdk's:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava wordt vervangen door reactor in Azure Cosmos DB Java SDK versie 3. x. x en 4,0

Als u niet bekend bent met asynchrone programmering of reactieve programmering, raadpleegt u de [hand leiding voor het reactor patroon](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) voor een inleiding tot async-programmering en project reactor. Deze hand leiding kan nuttig zijn als u Azure Cosmos DB Sync Java SDK 2. x. x of Azure Cosmos DB Java SDK 3. x. x Sync API in het verleden hebt gebruikt.

Als u Azure Cosmos DB async-SDK 2. x. x hebt gebruikt en u van plan bent om te migreren naar de 4,0 SDK, raadpleegt u de [hand leiding voor reactor en RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) voor richt lijnen voor het converteren van RxJava-code voor het gebruik van reactor.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 de modus directe connectiviteit heeft in zowel async-als synchronisatie-Api's

Als u Azure Cosmos DB Sync Java SDK 2. x. x gebruikt, moet u er rekening mee houden dat de directe verbindings modus op basis van TCP (in tegens telling tot HTTP) is geïmplementeerd in Azure Cosmos DB Java SDK 4,0 voor zowel de async-als de synchronisatie-Api's.

## <a name="api-level-changes"></a>Wijzigingen in het API-niveau

Hieronder vindt u een overzicht van de API-wijzigingen in Azure Cosmos DB Java SDK 4. x. x vergeleken met de vorige Sdk's (Java SDK 3. x. x, async Java SDK 2. x. x en Java SDK 2. x. x synchroniseren):

![Naamgevings conventies voor Azure Cosmos DB Java SDK](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* De Azure Cosmos DB Java SDK 3. x. x en 4,0 verwijzen naar de client resources als `Cosmos<resourceName>` . Bijvoorbeeld,, `CosmosClient` `CosmosDatabase` , `CosmosContainer` . Overwegende dat in versie 2. x. x de Azure Cosmos DB Java-Sdk's geen uniform naam schema hebben.

* Azure Cosmos DB Java SDK 3. x. x en 4,0 bieden synchronisatie-en async-Api's.

  * **Java SDK 4,0** : alle klassen behoren tot de API Sync, tenzij de naam van de klasse wordt toegevoegd `Async` `Cosmos` .

  * **Java SDK 3. x. x**: alle klassen behoren tot de ASYNC-API tenzij de naam van de klasse wordt toegevoegd `Async` `Cosmos` .

  * **Asynchrone Java SDK 2. x. x**: de namen van klassen zijn vergelijkbaar met het synchroniseren van Java SDK 2. x. x, maar de naam begint met *async*.

### <a name="hierarchical-api-structure"></a>Hiërarchische API-structuur

Azure Cosmos DB Java SDK 4,0 en 3. x. x introduceren een hiërarchische API-structuur waarmee de clients, data bases en containers op een geneste manier worden georganiseerd, zoals wordt weer gegeven in het volgende SDK-code fragment van 4,0:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

In versie 2. x. x van de Azure Cosmos DB Java SDK worden alle bewerkingen voor resources en documenten uitgevoerd via het client exemplaar.

### <a name="representing-documents"></a>Documenten vertegenwoordigen

In Azure Cosmos DB Java SDK 4,0 zijn aangepaste POJO en `JsonNodes` de twee opties om de documenten te lezen en te schrijven vanuit Azure Cosmos db.

In de Azure Cosmos DB Java SDK 3. x. x wordt het `CosmosItemProperties` object beschikbaar gemaakt door de open bare API en geleverd als een document weergave. Deze klasse wordt niet meer openbaar weer gegeven in versie 4,0.

### <a name="imports"></a>Rusland

* De Azure Cosmos DB Java SDK 4,0-pakketten beginnen met`com.azure.cosmos`
  * Azure Cosmos DB Java SDK 3. x. x-pakketten beginnen met`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4,0 worden verschillende klassen in een genest pakket geplaatst `com.azure.cosmos.models` . Enkele van deze pakketten zijn:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * De asynchrone API analoog voor alle bovenstaande pakketten
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... Heap.

### <a name="accessors"></a>Accessors

Azure Cosmos DB Java SDK 4,0 bevat `get` en `set` methoden om toegang te krijgen tot de exemplaar leden. Bijvoorbeeld het `CosmosContainer` exemplaar heeft en- `container.getId()` `container.setId()` methoden.

Dit wijkt af van Azure Cosmos DB Java SDK 3. x. x, waarmee een fluent interface wordt weer gegeven. Bijvoorbeeld: een `CosmosSyncContainer` exemplaar `container.id()` dat is overbelast om de waarde op te halen of in te stellen `id` .

## <a name="code-snippet-comparisons"></a>Vergelijking van code fragmenten

### <a name="create-resources"></a>Resources maken

Het volgende code fragment toont de verschillen in de manier waarop resources worden gemaakt tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Item bewerkingen

Het volgende code fragment toont de verschillen in hoe item bewerkingen worden uitgevoerd tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indexeren

Het volgende code fragment toont de verschillen in de manier waarop indexering wordt gemaakt tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Opgeslagen procedures

Het volgende code fragment toont de verschillen in de manier waarop opgeslagen procedures worden gemaakt tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Feed wijzigen

Het volgende code fragment toont de verschillen in de manier waarop wijzigings feed bewerkingen worden uitgevoerd tussen de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Time-to-Live (TTL) op container niveau

Het volgende code fragment toont de verschillen in het maken van time-to-Live voor gegevens in de container met behulp van de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Item niveau time-to-Live (TTL)

Het volgende code fragment toont de verschillen in het maken van time-to-Live voor een item met behulp van de 4,0-en 3. x. x async-Api's:

# <a name="java-sdk-40-async-api"></a>[Asynchrone API voor Java SDK 4,0](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x async-API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Volgende stappen

* [Een Java-app bouwen](create-sql-api-java.md) om Azure Cosmos DB SQL-API-gegevens te beheren met de v4-SDK
* Meer informatie over de [op reactor gebaseerde Java-sdk's](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Meer informatie over het converteren van RxJava async-code naar een reactor async-code met de [hand leiding voor reactor tegenover RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)