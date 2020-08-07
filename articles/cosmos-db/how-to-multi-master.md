---
title: Configureren voor meerdere masters in Azure Cosmos DB
description: Meer informatie over het configureren van multi-master voor uw toepassingen met behulp van verschillende Sdk's in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-javascript
ms.openlocfilehash: 2cf6d2c02beff93553944474138bee3a2a316aa9
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874347"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Multi-Master configureren in uw toepassingen die gebruikmaken van Azure Cosmos DB

Als een account is gemaakt terwijl meerdere schrijf regio's zijn ingeschakeld, moet u twee wijzigingen aanbrengen in uw toepassing in de Connection Policy voor de DocumentClient om de mogelijkheden voor meerdere masters en multi-multihoming in te scha kelen in Azure Cosmos DB. In de Connection Policy stelt u UseMultipleWriteLocations in op True en geeft u de naam van de regio waar de toepassing wordt geïmplementeerd naar SetCurrentLocation. Hiermee wordt de eigenschap PreferredLocations ingevuld op basis van de geografische nabijheid van de locatie die wordt door gegeven in. Als er later een nieuwe regio wordt toegevoegd aan het account, de toepassing niet hoeft te worden bijgewerkt of opnieuw wordt geïmplementeerd, wordt de regio dichter automatisch gedetecteerd en wordt er automatisch een regionale gebeurtenis weer gegeven.

> [!Note]
> Cosmos-accounts die in eerste instantie zijn geconfigureerd met één schrijf regio kunnen worden geconfigureerd voor meerdere schrijf regio's (dat wil zeggen multi-master) met een nul-tijd. Zie [meerdere regio's configureren](how-to-manage-database-account.md#configure-multiple-write-regions) voor meer informatie.

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Stel in op om meerdere masters in uw toepassing in te scha kelen `UseMultipleWriteLocations` `true` . Stel ook `SetCurrentLocation` de regio in waarin de toepassing wordt geïmplementeerd en waar Azure Cosmos DB worden gerepliceerd:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Als u meerdere masters in uw toepassing wilt inschakelen, stelt `ApplicationRegion` u de regio in waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

U kunt ook de gebruiken `CosmosClientBuilder` `WithApplicationRegion` om hetzelfde resultaat te krijgen:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a> Java V4 SDK

Als u meerdere masters in uw toepassing wilt inschakelen, roept u `.multipleWriteRegionsEnabled(true)` de client-opbouw functie op en gebruikt u deze `.preferredRegions(preferredRegions)` in `preferredRegions` een `List` container met één element. Dit is de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a>Asynchrone Java v2 SDK

De Java v2 SDK heeft de Maven [com. micro soft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)gebruikt. Als u meerdere masters in uw toepassing wilt inschakelen, stelt `policy.setUsingMultipleWriteLocations(true)` u in en stelt `policy.setPreferredLocations` u deze in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, java script en type script Sdk's

Stel in op om meerdere masters in uw toepassing in te scha kelen `connectionPolicy.UseMultipleWriteLocations` `true` . Stel ook `connectionPolicy.PreferredLocations` de regio in waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python-SDK

Stel in op om meerdere masters in uw toepassing in te scha kelen `connection_policy.UseMultipleWriteLocations` `true` . Stel ook `connection_policy.PreferredLocations` de regio in waarin de toepassing wordt geïmplementeerd en waar Cosmos DB worden gerepliceerd.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen:

* [Sessie tokens gebruiken voor het beheren van consistentie in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflictsoorten en oplossingsbeleid in Azure Cosmos DB](conflict-resolution-policies.md)
* [Hoge beschikbaarheid in Azure Cosmos DB](high-availability.md)
* [Consistentie niveaus in Azure Cosmos DB](consistency-levels.md)
* [Kies het juiste consistentie niveau in Azure Cosmos DB](consistency-levels-choosing.md)
* [Consistentie, Beschik baarheid en prestatie afweging in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Beschik baarheid en prestaties voor diverse consistentie niveaus](consistency-levels-tradeoffs.md)
* [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
* [Wereld wijde distributie: onder de motorkap](global-dist-under-the-hood.md)
