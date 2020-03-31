---
title: Configureren voor meerdere masters in Azure Cosmos DB
description: Meer informatie over het configureren van multi-master voor uw toepassingen met behulp van verschillende SDK's in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873638"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Multimaster configureren in uw toepassingen die Azure Cosmos DB gebruiken

Zodra een account is gemaakt met meerdere schrijfregio's ingeschakeld, moet u twee wijzigingen aanbrengen in uw toepassing in het verbindingsbeleid voor de DocumentClient om de multi-master- en multi-homing-mogelijkheden in Azure Cosmos DB in te schakelen. Stel useMultipleWriteLocations in in het verbindingsbeleid in op true en geef de naam door van het gebied waar de toepassing wordt geïmplementeerd op SetCurrentLocation. Hiermee wordt de eigenschap Voorkeurslocaties gevuld op basis van de geo-nabijheid van de doorgegeven locatie. Als een nieuwe regio later aan het account wordt toegevoegd, hoeft de toepassing niet te worden bijgewerkt of opnieuw te worden geïmplementeerd, detecteert deze automatisch de dichtere regio en wordt deze automatisch weergegeven als er een regionale gebeurtenis plaatsvindt.

> [!Note]
> Cosmos-accounts die in eerste instantie zijn geconfigureerd met één schrijfgebied, kunnen worden geconfigureerd voor meerdere schrijfregio's (d.w.z. multi-master) met nul downtime. Voor meer informatie, [meerdere schrijfgebieden configureren](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Als u multi-master in `UseMultipleWriteLocations` uw `true`toepassing wilt inschakelen, stelt u in op . Ook ingesteld `SetCurrentLocation` op het gebied waarin de toepassing wordt geïmplementeerd en waar Azure Cosmos DB wordt gerepliceerd:

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

Als u multimaster in uw `ApplicationRegion` toepassing wilt inschakelen, stelt u in op het gebied waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Optioneel u de `CosmosClientBuilder` `WithApplicationRegion` en om hetzelfde resultaat te bereiken gebruiken:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Als u multimaster in uw `policy.setUsingMultipleWriteLocations(true)` toepassing `policy.setPreferredLocations` wilt inschakelen, stelt u in en stel u in op het gebied waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript en TypeScript SDKs

Als u multi-master in `connectionPolicy.UseMultipleWriteLocations` uw `true`toepassing wilt inschakelen, stelt u in op . Ook ingesteld `connectionPolicy.PreferredLocations` op het gebied waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd:

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

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Als u multi-master in `connection_policy.UseMultipleWriteLocations` uw `true`toepassing wilt inschakelen, stelt u in op . Ook ingesteld `connection_policy.PreferredLocations` op het gebied waarin de toepassing wordt geïmplementeerd en waar Cosmos DB wordt gerepliceerd.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen:

* [Sessietokens gebruiken om consistentie in Azure Cosmos DB te beheren](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflictsoorten en oplossingsbeleid in Azure Cosmos DB](conflict-resolution-policies.md)
* [Hoge beschikbaarheid in Azure Cosmos DB](high-availability.md)
* [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)
* [Kies het juiste consistentieniveau in Azure Cosmos DB](consistency-levels-choosing.md)
* [Consistentie- en beschikbaarheidsafwegingen in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
* [Wereldwijd schalen van ingerichte doorvoer](scaling-throughput.md)
* [Globale distributie: Onder de motorkap](global-dist-under-the-hood.md)
