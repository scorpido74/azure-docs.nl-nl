---
title: Indexerings beleid in Azure Cosmos DB beheren
description: Meer informatie over het beheren van indexerings beleid in Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: thweiss
ms.openlocfilehash: a6c1ec6d58939336fb8a982e3ab1b9be20d4e0a5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172155"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Indexerings beleid in Azure Cosmos DB beheren

In Azure Cosmos DB worden gegevens geïndexeerd na het [indexerings beleid](index-policy.md) dat voor elke container is gedefinieerd. Het standaard indexerings beleid voor nieuw gemaakte containers dwingt bereik indexen af voor wille keurige teken reeksen of getallen en ruimtelijke indexen voor een geojson-object van het type punt. Dit beleid kan worden overschreven:

- van de Azure Portal
- de Azure CLI gebruiken
- een van de Sdk's gebruiken

Met een [indexerings beleid-update](index-policy.md#modifying-the-indexing-policy) wordt een index transformatie gegenereerd. De voortgang van deze trans formatie kan ook worden bijgehouden van de Sdk's.

> [!NOTE]
> Als onderdeel van de SDK en de upgrade van de portal wordt het index beleid zodanig aangepast dat het wordt uitgelijnd met een nieuwe index indeling die we hebben geïmplementeerd naar nieuwe containers. Met deze nieuwe indeling worden alle primitieve gegevens typen geïndexeerd als bereik met volledige precisie (-1). Daarom worden de index typen en de nauw keurigheid niet meer aan de gebruiker blootgesteld. In de toekomst moeten gebruikers eenvoudigweg paden toevoegen aan de sectie includedPaths en indexKinds en Precision negeren. Deze wijziging heeft geen invloed op de prestaties en u kunt het indexerings beleid blijven bijwerken met dezelfde syntaxis. U kunt alle voor beelden in onze bestaande documentatie blijven gebruiken om het index beleid bij te werken.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

Azure Cosmos-containers slaan hun indexerings beleid op als een JSON-document waarmee u de Azure Portal rechtstreeks kunt bewerken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Maak een nieuw Azure Cosmos DB-account of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer de container waaraan u wilt werken.

1. Klik op **schaal & instellingen**.

1. Het JSON-document van het indexerings beleid wijzigen (Zie de [volgende](#indexing-policy-examples)voor beelden)

1. Klik op **Opslaan** wanneer u klaar bent.

![Indexeren beheren met behulp van de Azure-portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Met de opdracht [AZ cosmosdb Collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) van Azure cli kunt u de JSON-definitie van het indexerings beleid van een container vervangen:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>De .NET SDK v2 gebruiken

Het `DocumentCollection` object van de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (Zie [deze Snelstartgids](create-sql-api-dotnet.md) met betrekking tot het gebruik) toont `IndexingPolicy` een eigenschap waarmee u de `IndexingMode` `ExcludedPaths`kunt wijzigen en toevoegen of `IncludedPaths` verwijderen.

De details van de container ophalen

```csharp
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
```

Stel de indexerings modus consistent in

```csharp
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
```

Een opgenomen pad toevoegen

```csharp
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/age/*" });
```

Een uitgesloten pad toevoegen

```csharp
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
```

Een ruimtelijke index toevoegen

```csharp
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
```

Een samengestelde index toevoegen

```csharp
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
```

Container bijwerken met wijzigingen

```csharp
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Als u de voortgang van de index transformatie wilt `RequestOptions` bijhouden, geeft u `PopulateQuotaInfo` een object `true`door dat de eigenschap instelt op.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Het `DocumentCollection` object van de [Java-SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) ( [Zie deze Snelstartgids](create-sql-api-java.md) `getIndexingPolicy()` met betrekking tot het gebruik) `setIndexingPolicy()` wordt weer gegeven en beschreven. Met `IndexingPolicy` het object dat wordt gemanipuleerd, kunt u de indexerings modus wijzigen en opgenomen en uitgesloten paden toevoegen of verwijderen.

De details van de container ophalen

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

Stel de indexerings modus consistent in

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

Een opgenomen pad toevoegen

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

Een uitgesloten pad toevoegen

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

Een ruimtelijke index toevoegen

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

Een samengestelde index toevoegen

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

De container bijwerken met wijzigingen

```java
 client.replaceCollection(container, null);
```

Als u de voortgang van de index transformatie op een container wilt `RequestOptions` bijhouden, geeft u een object door dat de quotum gegevens aanvraagt dat moet worden `x-ms-documentdb-collection-index-transformation-progress` ingevuld. vervolgens haalt u de waarde op uit de reactie header.

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

## <a name="use-the-nodejs-sdk"></a>De node. js-SDK gebruiken

De `ContainerDefinition` interface van de [node. js-SDK](https://www.npmjs.com/package/@azure/cosmos) (Zie [deze Snelstartgids](create-sql-api-nodejs.md) met betrekking tot het gebruik `indexingPolicy` ) toont een eigenschap waarmee u `indexingMode` de `excludedPaths`kunt wijzigen en `includedPaths` toevoegen of verwijderen.

De details van de container ophalen

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Stel de indexerings modus consistent in

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Opgenomen pad inclusief een ruimtelijke index toevoegen

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

Als u de voortgang van de index transformatie op een container wilt `RequestOptions` bijhouden, geeft u `populateQuotaInfo` een object `true`door waarmee de eigenschap wordt ingesteld en `x-ms-documentdb-collection-index-transformation-progress` haalt u de waarde op uit de reactie header.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>De python-SDK gebruiken

Wanneer u de [python-SDK](https://pypi.org/project/azure-cosmos/) gebruikt (Zie [deze Snelstartgids](create-sql-api-python.md) met betrekking tot het gebruik), wordt de container configuratie als een woorden boek beheerd. Vanuit deze woorden lijst is het mogelijk om toegang te krijgen tot het indexerings beleid en alle bijbehorende kenmerken.

De details van de container ophalen

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Stel de indexerings modus consistent in

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Een indexerings beleid definiëren met een opgenomen pad en een ruimtelijke index

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

Een indexerings beleid definiëren met een uitgesloten pad

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

## <a name="indexing-policy-examples"></a>Voor beelden van Indexing-beleid

Hier volgen enkele voor beelden van beleids regels voor indexering die worden weer gegeven in de JSON-indeling. Dit is de manier waarop deze worden weer gegeven op de Azure Portal. Dezelfde para meters kunnen worden ingesteld via de Azure CLI of een SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Opt-out-beleid om selectief enkele eigenschaps paden uit te sluiten
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Beleid voor opt-in om selectief een aantal eigenschaps paden op te nemen
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Opmerking: U wordt aangeraden een **opt-out-** indexerings beleid te gebruiken om Azure Cosmos DB proactief een nieuwe eigenschap te indexeren die kan worden toegevoegd aan uw model.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Alleen een ruimtelijke index gebruiken voor een specifiek pad naar een eigenschap
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Alle paden uitsluiten, maar indexeren actief houden

Dit beleid kan worden gebruikt in situaties waarin de [TTL-functie (time-to-Live)](time-to-live.md) actief is, maar geen secundaire index vereist is (om Azure Cosmos DB als zuivere sleutel waarde-archief te gebruiken).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Geen indexering
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Voor beelden van samengesteld indexerings beleid

Naast het opnemen of uitsluiten van paden voor afzonderlijke eigenschappen, kunt u ook een samengestelde index opgeven. Als u een query wilt uitvoeren met een `ORDER BY` -component voor meerdere eigenschappen, is een [samengestelde index](index-policy.md#composite-indexes) voor die eigenschappen vereist.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Samengestelde index gedefinieerd voor (naam ASC, leeftijd DESC):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Deze samengestelde index zou de volgende twee query's kunnen ondersteunen:

Query #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Query #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Samengestelde index gedefinieerd voor (naam ASC, Age ASC) en (naam ASC, leeftijd DESC):

U kunt meerdere verschillende samengestelde indexen binnen hetzelfde indexerings beleid definiëren. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Het is optioneel om de volg orde op te geven. Als u niets opgeeft, wordt de volg orde Oplopend.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexeren in de volgende artikelen:

- [Overzicht van indexeren](index-overview.md)
- [Indexeringsbeleid](index-policy.md)
