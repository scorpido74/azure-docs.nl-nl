---
title: Indexeringsbeleid in Azure Cosmos DB beheren
description: Meer informatie over het beheren van indexeringsbeleid, het opnemen of uitsluiten van indexering, het definiëren van indexering met behulp van verschillende Azure Cosmos DB SDK's
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252075"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Indexeringsbeleid in Azure Cosmos DB beheren

In Azure Cosmos DB worden gegevens geïndexeerd volgens [indexeringsbeleid](index-policy.md) dat voor elke container is gedefinieerd. Het standaardindexeringsbeleid voor nieuw gemaakte containers dwingt bereikindexen af voor een willekeurige tekenreeks of een willekeurig getal. Dit beleid kan worden overschreven met uw eigen aangepaste indexeringsbeleid.

## <a name="indexing-policy-examples"></a>Voorbeelden van indexeringsbeleid

Hier volgen enkele voorbeelden van indexeringsbeleid dat wordt weergegeven in [hun JSON-indeling,](index-policy.md#include-exclude-paths)en dat is hoe ze worden weergegeven op de Azure-portal. Dezelfde parameters kunnen worden ingesteld via de Azure CLI of een SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Opt-outbeleid om bepaalde vastgoedpaden selectief uit te sluiten

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Dit indexeringsbeleid is gelijk aan het ```kind```beleid ```dataType```waarop ```precision``` handmatig , en hun standaardwaarden worden ingesteld. Deze eigenschappen zijn niet langer nodig om expliciet in te stellen en u ze volledig weglaten uit uw indexeringsbeleid (zoals in het bovenstaande voorbeeld).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Opt-in-beleid om bepaalde vastgoedpaden selectief op te nemen

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Dit indexeringsbeleid is gelijk aan het ```kind```beleid ```dataType```waarop ```precision``` handmatig , en hun standaardwaarden worden ingesteld. Deze eigenschappen zijn niet langer nodig om expliciet in te stellen en u ze volledig weglaten uit uw indexeringsbeleid (zoals in het bovenstaande voorbeeld).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Het wordt over het algemeen aanbevolen om een **opt-out indexeringsbeleid** te gebruiken om Azure Cosmos DB proactief elke nieuwe eigenschap te laten indexeren die aan uw model kan worden toegevoegd.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Alleen een ruimtelijke index op een specifiek eigenschapspad gebruiken

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Voorbeelden van samengestelde indexeringsbeleid

Naast het opnemen of uitsluiten van paden voor afzonderlijke eigenschappen, u ook een samengestelde index opgeven. Als u een query wilt uitvoeren `ORDER BY` met een clausule voor meerdere eigenschappen, is een [samengestelde index](index-policy.md#composite-indexes) voor deze eigenschappen vereist. Bovendien hebben samengestelde indexen een prestatievoordeel voor query's die een filter hebben en een ORDER BY-clausule hebben over verschillende eigenschappen.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Samengestelde index gedefinieerd voor (naam asc, age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

De bovenstaande samengestelde index over naam en leeftijd is vereist voor query #1- en #2 query:

Query #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Query #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Deze samengestelde index komt ten goede aan query-#3 en query#4 en optimaliseert de filters:

Query #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Query #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Samengestelde index gedefinieerd voor (naam ASC, leeftijd ASC) en (naam ASC, leeftijd DESC):

U meerdere verschillende samengestelde indexen definiëren binnen hetzelfde indexeringsbeleid.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Samengestelde index gedefinieerd voor (naam ASC, leeftijd ASC):

Het is optioneel om de volgorde op te geven. Als dit niet is opgegeven, stijgt de volgorde.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Alle eigenschapspaden uitsluiten, maar blijven indexeren actief

Dit beleid kan worden gebruikt in situaties waarin de [Functie Time-to-Live (TTL)](time-to-live.md) actief is, maar er geen secundaire index vereist is (om Azure Cosmos DB te gebruiken als een pure key-value store).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Geen indexering

Met dit beleid wordt indexering uitgeschakeld. Als `indexingMode` ingesteld `none`is op, u geen TTL instellen op de container.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Indexeringsbeleid bijwerken

In Azure Cosmos DB kan het indexeringsbeleid worden bijgewerkt met een van de onderstaande methoden:

- vanaf de Azure-portal
- met de Azure CLI
- PowerShell gebruiken
- met behulp van een van de SDKs

Een [indexeringsbeleidsupdate](index-policy.md#modifying-the-indexing-policy) activeert een indextransformatie. De voortgang van deze transformatie kan ook worden gevolgd vanuit de SDK's.

> [!NOTE]
> Wanneer het indexeringsbeleid wordt bijgewerkt, wordt het schrijven naar Azure Cosmos DB ononderbroken. Tijdens het opnieuw indexeren kunnen query's gedeeltelijke resultaten retourneren wanneer de index wordt bijgewerkt.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Azure Cosmos-containers slaan hun indexeringsbeleid op als een JSON-document dat u met de Azure-portal rechtstreeks bewerken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Maak een nieuw Azure Cosmos DB-account of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer de container waaraan u wilt werken.

1. Klik op **& Instellingen schalen**.

1. Het JSON-document voor indexeringsbeleid wijzigen (zie voorbeelden [hieronder](#indexing-policy-examples))

1. Klik op **Opslaan** wanneer u klaar bent.

![Indexeren beheren met behulp van de Azure-portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Als u een container wilt maken met een aangepast indexeringsbeleid, [raadpleegt u Een container maken met een aangepast indexbeleid met CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>PowerShell gebruiken

Als u een container wilt maken met een aangepast indexeringsbeleid, [raadpleegt u Een container maken met een aangepast indexbeleid met Powershell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Gebruik de .NET SDK V2

Het `DocumentCollection` object van de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `IndexingPolicy` onthult `IndexingMode` een eigenschap `IncludedPaths` `ExcludedPaths`waarmee u de eigenschap wijzigen en toevoegen of verwijderen en.

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Als u de voortgang van `RequestOptions` de indextransformatie wilt bijhouden, geeft u een object door dat de `PopulateQuotaInfo` eigenschap instelt op `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Gebruik de .NET SDK V3

Het `ContainerProperties` object van de [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (zie [deze Quickstart](create-sql-api-dotnet.md) met betrekking tot het gebruik ervan) onthult een `IndexingPolicy` eigenschap waarmee u de `IndexingMode` eigenschap wijzigen en toevoegen of verwijderen `IncludedPaths` en `ExcludedPaths`.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Als u de voortgang van `RequestOptions` de indextransformatie wilt bijhouden, geeft u een object door waarop de `PopulateQuotaInfo` eigenschap is ingesteld `true`en haalt u de waarde op uit de `x-ms-documentdb-collection-index-transformation-progress` antwoordkop.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Wanneer u een aangepast indexeringsbeleid definieert terwijl u een nieuwe container maakt, u deze definitie op een beknopte en efficiënte manier schrijven:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Gebruik de Java SDK

Het `DocumentCollection` object van de [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (zie deze `getIndexingPolicy()` [Quickstart](create-sql-api-java.md) met betrekking tot het gebruik ervan) legt bloot en `setIndexingPolicy()` methoden. Met `IndexingPolicy` het object dat ze manipuleren u de indexeringsmodus wijzigen en opgenomen en uitgesloten paden toevoegen of verwijderen.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Als u de voortgang van de `RequestOptions` indextransformatie op een container wilt bijhouden, geeft `x-ms-documentdb-collection-index-transformation-progress` u een object door dat de te vullen quota-gegevens aangeeft en haalt u de waarde op uit de antwoordkop.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Gebruik de Node.js SDK

De `ContainerDefinition` interface van [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (zie [deze Quickstart](create-sql-api-nodejs.md) met betrekking tot het gebruik ervan) onthult een `indexingPolicy` eigenschap waarmee u `indexingMode` de eigenschap wijzigen en toevoegen of verwijderen `includedPaths` en `excludedPaths`.

De details van de container ophalen

```javascript
const containerResponse = await client.database('database').container('container').read();
```

De indexeringsmodus instellen op consistent

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Opgenomen pad inclusief ruimtelijke index toevoegen

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Uitgesloten pad toevoegen

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

De container bijwerken met wijzigingen

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Als u de voortgang van de `RequestOptions` indextransformatie op `populateQuotaInfo` een `true`container wilt bijhouden, geeft u een object door waarop de eigenschap is ingesteld en haalt u de waarde op uit de `x-ms-documentdb-collection-index-transformation-progress` antwoordkop.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>De Python SDK V3 gebruiken

Bij het gebruik van de [Python SDK V3](https://pypi.org/project/azure-cosmos/) (zie [deze Quickstart](create-sql-api-python.md) met betrekking tot het gebruik ervan), wordt de containerconfiguratie beheerd als een woordenboek. Vanuit dit woordenboek is het mogelijk om toegang te krijgen tot het indexeringsbeleid en al zijn kenmerken.

De details van de container ophalen

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

De indexeringsmodus instellen op consistent

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Een indexeringsbeleid definiëren met een opgenomen pad en een ruimtelijke index

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Een indexeringsbeleid definiëren met een uitgesloten pad

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Een samengestelde index toevoegen

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

De container bijwerken met wijzigingen

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="use-the-python-sdk-v4"></a>De Python SDK V4 gebruiken

Bij het gebruik van de [Python SDK V4](https://pypi.org/project/azure-cosmos/)wordt de containerconfiguratie beheerd als een woordenboek. Vanuit dit woordenboek is het mogelijk om toegang te krijgen tot het indexeringsbeleid en al zijn kenmerken.

De details van de container ophalen

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

De indexeringsmodus instellen op consistent

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Een indexeringsbeleid definiëren met een opgenomen pad en een ruimtelijke index

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Een indexeringsbeleid definiëren met een uitgesloten pad

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Een samengestelde index toevoegen

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

De container bijwerken met wijzigingen

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexeren in de volgende artikelen:

- [Indexoverzicht](index-overview.md)
- [Indexeringsbeleid](index-policy.md)
