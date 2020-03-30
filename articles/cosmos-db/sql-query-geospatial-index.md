---
title: Georuimtelijke gegevens indexeren met Azure Cosmos DB
description: Ruimtelijke gegevens indexeren met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137900"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Georuimtelijke gegevens indexeren met Azure Cosmos DB

We hebben de databaseengine van Azure Cosmos DB ontworpen om echt schemaagnostisch te zijn en eersteklas ondersteuning te bieden voor JSON. De voor schrijven geoptimaliseerde databaseengine van Azure Cosmos DB begrijpt native ruimtelijke gegevens die worden weergegeven in de GeoJSON-standaard.

In een notendop wordt de geometrie geprojecteerd vanaf geodetische coördinaten op een 2D-vlak en vervolgens geleidelijk verdeeld in cellen met behulp van een **quadtree.** Deze cellen worden toegewezen aan 1D op basis van de locatie van de cel binnen een **Hilbert ruimte vulcurve**, die de plaats van punten behoudt. Bovendien wanneer locatiegegevens worden geïndexeerd, gaat het door een proces dat bekend staat als **tessellation,** dat wil zeggen dat alle cellen die een locatie kruisen, worden geïdentificeerd en opgeslagen als sleutels in de Azure Cosmos DB-index. Bij querytijd worden argumenten zoals punten en Polygonen ook tessellated om de relevante cel-ID-bereiken te extraheren en vervolgens gebruikt om gegevens uit de index op te halen.

Als u een indexeringsbeleid opgeeft dat ruimtelijke index bevat voor /* (alle paden), worden alle gegevens in de container geïndexeerd voor efficiënte ruimtelijke query's.

> [!NOTE]
> Azure Cosmos DB ondersteunt indexering van punten, linestrings, polygonen en multipolygonen
>
>

## <a name="modifying-geospatial-data-type"></a>Georuimtelijk gegevenstype wijzigen

In uw container `geospatialConfig` geeft de aan hoe de georuimtelijke gegevens worden geïndexeerd. U moet `geospatialConfig` er één per container opgeven: geografie of geometrie. Als dit niet `geospatialConfig` is opgegeven, wordt het gegevenstype geografie standaard weergegeven. Wanneer u `geospatialConfig`de , alle bestaande georuimtelijke gegevens in de container wijzigt, worden ze opnieuw geïndexeerd.

> [!NOTE]
> Azure Cosmos DB ondersteunt momenteel wijzigingen in de geospatialConfig in de .NET SDK alleen in versies 3.6 en hoger.
>

Hier is een voorbeeld voor het wijzigen `geometry` van `geospatialConfig` het georuimtelijke gegevenstype door de eigenschap in te stellen en een **selectiekader**toe te voegen:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Voorbeelden van aardrijkskundegegevens indexeren

In het volgende JSON-fragment wordt een indexeringsbeleid weergegeven waarbij ruimtelijke indexering is ingeschakeld voor het **gegevenstype geografie.** Het is geldig voor ruimtelijke gegevens met het gegevenstype geografie en indexeert elke GeoJSON-punt, veelhoek, multipolygoon of linestring die is gevonden in documenten voor ruimtelijke query's. Als u het indexeringsbeleid wijzigt met de Azure-portal, u het volgende JSON voor indexeringsbeleid opgeven om ruimtelijke indexering op uw container in te schakelen:

**Container indexeringsbeleid JSON met ruimtelijke indexering geografie**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Als de locatie GeoJSON-waarde in het document misvormd of ongeldig is, wordt deze niet geïndexeerd voor ruimtelijke query's. U locatiewaarden valideren met ST_ISVALID en ST_ISVALIDDETAILED.

U ook [het indexeringsbeleid wijzigen](how-to-manage-indexing-policy.md) met de Azure CLI, PowerShell of een SDK.

## <a name="geometry-data-indexing-examples"></a>Voorbeelden van gegevensindexering geometrie

Met het **gegevenstype geometrie,** vergelijkbaar met het gegevenstype geografie, moet u relevante paden en typen opgeven om te indexeren. Daarnaast moet u ook `boundingBox` een binnen het indexeringsbeleid opgeven om het gewenste gebied aan te geven dat voor dat specifieke pad moet worden geïndexeerd. Elk georuimtelijk pad`boundingBox`vereist zijn eigen .

Het selectiekader bestaat uit de volgende eigenschappen:

- **xmin**: de minimale geïndexeerde x-coördinaat
- **ymin**: de minimale geïndexeerde y coördinaat
- **xmax**: de maximale geïndexeerde x-coördinaat
- **ymax**: de maximale geïndexeerde y-coördinaat

Een selectiekader is vereist omdat geometrische gegevens een vlak in beslag nemen dat oneindig kan zijn. Ruimtelijke indexen vereisen echter een eindige ruimte. Voor het **gegevenstype geografie** is de aarde de grens en hoeft u geen selectiekader in te stellen.

U moet een selectiekader maken dat alle (of de meeste) gegevens bevat. Alleen bewerkingen die worden berekend op de objecten die zich volledig binnen het selectiekader bevinden, kunnen de ruimtelijke index gebruiken. U moet het selectiekader niet aanzienlijk groter maken dan nodig is, omdat dit een negatieve invloed heeft op de queryprestaties.

Hier is een voorbeeld indexeringsbeleid dat **geometriegegevens** indexeert `geometry`met **geospatialConfig** ingesteld op:

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
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Het bovenstaande indexeringsbeleid heeft een **selectiekader** van (-10, 10) voor x-coördinaten en (-20, 20) voor y-coördinaten. De container met het bovenstaande indexeringsbeleid indexeert alle punten, veelhoeken, multipolygonen en linestrings die zich volledig binnen deze regio bevinden.

> [!NOTE]
> Als u een indexeringsbeleid met een **selectiekader** probeert `geography` toe te voegen aan een container met gegevenstype, mislukt dit. U moet de **geospatialFig** van de `geometry` container wijzigen voordat u een **selectiedoos toevoegt.** U gegevens toevoegen en de rest van uw indexeringsbeleid (zoals de paden en typen) wijzigen voor of na het selecteren van het georuimtelijke gegevenstype voor de container.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, u vervolgens:

* Meer informatie over [Azure Cosmos DB Query](sql-query-getting-started.md)
* Meer informatie over [het opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [geospatiale en GeoJSON-locatiegegevens in Azure Cosmos DB](sql-query-geospatial-intro.md)