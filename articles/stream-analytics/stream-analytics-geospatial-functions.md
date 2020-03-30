---
title: Inleiding tot georuimtelijke functies van Azure Stream Analytics
description: In dit artikel worden georuimtelijke functies beschreven die worden gebruikt in Azure Stream Analytics-taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426231"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Inleiding tot georuimtelijke functies van Stream Analytics

Georuimtelijke functies in Azure Stream Analytics maken realtime analyses mogelijk voor het streamen van georuimtelijke gegevens. Met slechts een paar regels code u een oplossing van productiekwaliteit ontwikkelen voor complexe scenario's. 

Voorbeelden van scenario's die kunnen profiteren van georuimtelijke functies zijn:

* Ride-sharing
* Fleet management
* Bijhouden van assets
* Geo-fencing
* Telefoontracking in mobiele sites

Stream Analytics Query Language heeft zeven ingebouwde georuimtelijke functies: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE,** **ST_OVERLAPS,** **ST_INTERSECTS**en **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

De `CreateLineString` functie accepteert punten en retourneert een GeoJSON LineString, die kan worden uitgezet als een lijn op een kaart. U moet ten minste twee punten hebben om een linestring te maken. De LineString-punten worden in volgorde verbonden.

De volgende `CreateLineString` query wordt gebruikt om een linestring te maken met behulp van drie punten. Het eerste punt wordt gemaakt op uit streaming invoergegevens, terwijl de andere twee handmatig worden gemaakt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 {"type" : "LineString", "coördinaten" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coördinaten" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Ga voor meer informatie naar de referentie [CreateLineString.](https://docs.microsoft.com/stream-analytics-query/createlinestring)

## <a name="createpoint"></a>CreatePoint

De `CreatePoint` functie accepteert een breedte- en lengtegraad en retourneert een GeoJSON-punt, dat op een kaart kan worden uitgezet. Uw breedte- en lengtegraden moeten een **zwevend** gegevenstype zijn.

In het volgende `CreatePoint` voorbeeld wordt query gebruikt om een punt te maken met behulp van breedtegraden en lengtegraden op basis van streaminginvoergegevens.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer
  
 {"type" : "Punt", "coördinaten" : [-10.2, 3.0]}  
  
 {"type" : "Punt", "coördinaten" : [20.2321, -87.33]}  

Ga voor meer informatie naar de [CreatePoint-verwijzing.](https://docs.microsoft.com/stream-analytics-query/createpoint)

## <a name="createpolygon"></a>CreatePolygon

De `CreatePolygon` functie accepteert punten en retourneert een GeoJSON-veelhoekrecord. De volgorde van de punten moet de juiste ringoriëntatie volgen, of tegen de klok in. Stel je voor dat je van het ene punt naar het andere loopt in de volgorde waarin ze werden verklaard. Het midden van de veelhoek zou de hele tijd aan je linkerkant zijn.

In de volgende `CreatePolygon` voorbeeldquery wordt een veelhoek gemaakt van drie punten. De eerste twee punten worden handmatig gemaakt en het laatste punt wordt gemaakt op maken van invoergegevens.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 {"type" : "Polygon", "coördinaten" : [[-10,2, 3,0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coördinaten" : [[20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]] }

Ga voor meer informatie naar de verwijzing [CreatePolygon.](https://docs.microsoft.com/stream-analytics-query/createpolygon)


## <a name="st_distance"></a>ST_DISTANCE
De `ST_DISTANCE` functie geeft de afstand tussen twee punten in meters. 

De volgende `ST_DISTANCE` query wordt gebruikt om een gebeurtenis te genereren wanneer een tankstation zich op minder dan 10 km van de auto bevindt.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Ga voor meer informatie naar de [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) referentie.

## <a name="st_overlaps"></a>ST_OVERLAPS
De `ST_OVERLAPS` functie vergelijkt twee veelhoeken. Als de veelhoeken elkaar overlappen, geeft de functie een 1 als resultaat. De functie geeft 0 als de veelhoeken elkaar niet overlappen. 

De volgende `ST_OVERLAPS` query wordt gebruikt om een gebeurtenis te genereren wanneer een gebouw zich binnen een mogelijke overstromingszone bevindt.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

De volgende voorbeeldquery genereert een gebeurtenis wanneer een storm op een auto afkomt.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Ga voor meer informatie naar de [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) referentie.

## <a name="st_intersects"></a>ST_INTERSECTS
De `ST_INTERSECTS` functie vergelijkt twee LineString. Als de lijntekenreeks elkaar kruist, wordt de functie 1 geretourneerd. De functie geeft 0 als de lijntekenreeks elkaar niet kruist.

In de volgende `ST_INTERSECTS` voorbeeldquery wordt bepaald of een verharde weg een onverharde weg kruist.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "coördinaten": [ [-10,0, 0,0], [0,0, 0,0], [10,0, 0,0] ]}|{"type":"LineString", "coördinaten": [ [0,0, 10,0], [0,0, 0,0], [0,0, -10,0] ]}|  
|{"type":"LineString", "coördinaten": [ [-10,0, 0,0], [0,0, 0,0], [10,0, 0,0] ]}|{"type":"LineString", "coördinaten": [ [-10,0, 10,0], [0,0, 10,0], [10,0, 10,0] ]}|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 1  
  
 0  

Ga voor meer informatie naar de [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) referentie.

## <a name="st_within"></a>ST_WITHIN
De `ST_WITHIN` functie bepaalt of een punt of veelhoek zich binnen een veelhoek bevindt. Als de veelhoek het punt of de veelhoek bevat, wordt de functie 1. De functie geeft 0 als het punt of de veelhoek zich niet binnen de opgegeven veelhoek bevindt.

In de volgende `ST_WITHIN` voorbeeldquery wordt bepaald of het leveringsdoelpunt zich binnen de opgegeven magazijnveelhoek bevindt.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|deliveryBestemming|Magazijn|  
|-------------------------|---------------|  
|{"type":"Punt", "coördinaten": [76.6, 10.1]}|{"type":"Polygon", "coördinaten": [ [0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0] ]}|  
|{"type":"Punt", "coördinaten": [15.0, 15.0]}|{"type":"Polygon", "coördinaten": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 0  
  
 1  

Ga voor meer informatie naar de [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) referentie.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
