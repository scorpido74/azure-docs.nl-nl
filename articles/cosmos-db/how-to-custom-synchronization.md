---
title: Aangepaste synchronisatie implementeren voor het optimaliseren voor hogere beschikbaarheid en prestaties in Azure Cosmos DB
description: Meer informatie over het implementeren van aangepaste synchronisatie om te optimaliseren voor betere Beschik baarheid en prestaties in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 8fce14496b9f8fa17f2dbfd04b7ea42f1495a8a9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093339"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Aangepaste synchronisatie implementeren voor betere Beschik baarheid en prestaties

Azure Cosmos DB biedt [vijf goed gedefinieerde consistentie niveaus](consistency-levels.md) om te kiezen uit de verhouding tussen consistentie, prestaties en beschik baarheid. Sterke consistentie helpt ervoor te zorgen dat gegevens synchroon worden gerepliceerd en blijvend behouden in elke regio waar het Azure Cosmos-account beschikbaar is. Deze configuratie biedt het hoogste niveau van duurzaamheid, maar is gebaseerd op de kosten van prestaties en beschik baarheid. Als u wilt dat uw toepassing de duurzaamheid van gegevens beheert of verlaagt aan de vereisten van de toepassing zonder dat de beschik baarheid in gevaar komt, kunt u *Aangepaste synchronisatie* op de toepassingslaag gebruiken om het gewenste niveau van duurzaamheid te krijgen.

In de volgende afbeelding wordt het aangepaste synchronisatie model visueel weer gegeven:

![Aangepaste synchronisatie](./media/how-to-custom-synchronization/custom-synchronization.png)

In dit scenario wordt een Azure Cosmos-container wereld wijd gerepliceerd in meerdere regio's op meerdere continenten. Het gebruik van sterke consistentie voor alle regio's in dit scenario is van invloed op de prestaties. De toepassing kan twee clients gebruiken die hetzelfde [sessie token](how-to-manage-consistency.md#utilize-session-tokens)delen, om ervoor te zorgen dat een hoger niveau van de gegevens duurzaamheid zonder de schrijf latentie in gevaar heeft.

De eerste client kan gegevens schrijven naar de lokale regio (bijvoorbeeld US - west). De tweede client (bijvoorbeeld in VS Oost) is een lees-client die wordt gebruikt om synchronisatie te garanderen. Door het sessie token uit te stromen van de schrijf reactie op het volgende lees proces, zorgt het lezen ervoor dat de schrijf bewerkingen naar VS Oost worden gesynchroniseerd. Azure Cosmos DB zorgt ervoor dat schrijf bewerkingen ten minste één regio worden weer gegeven. Ze zijn gegarandeerd een regionale storing te overleven als de oorspronkelijke schrijf regio uitvalt. In dit scenario wordt elke schrijf bewerking gesynchroniseerd naar VS-Oost, waardoor de latentie van het gebruik van sterke consistentie in alle regio's wordt gereduceerd. In een scenario met meerdere masters waarbij schrijf bewerkingen in elke regio worden uitgevoerd, kunt u dit model uitbreiden om te synchroniseren met meerdere regio's parallel.

## <a name="configure-the-clients"></a>De clients configureren

In het volgende voor beeld ziet u een Gegevenstoegangslaag voor gegevens toegang die twee clients voor aangepaste synchronisatie instantieert:

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3-SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Aangepaste synchronisatie implementeren

Nadat de clients zijn geïnitialiseerd, kan de toepassing schrijf bewerkingen naar de lokale regio (VS West) uitvoeren en de schrijf bewerkingen naar VS-Oost als volgt afdwingen.

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3-SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

U kunt het model uitbreiden om te synchroniseren met meerdere regio's parallel.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over globale distributie en consistentie in Azure Cosmos DB:

* [Kies het juiste consistentie niveau in Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromissen tussen consistentie, beschikbaarheid en prestaties in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Consistentie in Azure Cosmos DB beheren](how-to-manage-consistency.md)
* [Partitioneren en gegevensdistributie in Azure Cosmos DB](partition-data.md)
