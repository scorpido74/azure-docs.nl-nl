---
title: Geospatiale en GeoJSON-locatiegegevens in Azure Cosmos DB
description: Begrijpen hoe u ruimtelijke objecten maakt met Azure Cosmos DB en de SQL API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367581"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Geospatiale en GeoJSON-locatiegegevens in Azure Cosmos DB

Dit artikel is een inleiding tot de georuimtelijke functionaliteit in Azure Cosmos DB. Momenteel wordt het opslaan en openen van georuimtelijke gegevens alleen ondersteund door Azure Cosmos DB SQL API-accounts. Na het lezen van onze documentatie over georuimtelijke indexering u de volgende vragen beantwoorden:

* Hoe sla ik ruimtelijke gegevens op in Azure Cosmos DB?
* Hoe kan ik georuimtelijke gegevens opvragen in Azure Cosmos DB in SQL en LINQ?
* Hoe kan ik ruimtelijke indexering in Azure Cosmos DB in- of uitschakelen?

## <a name="introduction-to-spatial-data"></a>Inleiding tot ruimtelijke gegevens

Ruimtelijke gegevens beschrijven de positie en vorm van objecten in de ruimte. In de meeste toepassingen komen deze overeen met objecten op aarde en georuimtelijke gegevens. Ruimtelijke gegevens kunnen worden gebruikt om de locatie van een persoon, een plaats van belang, of de grens van een stad, of een meer weer te geven. Veelvoorkomende gebruiksgevallen hebben vaak betrekking op nabijheidsvragen, bijvoorbeeld 'vind alle coffeeshops in de buurt van mijn huidige locatie'.

De SQL API van Azure Cosmos DB ondersteunt twee ruimtelijke gegevenstypen: het **gegevenstype geometrie** en het **gegevenstype geografie.**

- Het **geometrietype** vertegenwoordigt gegevens in een Euclidisch (plat) coördinatensysteem
- Het **aardrijkskundetype** vertegenwoordigt gegevens in een round-earth coördinaatsysteem.

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

Azure Cosmos DB ondersteunt indexering en query's van georuimtelijke puntgegevens die worden weergegeven met behulp van de [GeoJSON-specificatie.](https://tools.ietf.org/html/rfc7946) GeoJSON-gegevensstructuren zijn altijd geldige JSON-objecten, zodat ze kunnen worden opgeslagen en opgevraagd met Azure Cosmos DB zonder gespecialiseerde hulpprogramma's of bibliotheken.

Azure Cosmos DB ondersteunt de volgende ruimtelijke gegevenstypen:

- Punt
- Lijntekenreeks
- Veelhoek
- Multipolygoon

### <a name="points"></a>Punten

Een **punt** geeft één positie in de ruimte aan. In georuimtelijke gegevens geeft een punt de exacte locatie weer, wat een adres van een supermarkt, een kiosk, een auto of een stad kan zijn.  Een punt wordt weergegeven in GeoJSON (en Azure Cosmos DB) met behulp van het coördinatenpaar of lengte- en breedtegraad.

Hier is een voorbeeld JSON voor een punt:

**Punten in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Ruimtelijke gegevenstypen kunnen worden ingesloten in een Azure Cosmos DB-document, zoals in dit voorbeeld wordt weergegeven van een gebruikersprofiel met locatiegegevens:

**Profiel gebruiken met locatie die is opgeslagen in Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punten in een coördinatensysteem voor geometrie

Voor het **gegevenstype geometrie** geeft de GeoJSON-specificatie eerst de horizontale as en de verticale as op de tweede.

### <a name="points-in-a-geography-coordinate-system"></a>Punten in een aardrijkskundecoördinatensysteem

Voor het **gegevenstype geografie** geeft de GeoJSON-specificatie eerst lengte- en breedtegraad op. Net als in andere mapping toepassingen, lengte en breedte zijn hoeken en vertegenwoordigd in termen van graden. Lengtewaarden worden gemeten vanaf de Prime Meridian en liggen tussen -180 graden en 180,0 graden, en breedtewaarden worden gemeten vanaf de evenaar en liggen tussen -90,0 graden en 90,0 graden.

Azure Cosmos DB interpreteert coördinaten zoals vertegenwoordigd volgens het WGS-84-referentiesysteem. Zie hieronder voor meer informatie over coördinatenreferentiesystemen.

### <a name="linestrings"></a>Linetekenreeksen

**LineStrings** vertegenwoordigen een reeks van twee of meer punten in de ruimte en de lijnsegmenten die deze verbinden. In georuimtelijke gegevens worden LineStrings vaak gebruikt om snelwegen of rivieren weer te geven.

**LineStrings in GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Veelhoeken

Een **Veelhoek** is een grens van verbonden punten die een gesloten linestring vormt. Polygonen worden vaak gebruikt om natuurlijke formaties zoals meren of politieke jurisdicties zoals steden en staten te vertegenwoordigen. Hier is een voorbeeld van een Veelhoek in Azure Cosmos DB:

**Polygonen in GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> De GeoJSON-specificatie vereist dat voor geldige Polygonen het laatste meegeleverde coördinatenpaar hetzelfde moet zijn als het eerste, om een gesloten vorm te maken.
>
> Punten binnen een Veelhoek moeten tegen de klok in worden opgegeven. Een veelhoek die in de volgorde met de klok mee is opgegeven, vertegenwoordigt het omgekeerde van het gebied erin.
>
>

### <a name="multipolygons"></a>MultiPolygonen

Een **MultiPolygon** is een array van nul of meer Polygonen. **MultiPolygons** kunnen geen kanten overlappen of een gemeenschappelijke ruimte hebben. Ze kunnen aanraken op een of meer punten.

**MultiPolygons in GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Referentiesystemen coördineren

Aangezien de vorm van de aarde onregelmatig is, worden de coördinaten van georuimtelijke gegevens van de geografie vertegenwoordigd in vele coördinatenreferentiesystemen (CRS), elk met hun eigen referentiekaders en meeteenheden. Het "National Grid of Britain" is bijvoorbeeld een referentiesysteem dat juist is voor het Verenigd Koninkrijk, maar niet daarbuiten.

De meest populaire CRS in gebruik vandaag is de World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS-apparaten en veel kaartservices, waaronder Google Maps en Bing Maps API's, maken gebruik van WGS-84. Azure Cosmos DB ondersteunt het indexeren en opvragen van georuimtelijke gegevens in geografie met alleen de WGS-84 CRS.

## <a name="creating-documents-with-spatial-data"></a>Documenten maken met ruimtelijke gegevens
Wanneer u documenten maakt die GeoJSON-waarden bevatten, worden ze automatisch geïndexeerd met een ruimtelijke index in overeenstemming met het indexeringsbeleid van de container. Als u werkt met een Azure Cosmos DB SDK in een dynamisch getypte taal zoals Python of Node.js, moet u geldige GeoJSON maken.

**Document maken met georuimtelijke gegevens in Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Als u met de SQL-API's werkt, `LineString` `Polygon`kunt `MultiPolygon` u de `Point` `Microsoft.Azure.Cosmos.Spatial` klassen , , en klassen binnen de naamruimte gebruiken om locatiegegevens in uw toepassingsobjecten in te sluiten. Deze klassen helpen de serialisatie en deserialisatie van ruimtelijke gegevens in GeoJSON te vereenvoudigen.

**Document maken met georuimtelijke gegevens in .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Als u niet beschikt over de breedte- en lengtegraad-informatie, maar de fysieke adressen of locatienaam hebt, zoals stad of land/regio, u de werkelijke coördinaten opzoeken met behulp van een geocoderingsservice zoals Bing Maps REST Services. Lees [hier](https://msdn.microsoft.com/library/ff701713.aspx)meer over de geocodering van Bing Maps.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, u vervolgens:

* Meer informatie over [Azure Cosmos DB Query](sql-query-getting-started.md)
* Meer informatie over [het opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [ruimtelijke gegevens indexen met Azure Cosmos DB](sql-query-geospatial-index.md)