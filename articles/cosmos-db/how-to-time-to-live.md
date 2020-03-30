---
title: Tijd om te leven configureren en beheren in Azure Cosmos DB
description: Meer informatie over het configureren en beheren van de tijd om te leven in een container en een item in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: anfeldma
ms.openlocfilehash: 72653a3b28181316a2bf7dd7e73f2685c3afcf73
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384259"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Time to Live configureren in Azure Cosmos DB

In Azure Cosmos DB kunt u Time to Live (TTL) configureren op containerniveau, maar u kunt het ook overschrijven op een itemniveau nadat u het voor de container hebt ingesteld. U kunt TTL voor een container configureren via de Azure-portal of de taalspecifieke SDK's. Overschrijvingen van TTL op itemniveau kunnen worden geconfigureerd met behulp van de SDK's.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Time to Live inschakelen op een container via de Azure-portal

Gebruik de volgende stappen om Time to Live in te schakelen op een container zonder vervaldatum. Schakel deze optie in om TTL te overschrijven op itemniveau. U kunt de TTL ook instellen door een waarde (in seconden) ongelijk aan nul in te voeren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Maak een nieuw Azure Cosmos DB-account of selecteer een bestaand account.

3. Open het deelvenster **Data Explorer**.

4. Selecteer een bestaande container, vouw deze uit en wijzig de volgende waarden:

   * Open het venster **Schaal en instellingen**.
   * Onder **Instelling** zoekt u **Time to Live**.
   * Selecteer **On (no default)** of **Aan** en stel een TTL-waarde in
   * Klik op **Opslaan** om de wijzigingen op te slaan.

   ![Time to Live configureren in de Azure-portal](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Wanneer DefaultTimeToLive null is, is uw tijd om te leven uitgeschakeld
* Wanneer DefaultTimeToLive -1 is, is de instelling Time to Live ingeschakeld (Geen standaardinstelling)
* Wanneer DefaultTimeToLive een andere Int-waarde heeft (behalve 0), is de instelling Time to Live ingeschakeld

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Tijd inschakelen om op een container te leven met Azure CLI of PowerShell

Als u TTL op een container wilt maken of inschakelen, ziet u

* [Een container met TTL maken met Azure CLI](manage-with-cli.md#create-a-container-with-ttl)
* [Een container met TTL maken met PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Time to Live inschakelen op een container met behulp van de SDK

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-noexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-noexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-noexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Time to Live instellen op een container met behulp van de SDK

Als u de Time to Live voor een container wilt instellen, dient u een positief getal (geen nul) op te geven die de tijd in seconden aangeeft. Op basis van de geconfigureerde TTL-waarde worden alle items in de container verwijderd na het laatste gewijzigde tijdstempel `_ts` van het item.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-enable-withexpiry"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-defaultexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-defaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="set-time-to-live-on-an-item"></a>Geldigheidsduur voor een item instellen

U kunt niet alleen de standaardwaarde voor Time to Live voor een container instellen, maar ook voor een item. Als u Time to Live op itemniveau instelt, wordt de standaard-TL van het item in die container overschreven.

* Als u de TTL voor een item instelt, dient u een dient u een positief getal (geen nul) op te geven die de tijd in seconden aangeeft. Hiermee wordt aangegeven dat het item vervalt na het laatste gewijzigde tijdstempel `_ts` van het item.

* Als het item geen TTL-veld heeft, wordt standaard de TTL-waarde die voor de container is ingesteld, op het item toegepast.

* Als TTL wordt uitgeschakeld op containerniveau, wordt het TTL-veld van het item genegeerd totdat TTL opnieuw voor de container wordt ingeschakeld.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure-portal

Gebruik de volgende stappen om de tijd om van een item te leven in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Maak een nieuw Azure Cosmos DB-account of selecteer een bestaand account.

3. Open het deelvenster **Data Explorer**.

4. Selecteer een bestaande container, vouw deze uit en wijzig de volgende waarden:

   * Open het venster **Schaal en instellingen**.
   * Onder **Instelling** zoekt u **Time to Live**.
   * Selecteer **Aan (geen standaard)** of selecteer **Aan** en stel een TTL-waarde in. 
   * Klik op **Opslaan** om de wijzigingen op te slaan.

5. Navigeer vervolgens naar het item waarvoor u de tijd `ttl` wilt instellen om te wonen, voeg de eigenschap toe en selecteer **Bijwerken**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (elke)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

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

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

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
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

## <a name="reset-time-to-live"></a>Time to Live opnieuw instellen

U kunt Time to Live opnieuw voor een item instellen door een schrijf- of bijwerkbewerking voor het item uit te voeren. Met de schrijf- of bijwerkbewerking wordt `_ts` ingesteld op de huidige tijd en de TTL voor dat item begint van voren af aan. Als u de TTL van een item wilt wijzigen, kunt u het veld op dezelfde manier bijwerken als elk ander veld.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-extend-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-modifyitemexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-modifyitemexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

## <a name="turn-off-time-to-live"></a>Time to Live uitzetten

Als Time to Live voor een item is ingesteld en u wilt dat item niet langer laten verlopen, dan haalt u dat item op, verwijdert u het TTL-veld en vervangt u het item op de server. Als het TTL-veld van het item wordt verwijderd, wordt de standaardwaarde van de TTL die aan de container is toegewezen, op het item toegepast. Stel de TTL-waarde in op -1 om te voorkomen dat een item verloopt en dat de TTL-waarde van de container wordt overgenomen.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-turn-off-ttl-item"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-itemdefaultexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-itemdefaultexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```

## <a name="disable-time-to-live"></a>Time to Live uitschakelen

Als u Time to Live voor een container wilt uitschakelen en het achtergrondproces dat op verlopen items controleert, wilt stoppen, moet eigenschap `DefaultTimeToLive` op de container worden verwijderd. Het verwijderen van deze eigenschap is iets anders dan het instellen ervan op -1. Als u de eigenschap op -1 instelt, zullen nieuwe items die aan de container worden toegevoegd, nooit verlopen. U kunt deze waarde echter voor bepaalde items in de container overschrijven. Wanneer u de eigenschap TTL uit de container verwijdert, vervallen de items nooit, zelfs als er sprake is van een eerdere standaardTTL-waarde.

### <a name="net-sdk-v2-microsoftazuredocumentdb"></a><a id="dotnet-disable-ttl"></a>.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a name="net-sdk-v3-microsoftazurecosmos"></a><a id="dotnet-disable-ttl"></a>.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-enable-disableexpiry"></a>Java SDK V4 (Maven com.azure:azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

### <a name="java-sdk-v3-maven-commicrosoftazureazure-cosmos"></a><a id="java3-enable-disableexpiry"></a>Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Time to Live vindt u in het volgende artikel:

* [Tijd om te leven](time-to-live.md)
