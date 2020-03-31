---
title: GeoJSON-gegevensformaat voor geofence | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het voorbereiden van de geofence-gegevens die kunnen worden gebruikt in de Microsoft Azure Maps GET- en POST Geofence-API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335625"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON data

Met de Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) en [POST Geofence](/rest/api/maps/spatial/postgeofence) API's u de nabijheid van een coördinaat ophalen ten opzichte van een meegeleverde geofence of een set hekken. In dit artikel wordt beschreven hoe u de geofence-gegevens voorbereidt die kunnen worden gebruikt in de Azure Maps GET- en POST-API.

De gegevens voor geofence of set geofences `FeatureCollection` worden `GeoJSON` weergegeven door `Feature` Object en Object in formaat, die is gedefinieerd in [rfc7946](https://tools.ietf.org/html/rfc7946). Daarnaast:

* Het objecttype GeoJSON `Feature` kan een `FeatureCollection` object of object zijn.
* Het type object geometrie `Point`kan `MultiPoint` `LineString`een `MultiLineString` `Polygon`, `MultiPolygon`, `GeometryCollection`, , en .
* Alle eigenschappen van `geometryId`de functie moeten een , die wordt gebruikt voor het identificeren van de geofence bevatten.
* Functie `Point`met `MultiPoint` `LineString`, `MultiLineString` , `radius` moet bevatten in eigenschappen. `radius`waarde wordt gemeten in `radius` meters, de waarde varieert van 1 tot 10000.
* Functie `polygon` met `multipolygon` en geometrietype heeft geen straaleigenschap.
* `validityTime`is een optionele eigenschap waarmee de gebruiker verlopen tijd en geldigheidsperiode voor de geofence-gegevens kan instellen. Als deze niet is opgegeven, verlopen de gegevens nooit en zijn ze altijd geldig.
* Het `expiredTime` is de vervaldatum en het tijdstip van geofencing gegevens. Als de `userTime` waarde van de aanvraag later is dan deze waarde, worden de bijbehorende geofencegegevens beschouwd als verlopen gegevens en worden ze niet opgevraagd. Waarop de geometrieid van deze geofence-gegevens `expiredGeofenceGeometryId` in array wordt opgenomen in de geofence-respons.
* Het `validityPeriod` is een lijst van geldigheidsperiode van de geofence. Als de `userTime` waarde van de aanvraag buiten de geldigheidsperiode valt, worden de bijbehorende geofencegegevens als ongeldig beschouwd en worden ze niet opgevraagd. De geometrieId van deze geofence `invalidPeriodGeofenceGeometryId` gegevens is opgenomen in array binnen geofence respons. In de volgende tabel worden de eigenschappen van het element Geldigheidsperiode weergegeven.

| Name | Type | Vereist  | Beschrijving |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum/tijd  | waar | De begindatum van de geldigheidsperiode. |
| endTime   | Datum/tijd  | waar |  De einddatum van de geldigheidsperiode. |
| Recurrencetype | tekenreeks | false |   Het herhalingstype van de periode. De waarde `Daily`kan `Weekly` `Monthly`worden `Yearly`, , , of . Standaardwaarde `Daily`is .|
| businessDayOnly | Booleaans | false |  Geef aan of de gegevens alleen geldig zijn tijdens werkdagen. Standaardwaarde `false`is .|


* Alle coördinatenwaarden worden weergegeven als [lengtegraad, `WGS84`breedtegraad] gedefinieerd in .
* Voor elke functie, `MultiPoint` `MultiLineString`die `MultiPolygon` de `GeometryCollection`eigenschappen bevat, , of de eigenschappen worden toegepast op alle elementen. bijvoorbeeld: Alle punten `MultiPoint` in zullen dezelfde straal gebruiken om een geofence met meerdere cirkels te vormen.
* In het puntcirkelscenario kan een cirkelgeometrie worden `Point` weergegeven met behulp van een geometrieobject met eigenschappen die zijn uitgewerkt in [GeoJSON-geometrieën uitbreiden.](https://docs.microsoft.com/azure/azure-maps/extend-geojson)      

Hieronder volgt een voorbeeldaanvraaginstantie voor een geofence die wordt `GeoJSON` weergegeven als een cirkelgeofence-geometrie in het gebruik van een middelpunt en een straal. De geldige periode van de geofence-gegevens begint van 2018-10-22, 9.00 tot 17.00 uur, elke dag herhaald, behalve in het weekend. `expiredTime`geeft aan dat deze geofence-gegevens `userTime` als verlopen worden `2019-01-01`beschouwd, als in de aanvraag later dan .  

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
