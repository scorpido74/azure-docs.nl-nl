---
title: Inleiding tot Azure Stream Analytics georuimtelijke functies
description: Dit artikel wordt beschreven georuimtelijke functies die worden gebruikt in Azure Stream Analytics-taken.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426231"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Inleiding tot Stream Analytics georuimtelijke functies

Georuimtelijke functies in Azure Stream Analytics kunnen realtime analyses over het streamen van georuimtelijke gegevens. Met slechts een paar regels code, kunt u een oplossing voor productie geschikt voor complexe scenario's te ontwikkelen. 

Voorbeelden van scenario's die van georuimtelijke functies profiteren kunnen zijn:

* Rijpositie delen
* Wagenparkbeheer
* Bijhouden van assets
* Geofencing
* Telefoon bijhouden op cel sites

Querytaal voor Stream Analytics beschikt over zeven ingebouwde georuimtelijke functies: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE** , **ST_OVERLAPS**, **ST_INTERSECTS**, en **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

De `CreateLineString` functie punten accepteert en retourneert een LineString van GeoJSON, die kunnen worden weergegeven als een regel op een kaart. U moet ten minste twee punten om te maken van een LineString hebben. De punten LineString worden, in volgorde verbonden.

De volgende query gebruikt `CreateLineString` te maken van een LineString met behulp van de drie punten. Het eerste punt wordt gemaakt van streaming-gegevens, terwijl de andere twee handmatig worden gemaakt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Breedtegraad|Lengtegraad|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 {"type": "LineString", "coördinaten": [[-10.2, 3.0], [10.0, 10.0], [10,5, 10,5]]}

 {"type": "LineString", "coördinaten": [[20.2321,-87.33], [10.0, 10.0], [10,5, 10,5]]}

Voor meer informatie gaat u naar de [CreateLineString](https://docs.microsoft.com/stream-analytics-query/createlinestring) verwijzing.

## <a name="createpoint"></a>CreatePoint

De `CreatePoint` functie accepteert een breedtegraad en lengtegraad en retourneert een GeoJSON-punt op een kaart kan worden getekend. Uw Latitude en lengten moet een **float** gegevenstype.

Maakt gebruik van de volgende voorbeeldquery `CreatePoint` om een te maken met behulp van Latitude en lengten van streaming-gegevens.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Breedtegraad|Lengtegraad|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer
  
 {"type": "Punt", "coördinaten": [-10.2, 3.0]}  
  
 {"type": "Punt", "coördinaten": [20.2321,-87.33]}  

Voor meer informatie gaat u naar de [CreatePoint](https://docs.microsoft.com/stream-analytics-query/createpoint) verwijzing.

## <a name="createpolygon"></a>CreatePolygon

De `CreatePolygon` functie punten accepteert en retourneert een record GeoJSON-veelhoek. De volgorde van punten in de rechter ring afdrukstand moet volgen of linksom draaien. Stel lopen van één plaats naar de andere in de volgorde waarin die ze zijn gedeclareerd. Het midden van de polygoon zou worden aan de linkerkant van de gehele tijd.

Maakt gebruik van de volgende voorbeeldquery `CreatePolygon` te maken van een polygoon van de drie punten. De eerste twee punten handmatig worden gemaakt, en het laatste punt is gemaakt op basis van invoergegevens.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|Breedtegraad|Lengtegraad|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 {"type": "Veelhoek", "coördinaten": [[[-10.2, 3.0], [10.0, 10.0], [10,5, 10,5], [-10.2, 3.0]]]}
 
 {"type": "Veelhoek", "coördinaten": [[[20.2321,-87.33], [10.0, 10.0], [10,5, 10,5], [20.2321,-87.33]]]}

Voor meer informatie gaat u naar de [CreatePolygon](https://docs.microsoft.com/stream-analytics-query/createpolygon) verwijzing.


## <a name="st_distance"></a>ST_DISTANCE
De `ST_DISTANCE` functie retourneert de afstand tussen de twee punten in meters. 

De volgende query gebruik `ST_DISTANCE` voor het genereren van een gebeurtenis wanneer een Tankstation minder dan 10 km vanuit de auto is.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Voor meer informatie gaat u naar de [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) verwijzing.

## <a name="st_overlaps"></a>ST_OVERLAPS
De `ST_OVERLAPS` functie vergelijkt twee veelhoeken. Als de veelhoeken elkaar overlappen, retourneert de functie een 1. De functie retourneert 0 als de veelhoeken niet overlappen. 

De volgende query gebruik `ST_OVERLAPS` voor het genereren van een gebeurtenis wanneer een gebouw zich binnen een zone vollopen mogelijk.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

De volgende voorbeeldquery genereert een gebeurtenis wanneer een storm gaat naar een auto heen.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Voor meer informatie gaat u naar de [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) verwijzing.

## <a name="st_intersects"></a>ST_INTERSECTS
De `ST_INTERSECTS` functie vergelijkt twee LineString. Als de LineString elkaar overlappen, retourneert de functie 1. De functie retourneert 0 als de LineString elkaar niet overlappen.

Maakt gebruik van de volgende voorbeeldquery `ST_INTERSECTS` om te bepalen als een geplaveide weg samen met een vuil weg.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "Lines Tring", "coördinaten": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "Lines Tring", "coördinaten": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"type": "Lines Tring", "coördinaten": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"type": "Lines Tring", "coördinaten": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 1  
  
 0  

Voor meer informatie gaat u naar de [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) verwijzing.

## <a name="st_within"></a>ST_WITHIN
De `ST_WITHIN` functie bepaalt of een punt of veelhoek binnen een veelhoek is. Als de veelhoek het punt of de veelhoek bevat, de functie geeft als resultaat 1. De functie retourneert 0 als het punt of de veelhoek niet binnen de gedeclareerde polygoon bevindt.

Maakt gebruik van de volgende voorbeeldquery `ST_WITHIN` om te bepalen of de levering van bestemmingspunt binnen de opgegeven datawarehouse-veelhoek.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Voorbeeld van invoer  
  
|deliveryDestination|datawarehouse|  
|-------------------------|---------------|  
|{"type": "punt", "coördinaten": [76,6, 10,1]}|{"type": "veelhoek", "coördinaten": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"type": "punt", "coördinaten": [15,0, 15,0]}|{"type": "veelhoek", "coördinaten": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Voorbeeld van uitvoer  

 0  
  
 1  

Voor meer informatie gaat u naar de [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) verwijzing.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
