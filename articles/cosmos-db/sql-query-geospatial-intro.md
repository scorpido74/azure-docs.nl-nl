---
title: Georuimtelijke en geojson-locatie gegevens in Azure Cosmos DB
description: Meer informatie over het maken van ruimtelijke objecten met Azure Cosmos DB en de SQL-API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 0fe83b8e28b96f1d89a7c98cfe86a6e924f1bc49
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566346"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Georuimtelijke en geojson-locatie gegevens in Azure Cosmos DB

In dit artikel bevat een inleiding tot de functionaliteit van georuimtelijke in Azure Cosmos DB. Het opslaan en openen van georuimtelijke gegevens wordt momenteel alleen ondersteund door Azure Cosmos DB SQL-API-accounts. Nadat u onze documentatie voor georuimtelijk indexeren hebt gelezen, kunt u de volgende vragen beantwoorden:

* Hoe kan ik ruimtelijke gegevens opslaan in Azure Cosmos DB?
* Hoe kan ik georuimtelijke gegevens in Azure Cosmos DB in SQL en LINQ op te vragen?
* Hoe ik in- of uitschakelen ruimtelijke indexering in Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Inleiding tot ruimtelijke gegevens

Ruimtelijke gegevens beschrijft de positie en de vorm van objecten in de ruimte. In de meeste toepassingen, deze komen overeen met de objecten op de aarde en georuimtelijke gegevens. Ruimtelijke gegevens kunnen worden gebruikt om weer te geven van de locatie van een persoon, een plaats van belang of de grens van een plaats of een meer. Veelgebruikte toepassingsgebieden vaak betrekking hebben op nabijheidsquery's, bijvoorbeeld, "alle in internetcafés vinden in de buurt van mijn huidige locatie."

De SQL-API van Azure Cosmos DB ondersteunt het **geografie** -gegevens type. Het **geografie** type staat voor gegevens in een coördinaten systeem met round-Earth.

## <a name="supported-data-types"></a>Ondersteunde gegevens typen

Azure Cosmos DB ondersteunt het indexeren en doorzoeken van georuimtelijke punt gegevens die worden weer gegeven met de [GEOjson-specificatie](https://tools.ietf.org/html/rfc7946). GeoJSON-gegevensstructuren zijn altijd geldig JSON-objecten, zodat ze kunnen worden opgeslagen en opgevraagd met behulp van Azure Cosmos DB zonder speciale hulpprogramma's of bibliotheken.

Azure Cosmos DB ondersteunt de volgende ruimtelijke gegevens typen:

- Spreek
- Lines Tring
- Polygoon
- MultiPolygon

### <a name="points"></a>Verwijst

Een **punt** geeft aan dat er één positie in de ruimte is. Een punt vertegenwoordigt in georuimtelijke gegevens, de exacte locatie, wat erop kan een adres van een winkelketen, een kiosk, een auto of een plaats.  Een punt wordt weergegeven in de combinatie van GeoJSON (en de Azure Cosmos DB) met behulp van de coördinaat of lengtegraad en breedtegraad.

Hier volgt een voor beeld van een JSON voor een punt:

**Punten in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Ruimtelijke gegevens typen kunnen worden inge sloten in een Azure Cosmos DB-document, zoals wordt weer gegeven in dit voor beeld van een gebruikers profiel dat locatie gegevens bevat:

**Profiel gebruiken met de locatie die is opgeslagen in Azure Cosmos DB**

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

### <a name="points-in-geography-coordinate-system"></a>Punten in geografisch coördinaten systeem

Voor het **geografische** gegevens type geeft de geojson-specificatie de lengte graad en de tweede breedte aan. Net als in andere toepassingen toewijzing lengtegraad en breedtegraad zijn hoeken en weergegeven in termen van graden. Waarden voor lengtegraad van de nulmeridiaan worden gemeten en tussen-180 graden en 180.0 graden en waarden voor breedtegraad van de evenaar worden gemeten en tussen-90.0 graden en 90.0 graden zijn.

Azure Cosmos DB interpreteert coördinaten zoals voorgesteld per de referentiesysteem WGS 84. Zie hieronder voor meer informatie over de coördinaat referentiesystemen.

### <a name="linestrings"></a>Line strings toe

**Line strings toe** vertegenwoordigen een reeks van twee of meer punten in ruimte en de lijn segmenten waarmee ze zijn verbonden. Georuimtelijke gegevens, worden de LineStrings veelvuldig gebruikt om snelwegen of rivieren vertegenwoordigen.

**Line strings toe in geojson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygonen

Een **veelhoek** is een grens van verbonden punten die een gesloten Lines Tring vormen. Veelhoeken worden veelvuldig gebruikt om natuurlijke ontstaan wachtrijen zoals meren of politieke jurisdicties zoals plaatsen en Staten vertegenwoordigen. Hier volgt een voor beeld van een veelhoek in Azure Cosmos DB:

**Veelhoeken in geojson**

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
> De GeoJSON-specificatie is vereist dat voor geldige veelhoeken, het laatste coördinaat paar opgegeven moet hetzelfde zijn als de eerste pagina, om een gesloten vorm te maken.
>
> Punten in een Polygoon moeten worden opgegeven in volgorde van linksom draaien. Een veelhoek in rechtsom volgorde opgegeven vertegenwoordigt de omgekeerde waarde van de regio binnen het.
>
>

### <a name="multipolygons"></a>Point

Een **multiveelhoek** is een matrix met nul of meer veelhoeken. **Multiveelhoeken** kunnen geen zijden overlappen of een gemeen schappelijk gebied hebben. Ze kunnen op een of meer punten raken.

**Multiveelhoeken in geojson**

```json
{
    "type":"MultiPolygon",
    "coordinates":[ [
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
    ],
    [
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
    ] ]
}
```

## <a name="coordinate-reference-systems"></a>Coördinaat referentiesystemen

Omdat de vorm van de aarde onregelmatig is, worden coördinaten van georuimtelijke gegevens weer gegeven in veel coördinaten referentie systemen (CRS), elk met hun eigen frames van referentie-en meet eenheden. Bijvoorbeeld, de 'nationale raster van Brittannië' is een referentiesysteem juist is voor het Verenigd Koninkrijk, maar niet daarbuiten wordt weergegeven.

Het populairste CRS dat momenteel wordt gebruikt, is de World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Gebruik WGS 84 GPS-apparaten en veel toewijzing diensten, waaronder Google Maps en Bing Maps-API's. Azure Cosmos DB ondersteunt het indexeren en doorzoeken van georuimtelijke gegevens op geografische locatie alleen met behulp van de WGS-84-CRS.

## <a name="creating-documents-with-spatial-data"></a>Het maken van documenten met ruimtelijke gegevens
Bij het maken van documenten die de GeoJSON-waarden bevatten, worden ze automatisch geïndexeerd met een ruimtelijke index in overeenstemming met het indexeringsbeleid van de container. Als u met een Azure Cosmos DB SDK in een dynamisch getypeerde taal, zoals Python of Node.js werkt, moet u geldige GeoJSON maken.

**Document maken met georuimtelijke gegevens in node. js**

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

Als u met de SQL-Api's werkt, kunt u de klassen `Point`, `LineString`, `Polygon`en `MultiPolygon` binnen de naam ruimte `Microsoft.Azure.Cosmos.Spatial` gebruiken om locatie-informatie in uw toepassings objecten in te sluiten. Deze klassen te vereenvoudigen de serialisatie en deserialisatie van ruimtelijke gegevens in GeoJSON.

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

Als u niet beschikt over de breedte graad en lengte graad, maar de fysieke adressen of locatie naam als plaats of land/regio hebt, kunt u de werkelijke coördinaten opzoeken met behulp van een geocoderings service zoals Bing Maps REST services. Lees [hier](https://msdn.microsoft.com/library/ff701713.aspx)meer over de geocodering van Bing Maps.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, naast kunt u:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over het [opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [ruimtelijke gegevens in index met Azure Cosmos DB](sql-query-geospatial-index.md)