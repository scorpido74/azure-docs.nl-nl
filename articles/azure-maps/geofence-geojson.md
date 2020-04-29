---
title: Geojson-gegevens indeling voor geofence | Microsoft Azure kaarten
description: In dit artikel leert u hoe u de geofence-gegevens voorbereidt die kunnen worden gebruikt in de Microsoft Azure Maps-kaarten krijgen en POST geofence API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335625"
---
# <a name="geofencing-geojson-data"></a>Geojson-gegevens geoomheining

Met de Azure Maps [geofence ophalen](/rest/api/maps/spatial/getgeofence) en [post geofence](/rest/api/maps/spatial/postgeofence) -api's kunt u de nabijheid van een coördinaat ophalen ten opzichte van een opgegeven geofence of set omheiningen. In dit artikel wordt beschreven hoe u de geofence-gegevens voorbereidt die kunnen worden gebruikt in de Azure Maps GET en POST-API.

De gegevens voor geofence of set geofences `Feature` worden vertegenwoordigd door object en `FeatureCollection` object in `GeoJSON` indeling, dat is gedefinieerd in [rfc7946](https://tools.ietf.org/html/rfc7946). Naast het volgende:

* Het geojson-object type kan een `Feature` object of een `FeatureCollection` object zijn.
* Het object type Geometry kan `Point`een, `MultiPoint`, `LineString` `MultiLineString` `Polygon` `MultiPolygon`,,, en `GeometryCollection`zijn.
* Alle functie-eigenschappen moeten een `geometryId`bevatten, die wordt gebruikt om de geofence te identificeren.
* De functie `Point`met `MultiPoint`, `LineString`, `MultiLineString` , moet `radius` in eigenschappen bevatten. `radius`waarde wordt gemeten in meters, `radius` de waarde ligt tussen 1 en 10000.
* De functie `polygon` met `multipolygon` en het type geometrie heeft geen RADIUS-eigenschap.
* `validityTime`is een optionele eigenschap waarmee de gebruiker een verlopen tijd en geldigheids periode voor de geofence-gegevens kan instellen. Als u niets opgeeft, verlopen de gegevens nooit en zijn ze altijd geldig.
* De `expiredTime` is de verloop datum en-tijd van geoomheinings gegevens. Als de waarde van `userTime` in de aanvraag later is dan deze waarde, worden de bijbehorende geofence-gegevens beschouwd als verlopen gegevens en wordt er geen query uitgevoerd. Wanneer de geometryId van deze geofence-gegevens worden opgenomen in `expiredGeofenceGeometryId` de matrix binnen de geofence-respons.
* De `validityPeriod` is een lijst met de geldigheids periode van de geofence. Als de waarde van `userTime` in de aanvraag buiten de geldigheids periode valt, worden de bijbehorende geofence-gegevens als ongeldig beschouwd en wordt er geen query uitgevoerd. Het geometryId van deze geofence-gegevens is opgenomen `invalidPeriodGeofenceGeometryId` in de matrix binnen de geofence-respons. In de volgende tabel worden de eigenschappen van het element validityPeriod weer gegeven.

| Naam | Type | Vereist  | Beschrijving |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum/tijd  | waar | De begin datum en-tijd van de geldigheids periode. |
| endTime   | Datum/tijd  | waar |  De eind datum van de geldigheids periode. |
| recurrenceType | tekenreeks | false |   Het terugkeer type van de periode. De waarde kan, `Daily` `Weekly` `Monthly`, of `Yearly`zijn. De standaard waarde `Daily`is.|
| businessDayOnly | Booleaans | false |  Geef aan of de gegevens alleen geldig zijn tijdens werk dagen. De standaard waarde `false`is.|


* Alle coördinaat waarden worden weer gegeven als [lengte graad, breedte graad `WGS84`] gedefinieerd in.
* Voor elke functie, `MultiPoint`met, `MultiLineString`, `MultiPolygon` , of `GeometryCollection`, worden de eigenschappen toegepast op alle elementen. bijvoorbeeld: alle punten in `MultiPoint` zullen dezelfde RADIUS gebruiken om een geofence van meerdere cirkels te vormen.
* In het scenario van een punt kan de geometrie van een cirkel worden weer `Point` gegeven met behulp van een Geometry-object met eigenschappen die zijn opgenomen in de [uitbrei ding van geojson-geometrie](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Hieronder volgt een voor beeld van een aanvraag tekst voor een geofence die wordt weer gegeven als `GeoJSON` een cirkel geofence-geometrie in met behulp van een middel punt en een RADIUS. De geldigheids periode van de geofence-gegevens begint van 2018-10-22, 9:00 tot tot 17:00 uur, die elke dag wordt herhaald, met uitzonde ring van het weekend. `expiredTime`Hiermee wordt aangegeven dat deze geofence-gegevens als verlopen `userTime` worden beschouwd als de aanvraag later `2019-01-01`is dan.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
