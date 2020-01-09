---
title: Georuimtelijke gegevens gebruiken in Azure Cosmos DB SQL-API-account
description: Informatie over het maken, indexeren en query uitvoeren op ruimtelijke objecten met Azure Cosmos DB en de SQL-API.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: e48f6c52aa2d633ea20fd0dae70c7aa1380bb50d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441888"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Georuimtelijke en GeoJSON locatiegegevens gebruiken met Azure Cosmos DB SQL API-account

In dit artikel bevat een inleiding tot de functionaliteit van georuimtelijke in Azure Cosmos DB. Op dit moment opslaan en openen van georuimtelijke gegevens wordt ondersteund door Cosmos DB SQL API-accounts. Na het lezen van dit artikel, kunt u zich de volgende vragen beantwoorden:

* Hoe kan ik ruimtelijke gegevens opslaan in Azure Cosmos DB?
* Hoe kan ik georuimtelijke gegevens in Azure Cosmos DB in SQL en LINQ op te vragen?
* Hoe ik in- of uitschakelen ruimtelijke indexering in Azure Cosmos DB?

Dit artikel leest hoe u werkt met ruimtelijke gegevens met de SQL-API. Raadpleeg deze [GitHub-project](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) voor codevoorbeelden.

## <a name="introduction-to-spatial-data"></a>Inleiding tot ruimtelijke gegevens
Ruimtelijke gegevens beschrijft de positie en de vorm van objecten in de ruimte. In de meeste toepassingen, deze komen overeen met de objecten op de aarde en georuimtelijke gegevens. Ruimtelijke gegevens kunnen worden gebruikt om weer te geven van de locatie van een persoon, een plaats van belang of de grens van een plaats of een meer. Veelgebruikte toepassingsgebieden vaak betrekking hebben op nabijheidsquery's, bijvoorbeeld, "alle in internetcafés vinden in de buurt van mijn huidige locatie." 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB biedt ondersteuning voor indexeren en query's naar georuimtelijke gegevens die is aangeduid met behulp van de [GeoJSON-specificatie](https://tools.ietf.org/html/rfc7946). GeoJSON-gegevensstructuren zijn altijd geldig JSON-objecten, zodat ze kunnen worden opgeslagen en opgevraagd met behulp van Azure Cosmos DB zonder speciale hulpprogramma's of bibliotheken. De Azure Cosmos DB SDK's bieden helperklassen en methoden die het gemakkelijk om te werken met ruimtelijke gegevens. 

### <a name="points-linestrings-and-polygons"></a>Punten, LineStrings en veelhoeken
Een **punt** geeft aan een enkele positie in de ruimte. Een punt vertegenwoordigt in georuimtelijke gegevens, de exacte locatie, wat erop kan een adres van een winkelketen, een kiosk, een auto of een plaats.  Een punt wordt weergegeven in de combinatie van GeoJSON (en de Azure Cosmos DB) met behulp van de coördinaat of lengtegraad en breedtegraad. Hier volgt een voorbeeld-JSON voor een punt.

**Punten in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> De GeoJSON-specificatie bevat lengtegraad eerste en breedtegraad tweede. Net als in andere toepassingen toewijzing lengtegraad en breedtegraad zijn hoeken en weergegeven in termen van graden. Waarden voor lengtegraad van de nulmeridiaan worden gemeten en tussen-180 graden en 180.0 graden en waarden voor breedtegraad van de evenaar worden gemeten en tussen-90.0 graden en 90.0 graden zijn. 
> 
> Azure Cosmos DB interpreteert coördinaten zoals voorgesteld per de referentiesysteem WGS 84. Zie hieronder voor meer informatie over de coördinaat referentiesystemen.
> 
> 

Dit kan worden ingesloten in een Azure Cosmos DB-document zoals wordt weergegeven in dit voorbeeld van een gebruikersprofiel met van locatiegegevens:

**Profiel met de locatie die zijn opgeslagen in Azure Cosmos DB gebruiken**

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

Naast de punten GeoJSON biedt ook ondersteuning voor LineStrings en veelhoeken. **LineStrings** vertegenwoordigt een reeks van twee of meer punten in de ruimte en de lijnsegmenten waarmee ze zijn verbonden. Georuimtelijke gegevens, worden de LineStrings veelvuldig gebruikt om snelwegen of rivieren vertegenwoordigen. Een **veelhoek** is een grens van de verbonden punten die een gesloten LineString vormt. Veelhoeken worden veelvuldig gebruikt om natuurlijke ontstaan wachtrijen zoals meren of politieke jurisdicties zoals plaatsen en Staten vertegenwoordigen. Hier volgt een voorbeeld van een Polygoon in Azure Cosmos DB. 

**Veelhoeken in GeoJSON**

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

Naast Point, LineString en Veelhoek GeoJSON ook Hiermee geeft u de weergave voor hoe moet worden gegroepeerd meerdere georuimtelijke locaties, evenals de eigenschappen van willekeurige koppelen aan geolocatie als een **functie**. Aangezien deze objecten geldige JSON zijn, kunnen ze allemaal worden opgeslagen en verwerkt in Azure Cosmos DB. Maar Azure Cosmos DB biedt alleen ondersteuning automatische indexering van punten.

### <a name="coordinate-reference-systems"></a>Coördinaat referentiesystemen
Omdat de vorm van de aarde onregelmatig is, worden in veel coördinaat referentiesystemen (CRS), elk met hun eigen frames van referentie- en maateenheden coördinaten van georuimtelijke gegevens weergegeven. Bijvoorbeeld, de 'nationale raster van Brittannië' is een referentiesysteem juist is voor het Verenigd Koninkrijk, maar niet daarbuiten wordt weergegeven. 

De meest populaire CRS in gebruik is vandaag de wereld geodetisch systeem [WGS 84](https://earth-info.nga.mil/GandG/update/index.php). Gebruik WGS 84 GPS-apparaten en veel toewijzing diensten, waaronder Google Maps en Bing Maps-API's. Azure Cosmos DB biedt ondersteuning voor indexeren en query's naar georuimtelijke gegevens met behulp van de WGS 84 CRS alleen. 

## <a name="creating-documents-with-spatial-data"></a>Het maken van documenten met ruimtelijke gegevens
Bij het maken van documenten die de GeoJSON-waarden bevatten, worden ze automatisch geïndexeerd met een ruimtelijke index in overeenstemming met het indexeringsbeleid van de container. Als u met een Azure Cosmos DB SDK in een dynamisch getypeerde taal, zoals Python of Node.js werkt, moet u geldige GeoJSON maken.

**Document met georuimtelijke gegevens in Node.js maken**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Als u met de SQL-API's werkt, kunt u de `Point` en `Polygon` klassen binnen de `Microsoft.Azure.Documents.Spatial` naamruimte voor het insluiten van locatiegegevens in uw toepassingsobjecten. Deze klassen te vereenvoudigen de serialisatie en deserialisatie van ruimtelijke gegevens in GeoJSON.

**Document met georuimtelijke gegevens in .NET maken**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Als u niet beschikt over de breedte graad en lengte graad, maar de fysieke adressen of locatie naam als plaats of land/regio hebt, kunt u de werkelijke coördinaten opzoeken met behulp van een geocoderings service zoals Bing Maps REST services. Meer informatie over de geocodering van Bing Maps [hier](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Uitvoeren van ruimtelijke typen query 's
Nu dat we kijken hoe u georuimtelijke gegevens invoegen, we gaan kijken hoe u query's van deze gegevens met behulp van Azure Cosmos DB met behulp van SQL en LINQ hebt genomen.

### <a name="spatial-sql-built-in-functions"></a>Ruimtelijke SQL ingebouwde functies
Azure Cosmos DB ondersteunt de volgende Open georuimtelijke Consortium (OGC) ingebouwde functies voor georuimtelijke query's. Zie voor meer informatie over de volledige reeks ingebouwde functies in de SQL-taal, [Query Azure Cosmos DB](how-to-sql-query.md).

|**Gebruik**|**Beschrijving**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Retourneert de afstand tussen de twee GeoJSON Point-, Polygon- of LineString-expressies.|
|ST_WITHIN (spatial_expr, spatial_expr) | Retourneert een Booleaanse expressie die aangeeft of het eerste GeoJSON-object (Point, Polygon of LineString) zich bevindt in het tweede GeoJSON-object (punt, Polygon of LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retourneert een Booleaanse expressie die aangeeft of de twee opgegeven GeoJSON-objecten (Point, Polygon of LineString) elkaar snijden.|
|ST_ISVALID| Retourneert een Booleaanse waarde die aangeeft of de opgegeven GeoJSON Point-, Polygon- of LineString-expressie geldig is.|
| ST_ISVALIDDETAILED| Retourneert een JSON-waarde met een Booleaanse waarde die aangeeft of de opgegeven GeoJSON Point-, Polygon- of LineString-expressie geldig is. Als de expressie ongeldig is, worden ook de reden daarvoor en een tekenreekswaarde geretourneerd.|

Ruimtelijke functies kunnen worden gebruikt om nabijheidsquery's uit te voeren op ruimtelijke gegevens. Bijvoorbeeld, als volgt een query waarmee alle familie documenten die binnen 30 kilometer van de opgegeven locatie met behulp van de ingebouwde functie ST_DISTANCE retourneert. 

**Query**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Als u ruimtelijke indexering in uw indexeringsbeleid, wordt klikt u vervolgens 'afstand query's ' efficiënt worden uitgevoerd via de index. Zie de sectie hieronder voor meer informatie over het ruimtelijke indexering. Als u een ruimtelijke index voor de opgegeven paden hebt, kunt u nog steeds ruimtelijke query's uitvoeren door op te geven `x-ms-documentdb-query-enable-scan` aanvraagheader met de waarde die is ingesteld op 'true'. In .NET, kan dit worden gedaan door te geven de optionele **FeedOptions** argument voor query's met [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) is ingesteld op true. 

ST_WITHIN kan worden gebruikt om te controleren als een punt zich binnen een veelhoek. Veelhoeken worden vaak gebruikt om grenzen als postcodes, status grenzen of natuurlijke ontstaan wachtrijen vertegenwoordigen. Het opnieuw als u ruimtelijke indexering in uw indexeringsbeleid, klikt u vervolgens 'in'-query's wordt efficiënt worden uitgevoerd via de index. 

Veelhoek argumenten in ST_WITHIN mag slechts één keer, dat wil zeggen, de veelhoeken niet gaten erin moeten bevatten. 

**Query**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Results**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Vergelijkbaar met hoe niet-overeenkomende typen werk in Azure Cosmos DB-query, als de locatiewaarde die is opgegeven in een argument is onjuist gevormd of ongeldig, en vervolgens dit resulteert in **niet-gedefinieerde** en de geëvalueerde document uit de queryresultaten worden overgeslagen. Als uw query geen resultaten worden geretourneerd, voert u ST_ISVALIDDETAILED voor foutopsporing waarom het ruimtelijke gegevenstype ongeldig is.     
> 
> 

Azure Cosmos DB ondersteunt ook omgekeerde query's uitvoert, dat wil zeggen, kunt u veelhoeken of regels in een Azure Cosmos DB indexeren, en vervolgens op te vragen voor de gebieden die een opgegeven punt bevatten. Dit patroon wordt vaak gebruikt in logistiek te identificeren, bijvoorbeeld wanneer een vrachtwagen binnengaat of een aangewezen gebied verlaat. 

**Query**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Results**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID en ST_ISVALIDDETAILED kunnen worden gebruikt om te controleren of een ruimtelijk object geldig is. Bijvoorbeeld, controleert de volgende query de geldigheid van een punt met een out-of breedtegraad bereikwaarde (-132.8). ST_ISVALID retourneert alleen een Booleaanse waarde en ST_ISVALIDDETAILED retourneert de Booleaanse waarde en een tekenreeks met de reden waarom het wordt als ongeldig wordt beschouwd.

**Query**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Deze functies kunnen ook worden gebruikt voor het valideren van veelhoeken. Bijvoorbeeld, hier gebruiken we ST_ISVALIDDETAILED voor het valideren van een Polygoon die niet wordt afgesloten. 

**Query**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Results**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ-query's uitvoeren in de .NET SDK
De SQL .NET SDK ook providers stub-methoden `Distance()` en `Within()` voor gebruik binnen het LINQ-expressies. De SQL LINQ-provider vertaalt deze methode aanroepen naar de equivalente SQL ingebouwde functieaanroepen (ST_DISTANCE en ST_WITHIN respectievelijk). 

Hier volgt een voor beeld van een LINQ-query waarmee alle documenten in de Azure Cosmos-container worden gevonden waarvan de locatie waarde binnen een straal van 30 km van het opgegeven punt met behulp van LINQ is.

**LINQ-query voor afstand**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Op dezelfde manier als volgt een query voor het zoeken naar alle documenten waarvan "locatie" binnen de opgegeven box/veelhoek is. 

**LINQ op te vragen voor binnen**

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

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Nu dat we een overzicht van hoe u documenten met behulp van LINQ en SQL query hebt gemaakt, laten we eens over het configureren van Azure Cosmos DB voor ruimtelijke indexering.

## <a name="indexing"></a>Indexeren
Zoals wordt beschreven in de [Schema-agnostische indexering met Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papier, we ontworpen voor Azure Cosmos DB-database-engine moet echt schema-agnostische en eersteklas ondersteuning voor JSON. De schrijven geoptimaliseerde database-engine van Azure Cosmos DB begrijpt systeemeigen ruimtelijke gegevens (punten, veelhoeken en regels) weergegeven in de GeoJSON-standaard.

Kortom, de geometrie is geprojecteerd van geodetisch coördinaten op een 2D vlak vervolgens geleidelijk worden onderverdeeld in cellen met behulp van een **quadtree**. Als u deze cellen zijn toegewezen aan 1D op basis van de locatie van de cel binnen een **Hilbert ruimte vullen curve**, die de locatie van de punten blijft behouden. Daarnaast wanneer locatiegegevens wordt geïndexeerd, doorloopt een proces dat ook wel **mozaïekpatroon**, dat wil zeggen, de cellen die elkaar een locatie overlappen worden geïdentificeerd en opgeslagen als sleutels in de Azure Cosmos DB-index. Op het moment dat de query, argumenten, zoals punten en polygonen zijn ook representatie mozaïekpatroon om op te halen van de relevante ID celbereiken, en vervolgens gebruikt voor het ophalen van gegevens uit de index.

Als u een indexeringsbeleid met ruimtelijke index voor / * (alle paden), en vervolgens alle punten gevonden binnen de verzameling worden geïndexeerd voor efficiënte ruimtelijke query's (ST_WITHIN en ST_DISTANCE). Ruimtelijke indexen niet hebben een precisiewaarde, en gebruik altijd een standaardwaarde van de precisie.

> [!NOTE]
> Azure Cosmos DB ondersteunt automatische indexering van punten en polygonen LineStrings
> 
> 

Het volgende JSON-fragment toont een indexeringsbeleid met ruimtelijke indexering ingeschakeld, dat wil zeggen, index-enig GeoJSON gevonden in documenten voor ruimtelijke query's. Als u het indexeringsbeleid met behulp van de Azure portal wijzigt, kunt u de volgende JSON voor het indexeringsbeleid om in te schakelen ruimtelijke indexering voor uw verzameling opgeven.

**Verzameling indexeren van JSON met Spatial ingeschakeld voor punten en polygonen beleid**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Hier volgt een codefragment in .NET die laat hoe u een verzameling maken zien met een ruimtelijke indexering ingeschakeld voor alle paden die bevatten. 

**Een verzameling maken met een ruimtelijke indexering**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

En dit is hoe u een bestaande verzameling om te profiteren van ruimtelijke indexering via alle punten die zijn opgeslagen in de documenten kunt wijzigen.

**Een bestaande verzameling met ruimtelijke indexering wijzigen**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Als de locatie GeoJSON-waarde in het document onjuist gevormd of ongeldig is, worden klikt u vervolgens het niet geïndexeerd voor ruimtelijke query's. U kunt de locatie waarden met behulp van ST_ISVALID en ST_ISVALIDDETAILED valideren.
> 
> Als de definitie van de verzameling een partitiesleutel bevat, wordt indexering van de voortgang van de transformatie niet gerapporteerd. 
> 
> 

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, naast kunt u:

* Gaan coderen met de [georuimtelijke .NET-codevoorbeelden op GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Aan de slag met georuimtelijke query's uitvoeren op de [Azure Cosmos DB testomgeving voor Query's](https://www.documentdb.com/sql/demo#geospatial)
* Meer informatie over [Azure Cosmos DB-Query](how-to-sql-query.md)
* Meer informatie over [Azure Cosmos DB indexeren van beleid](index-policy.md)

