---
title: Georuimtelijke gegevens opvragen met Azure Cosmos DB
description: Ruimtelijke gegevens opvragen met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566320"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Georuimtelijke gegevens opvragen met Azure Cosmos DB

In dit artikel wordt geverslagd over het opvragen van georuimtelijke gegevens in Azure Cosmos DB met SQL en LINQ. Momenteel wordt het opslaan en openen van georuimtelijke gegevens alleen ondersteund door Azure Cosmos DB SQL API-accounts. Azure Cosmos DB ondersteunt de volgende Open Geospatial Consortium (OGC) ingebouwde functies voor georuimtelijke query's. Zie [Querysysteemfuncties in Azure Cosmos DB](sql-query-system-functions.md)voor meer informatie over de volledige set ingebouwde functies in de SQL-taal.

## <a name="spatial-sql-built-in-functions"></a>Ruimtelijke SQL-ingebouwde functies

Hier is een lijst met georuimtelijke systeemfuncties die nuttig zijn voor het opvragen in Azure Cosmos DB:

|**Gebruik**|**Beschrijving**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Retourneert de afstand tussen de twee GeoJSON Point-, Polygon- of LineString-expressies.|
|ST_WITHIN (spatial_expr, spatial_expr) | Retourneert een Booleaanse expressie die aangeeft of het eerste GeoJSON-object (Point, Polygon of LineString) zich bevindt in het tweede GeoJSON-object (punt, Polygon of LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retourneert een Booleaanse expressie die aangeeft of de twee opgegeven GeoJSON-objecten (Point, Polygon of LineString) elkaar snijden.|
|ST_ISVALID| Retourneert een Booleaanse waarde die aangeeft of de opgegeven GeoJSON Point-, Polygon- of LineString-expressie geldig is.|
| ST_ISVALIDDETAILED| Retourneert een JSON-waarde met een Booleaanse waarde als de opgegeven expressie GeoJSON Point, Polygon of LineString geldig is. Als dit ongeldig is, wordt de reden als tekenreekswaarde geretourneerd.|

Ruimtelijke functies kunnen worden gebruikt om nabijheidsquery's uit te voeren op ruimtelijke gegevens. Hier vindt u bijvoorbeeld een query die alle gezinsdocumenten retourneert die `ST_DISTANCE` zich binnen 30 km van de opgegeven locatie bevinden met behulp van de ingebouwde functie.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Resultaten**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Als u ruimtelijke indexering opneemt in uw indexeringsbeleid, worden 'afstandsquery's' efficiënt via de index weergegeven. Zie [georuimtelijke indexering voor](sql-query-geospatial-index.md)meer informatie over ruimtelijke indexering . Als u geen ruimtelijke index hebt voor de opgegeven paden, wordt de container door de query gescant.

`ST_WITHIN`kan worden gebruikt om te controleren of een punt ligt in een Veelhoek. Vaak worden Veelhoeken gebruikt om grenzen te vertegenwoordigen, zoals postcodes, staatsgrenzen of natuurlijke formaties. Nogmaals als u ruimtelijke indexering opneemt in uw indexeringsbeleid, worden query's 'binnen' efficiënt via de index weergegeven.

Polygoon `ST_WITHIN` argumenten in kan bevatten slechts een enkele ring, dat wil zeggen, de Polygonen mogen geen gaten bevatten in hen.

**Query**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultaten**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Net als bij de manier waarop niet-overeenkomende typen werken in Azure Cosmos DB-query, als de locatiewaarde die in een van beide argumenten is opgegeven, onjuist of ongeldig is, wordt deze geëvalueerd op **niet-gedefinieerde** en het geëvalueerde document dat moet worden overgeslagen uit de queryresultaten. Als uw query geen `ST_ISVALIDDETAILED` resultaten retourneert, voert u uit om te debuggen waarom het ruimtelijke type ongeldig is.
>
>

Azure Cosmos DB ondersteunt ook het uitvoeren van omgekeerde query's, dat wil zeggen dat u veelhoeken of lijnen indexeren in Azure Cosmos DB en vervolgens query's maken voor de gebieden die een opgegeven punt bevatten. Dit patroon wordt vaak gebruikt in de logistiek om te identificeren, bijvoorbeeld wanneer een vrachtwagen een aangewezen gebied binnenkomt of verlaat.

**Query**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Resultaten**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`en `ST_ISVALIDDETAILED` kan worden gebruikt om te controleren of een ruimtelijk object geldig is. In de volgende query wordt bijvoorbeeld de geldigheid van een punt met een breedtegraadwaarde buiten bereik (-132.8) gecontroleerd. `ST_ISVALID`retourneert alleen een `ST_ISVALIDDETAILED` Booleaanse waarde en retourneert de Booleaanse en een tekenreeks met de reden waarom deze als ongeldig wordt beschouwd.

**Query**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultaten**

```json
    [{
      "$1": false
    }]
```

Deze functies kunnen ook worden gebruikt om Polygonen te valideren. Hier gebruiken `ST_ISVALIDDETAILED` we bijvoorbeeld om een Veelhoek te valideren die niet is gesloten.

**Query**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Resultaten**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-query's in de .NET SDK

De SQL .NET SDK levert `Distance()` `Within()` ook stub-methoden en voor gebruik binnen LINQ-expressies. De SQL LINQ-provider vertaalt deze methodeaanroepen naar de equivalente SQL-ingebouwde functieaanroepen (respectievelijk ST_DISTANCE en ST_WITHIN).

Hier is een voorbeeld van een LINQ-query waarin alle `location` documenten in de Azure Cosmos-container worden gevonden waarvan de waarde zich binnen een straal van 30 km van het opgegeven punt bevindt met LINQ.

**LINQ-query voor afstand**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Op dezelfde manier is hier een query `location` voor het vinden van alle documenten waarvan deze zich binnen het opgegeven vak/Veelhoek bevindt.

**LINQ-query voor Binnen**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, u vervolgens:

* Meer informatie over [Azure Cosmos DB Query](sql-query-getting-started.md)
* Meer informatie over [geospatiale en GeoJSON-locatiegegevens in Azure Cosmos DB](sql-query-geospatial-intro.md)
* Meer informatie over [ruimtelijke gegevens indexen met Azure Cosmos DB](sql-query-geospatial-index.md)
