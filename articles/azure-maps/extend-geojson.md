---
title: Uitgebreide GeoJSON-geometrieën | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over hoe Microsoft Azure Maps de GeoJSON-specificatie uitbreidt om bepaalde geometrieën weer te geven.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276398"
---
# <a name="extended-geojson-geometries"></a>Uitgebreide GeoJSON-geometrieën

Azure Maps biedt een lijst met krachtige API's om binnen en langs geografische functies te zoeken. Deze API's voldoen aan de standaard [GeoJSON-specificaties][1] van het weergeven van geografische kenmerken.  

De [GeoJSON-spec][1] ondersteunt alleen de volgende geometrieën:

* GeometrieVerzameling
* Lijntekenreeks
* MultilineString
* Multipoint
* Multipolygoon
* Punt
* Veelhoek

Sommige Azure Maps API's accepteren geometrieën die geen deel uitmaken van de [GeoJSON-specificatie.][1] De API [Zoeken in geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) accepteert bijvoorbeeld Circle en Polygons.

In dit artikel wordt uitgelegd hoe Azure Maps de [GeoJSON-specificaties][1] uitbreidt om bepaalde geometrieën weer te geven.

## <a name="circle"></a>Cirkel

De `Circle` geometrie wordt niet ondersteund door de [GeoJSON-spec][1]. We gebruiken `GeoJSON Point Feature` een object om een cirkel te vertegenwoordigen.

Een `Circle` geometrie die met `GeoJSON Feature` het object wordt __weergegeven, moet__ de volgende coördinaten en eigenschappen bevatten:

- Midden

    Het middelpunt van de cirkel `GeoJSON Point` wordt weergegeven met behulp van een object.

- Radius

    De cirkel `radius` wordt weergegeven `GeoJSON Feature`met behulp van 's eigenschappen. De straalwaarde _meters_ bevindt zich in `double`meters en moet van het type zijn.

- Subtype

    De cirkelgeometrie moet ook `subType` de eigenschap bevatten. Deze eigenschap moet deel `GeoJSON Feature`uitmaken van de 's eigenschappen en de waarde moet _Circle_

#### <a name="example"></a>Voorbeeld

Zo vertegenwoordigt u een cirkel met `GeoJSON Feature` een object. Laten we de cirkel op breedtegraad centreren: 47,639754 en lengte: -122.126986, en het toewijzen van een straal gelijk aan 100 meter:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rechthoek

De `Rectangle` geometrie wordt niet ondersteund door de [GeoJSON-spec][1]. We gebruiken `GeoJSON Polygon Feature` een object om een rechthoek weer te geven. De rechthoekextensie wordt voornamelijk gebruikt door de module tekengereedschappen van de Web SDK.

Een `Rectangle` geometrie die met `GeoJSON Polygon Feature` het object wordt __weergegeven, moet__ de volgende coördinaten en eigenschappen bevatten:

- Hoeken

    De hoeken van de rechthoek worden weergegeven `GeoJSON Polygon` met behulp van de coördinaten van een object. Er moeten vijf coördinaten zijn, één voor elke hoek. En, een vijfde coördinaat die hetzelfde is als de eerste coördinaat, om de veelhoekring te sluiten. Er wordt van uitgegaan dat deze coördinaten worden uitgelijnd en dat de ontwikkelaar ze naar gewenst kan roteren.

- Subtype

    De rechthoekgeometrie moet ook `subType` de eigenschap bevatten. Deze eigenschap moet deel `GeoJSON Feature`uitmaken van de eigenschappen van de 's, en de waarde moet _rechthoek_ zijn

### <a name="example"></a>Voorbeeld

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Volgende stappen

Meer informatie over GeoJSON-gegevens in Azure Maps:

> [!div class="nextstepaction"]
> [Geofence GeoJSON-indeling](geofence-geojson.md)

Bekijk de woordenlijst van algemene technische termen die zijn gekoppeld aan Azure Maps en locatie-intelligentietoepassingen:

> [!div class="nextstepaction"]
> [Azure Maps-woordenlijst](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
