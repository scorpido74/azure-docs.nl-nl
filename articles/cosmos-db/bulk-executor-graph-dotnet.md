---
title: De graph bulk executor .NET-bibliotheek gebruiken met Azure Cosmos DB Gremlin API
description: Meer informatie over het gebruik van de bulkexecutorbibliotheek om grafiekgegevens massaal te importeren in een Azure Cosmos DB Gremlin-API-container.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.openlocfilehash: adf512fc521ef553f0bbd6ef6dd8ee19e398b37b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982700"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>De graph bulk executor .NET-bibliotheek gebruiken om bulkbewerkingen uit te voeren in Azure Cosmos DB Gremlin API

Deze zelfstudie bevat instructies over het gebruik van de bulkexecutor .NET-bibliotheek van Azure CosmosDB om grafiekobjecten te importeren en bij te werken in een Azure Cosmos DB Gremlin-API-container. Dit proces maakt gebruik van de klasse Graph in de [bulkexecutor-bibliotheek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) om Vertex- en Edge-objecten programmatisch te maken om er vervolgens meerdere per netwerkaanvraag in te voegen. Dit gedrag kan worden geconfigureerd via de bulkexecutorbibliotheek om optimaal gebruik te maken van zowel database- als lokale geheugenbronnen.

In tegenstelling tot het verzenden van Gremlin-query's naar een database, waarbij de opdracht wordt geëvalueerd en vervolgens één voor één wordt uitgevoerd, moet de objecten lokaal worden gemaakt en gevalideerd met behulp van de bibliotheek voor bulkuitvoerfouten. Als de objecten zijn gemaakt, kunt u opeenvolgend grafen naar de databaseservice verzenden. Dankzij deze methode kunnen snelheden van gegevensopnames met een factor 100 worden verhoogd. Daardoor is het een ideale methode voor initiële gegevensmigraties of periodieke gegevensverplaatsingen. Ga naar de GitHub-pagina van de [voorbeeldtoepassing Azure Cosmos DB Graph voor bulkuitvoer.](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)

## <a name="bulk-operations-with-graph-data"></a>Bulkbewerkingen met graafgegevens

De [bulkexecutorbibliotheek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` bevat een naamruimte om functionaliteit te bieden voor het maken en importeren van grafiekobjecten. 

In het volgende proces wordt uiteengezet hoe gegevensmigratie voor een Gremlin API-container kan worden gebruikt:
1. Haal records op uit de gegevensbron.
2. Construeer `GremlinVertex`- en `GremlinEdge`-objecten uit de verkregen objecten en voeg ze toe aan een `IEnumerable`-gegevensstructuur. In dit deel van de toepassing moet de logica voor het detecteren en toevoegen van relaties worden geïmplementeerd in geval de gegevensbron geen grafendatabase is.
3. Gebruik de [Graph BulkImportAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) voor het invoegen van de graafobjecten in de verzameling.

Met dit mechanisme wordt de efficiëntie van de gegevensmigratie verbeterd vergeleken met het gebruik van een Gremlin-client. Deze verbetering is merkbaar omdat voor het invoegen van gegevens met Gremlin de toepassing per keer een query moet verzenden die moet worden gevalideerd, geëvalueerd en vervolgens uitgevoerd om de gegevens te maken. De bulkexecutorbibliotheek verwerkt de validatie in de toepassing en verzendt meerdere grafiekobjecten tegelijk voor elke netwerkaanvraag.

### <a name="creating-vertices-and-edges"></a>Hoekpunten en randen maken

`GraphBulkExecutor` biedt de `BulkImportAsync`-methode, waarvoor een `IEnumerable`-lijst met `GremlinVertex`- of `GremlinEdge`-objecten is vereist, die beide zijn gedefinieerd in de `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`-naamruimte. In het voorbeeld worden de randen en hoekpunten gescheiden in twee BulkExecutor-importeertaken. Zie het voorbeeld hieronder:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Raadpleeg voor meer informatie over de parameters van de bulkexecutorbibliotheek het [thema BulkImportData naar Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

De nettolading moet in `GremlinVertex`- en `GremlinEdge`-objecten worden gemaakt. Hier ziet u hoe deze objecten kunnen worden gemaakt:

**Hoekpunten**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Randen**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Het hulpprogramma voor bulkuitvoerfouten controleert niet automatisch op bestaande Vertices voordat randen worden toegevoegd. Dit moet worden gevalideerd in de toepassing voordat de BulkImport-taken worden uitgevoerd.

## <a name="sample-application"></a>Voorbeeldtoepassing

### <a name="prerequisites"></a>Vereisten
* Visual Studio 2019 met de Azure-ontwikkelworkload. Je gratis aan de slag met de [Visual Studio 2019 Community Edition.](https://visualstudio.microsoft.com/downloads/)
* Een Azure-abonnement. U kunt [hier een gratis Azure-account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). U ook gratis een Cosmos-databaseaccount maken met [Probeer Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement.
* Een Azure Cosmos DB Gremlin API-database met een **onbeperkte verzameling**. In deze handleiding leest u hoe u aan de slag kunt met [Azure Cosmos DB Gremlin API in .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Ga naar de [Git-pagina met downloads](https://git-scm.com/downloads) voor meer informatie.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen
In deze zelfstudie volgen we de stappen om aan de slag te gaan met het [azure cosmos DB Graph bulk executor sample](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) gehost op GitHub. Deze toepassing bestaat uit een .NET-oplossing die willekeurig hoekpunt- en randobjecten maakt en ze vervolgens bulksgewijs invoegt voor het opgegeven grafendatabaseaccount. Voer de onderstaande opdracht `git clone` uit om de toepassing op te halen:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Deze opslagplaats bevat het GraphBulkExecutor-voorbeeld met de volgende bestanden:

File|Beschrijving
---|---
`App.config`|Hier worden de voor de toepassing en database specifieke parameters gespecificeerd. Dit bestand moet eerst worden aangepast om verbinding te kunnen maken met de doeldatabase en de verzamelingen.
`Program.cs`| Dit bestand bevat de `DocumentClient` logica achter het maken van de verzameling, het afhandelen van de opschoningen en het verzenden van de bulkexecutoraanvragen.
`Util.cs`| Dit bestand bevat een helperklasse die de logica bevat voor het genereren van testgegevens en het controleren op de aanwezigheid van de database en verzamelingen.

In het `App.config`-bestand zijn de configuratiewaarden die kunnen worden opgegeven, als volgt:

Instelling|Beschrijving
---|---
`EndPointUrl`|Dit is uw **.NET SDK-eindpunt**. Dit is aanwezig in de blade Overzicht van uw Azure Cosmos DB Gremlin API-databaseaccount. De indeling is `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Dit is de primaire of secundaire sleutel die vermeld staat onder uw Azure Cosmos DB-account. Meer informatie over [Toegang verkrijgen tot Azure Cosmos DB-gegevens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|Dit zijn **de namen van de doeldatabase en verzameling**. Als `ShouldCleanupOnStart` is ingesteld op `true`, worden deze waarden, in combinatie met `CollectionThroughput`, gebruikt om ze te verwijderen en een nieuwe database en verzameling te maken. Op dezelfde manier geldt dat als `ShouldCleanupOnFinish` is ingesteld op `true`, de waarden worden gebruikt om de database te verwijderen zodra de gegevensopname is voltooid. Merk op dat de doelverzameling een **onbeperkte verzameling** moet zijn.
`CollectionThroughput`|Deze wordt gebruikt om een nieuwe verzameling te maken als de optie `ShouldCleanupOnStart` is ingesteld op `true`.
`ShouldCleanupOnStart`|Hiermee worden het databaseaccount en de verzamelingen verwijderd voordat het programma wordt uitgevoerd. Vervolgens worden nieuwe gemaakt aan de hand van de waarden `DatabaseName`, `CollectionName` en `CollectionThroughput`.
`ShouldCleanupOnFinish`|Hiermee worden het databaseaccount en de verzamelingen verwijderd met de opgegeven `DatabaseName` en `CollectionName` nadat het programma is uitvoeren.
`NumberOfDocumentsToImport`|Hiermee wordt het aantal hoekpunten en randen bepaald dat in het voorbeeld wordt gegenereerd. Dit aantal geldt voor zowel de hoekpunten als de randen.
`NumberOfBatches`|Hiermee wordt het aantal hoekpunten en randen bepaald dat in het voorbeeld wordt gegenereerd. Dit aantal geldt voor zowel de hoekpunten als de randen.
`CollectionPartitionKey`|Deze wordt gebruikt om de testhoekpunten en -randen te maken, waarbij deze eigenschap automatisch wordt toegewezen. Deze wordt ook gebruikt als de database en de verzamelingen opnieuw worden gemaakt als de optie `ShouldCleanupOnStart` is ingesteld op `true`.

### <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

1. Voeg uw specifieke databaseconfiguratieparameters toe aan `App.config`. Deze wordt gebruikt om een DocumentClient-exemplaar te maken. Als de database en de container nog niet zijn gemaakt, worden ze automatisch gemaakt.
2. Voer de toepassing uit. Hierdoor wordt `BulkImportAsync` tweemaal aangeroepen; eenmaal om hoekpunten te importeren en eenmaal om randen te importeren. Als een van de objecten bij het invoegen een fout genereert, wordt het object toegevoegd aan `.\BadVertices.txt` of `.\BadEdges.txt`.
3. Evalueer de resultaten door een query uit te voeren op de grafendatabase. Als de optie `ShouldCleanupOnFinish` is ingesteld op waar, wordt de database automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen
* Zie [bulkexecutor SDK-details](sql-api-sdk-bulk-executor-dot-net.md)voor meer informatie over nuget-pakketdetails en releasenotes van bulkexecutor .NET-bibliotheek. 
* Bekijk de [prestatietips](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) om het gebruik van bulkuitvoerders verder te optimaliseren.
* Lees het artikel [BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) voor meer informatie over de klassen en methoden die in deze naamruimte zijn gedefinieerd.
