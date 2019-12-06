---
title: Configureren voor meerdere masters in Azure Cosmos DB
description: Meer informatie over het configureren van multi-master voor uw toepassingen met behulp van verschillende Sdk's in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873638"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Multi-Master configureren in uw toepassingen die gebruikmaken van Azure Cosmos DB

Als een account is gemaakt terwijl meerdere schrijf regio's zijn ingeschakeld, moet u twee wijzigingen aanbrengen in uw toepassing in de Connection Policy voor de DocumentClient om de mogelijkheden voor meerdere masters en multi-multihoming in te scha kelen in Azure Cosmos DB. In de Connection Policy stelt u UseMultipleWriteLocations in op True en geeft u de naam van de regio waar de toepassing wordt geïmplementeerd naar SetCurrentLocation. Hiermee wordt de eigenschap PreferredLocations ingevuld op basis van de geografische nabijheid van de locatie die wordt door gegeven in. Als er later een nieuwe regio wordt toegevoegd aan het account, de toepassing niet hoeft te worden bijgewerkt of opnieuw wordt geïmplementeerd, wordt de regio dichter automatisch gedetecteerd en wordt er automatisch een regionale gebeurtenis weer gegeven.

> [!Note]
> Cosmos-accounts die in eerste instantie zijn geconfigureerd met één schrijf regio kunnen worden geconfigureerd voor meerdere schrijf regio's (dat wil zeggen multi-master) met een nul-tijd. Zie [meerdere regio's configureren](how-to-manage-database-account.md#configure-multiple-write-regions) voor meer informatie.

## <a id="netv2"></a>.NET SDK v2

Als u meerdere masters in uw toepassing wilt inschakelen, stelt u `UseMultipleWriteLocations` in op `true`. Stel ook `SetCurrentLocation` in op de regio waarin de toepassing wordt geïmplementeerd en waar Azure Cosmos DB wordt gerepliceerd:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3

Als u meerdere masters in uw toepassing wilt inschakelen, stelt u `ApplicationRegion` in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

U kunt eventueel de `CosmosClientBuilder` en `WithApplicationRegion` gebruiken om hetzelfde resultaat te krijgen:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>Java Async SDK

Als u meerdere masters in uw toepassing wilt inschakelen, stelt u `policy.setUsingMultipleWriteLocations(true)` in en stelt u `policy.setPreferredLocations` in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

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

## <a id="javascript"></a>Node. js-, java script-en type script-Sdk's

Als u meerdere masters in uw toepassing wilt inschakelen, stelt u `connectionPolicy.UseMultipleWriteLocations` in op `true`. Stel ook `connectionPolicy.PreferredLocations` in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

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

## <a id="python"></a>Python SDK

Als u meerdere masters in uw toepassing wilt inschakelen, stelt u `connection_policy.UseMultipleWriteLocations` in op `true`. Stel ook `connection_policy.PreferredLocations` in op de regio waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd.

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
* [Compromissen tussen consistentie, beschikbaarheid en prestaties in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Beschikbaarheid en prestaties van optimalisatie voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
* [Wereld wijd schalen van ingerichte door Voer](scaling-throughput.md)
* [Wereld wijde distributie: onder de motorkap](global-dist-under-the-hood.md)
