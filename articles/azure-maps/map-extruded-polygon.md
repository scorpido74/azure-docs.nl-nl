---
title: Een polygoonextrusielaag toevoegen aan een kaart | Microsoft Azure Maps
description: Een polygoonextrusielaag toevoegen aan de Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334546"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Een polygoonextrusielaag toevoegen aan de kaart

In dit artikel ziet u hoe u de polygoonextrusielaag gebruiken om gebieden van `Polygon` geometrieën weer te geven en `MultiPolygon` te gebruiken als geëxtrudeerde vormen. De Azure Maps Web SDK ondersteunt het renderen van Circle-geometrieën zoals gedefinieerd in het [uitgebreide GeoJSON-schema.](extend-geojson.md#circle) Deze cirkels kunnen worden omgezet in polygonen wanneer weergegeven op de kaart. Alle geometrieën van de functie kunnen eenvoudig worden bijgewerkt wanneer ze met de atlas zijn [verpakt. Vormklasse.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Een polygoonextrusielaag gebruiken

Verbind de [polygoonextrusielaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) met een gegevensbron. Vervolgens, geladen op de kaart. De polygoon extrusielaag `Polygon` geeft `MultiPolygon` de gebieden van a en functies als geëxtrudeerde vormen. De `height` `base` eigenschappen en eigenschappen van de polygoonextrusielaag definiëren de basisafstand van de grond en hoogte van de geëxtrudeerde vorm in **meters.** In de volgende code ziet u hoe u een veelhoek maakt, deze toevoegt aan een gegevensbron en deze weergeeft met de klasse Polygoon-extrusielaag.

> [!Note]
> De `base` waarde die in de polygoonextrusielaag is gedefinieerd, moet kleiner zijn dan of gelijk zijn aan die van de `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Geëxtrudeerde veelhoek" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>geëxtrudeerde veelhoek</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Gegevensgestuurde veelhoeken toevoegen

Een choropleth kaart kan worden weergegeven met behulp van de polygoon extrusie laag. Stel `height` de `fillColor` eigenschappen en eigenschappen van de extrusielaag `Polygon` in `MultiPolygon` op de meting van de statistische variabele in de geometrieën en de functie. De volgende code monster toont een geëxtrudeerde choropleth Kaart van de VS op basis van de meting van de bevolkingsdichtheid per staat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Geëxtrudeerde choropleth kaart" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth-kaart</a> van Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="add-a-circle-to-the-map"></a>Een cirkel toevoegen aan de kaart

Azure Maps maakt gebruik van een uitgebreide versie van het GeoJSON-schema die een definitie biedt voor cirkels zoals [hier](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)wordt opgemerkt. Een geëxtrudeerde cirkel kan op de `point` kaart `subType` worden `Circle` weergegeven door `Radius` een functie te maken met een eigenschap van en een genummerde eigenschap die de straal in **meters**vertegenwoordigt. Bijvoorbeeld:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

De Azure Maps Web `Point` SDK `Polygon` zet deze functies om in functies onder de motorkap. Deze `Point` functies kunnen worden weergegeven op de kaart met behulp van polygoon extrusie laag zoals weergegeven in de volgende code monster.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone luchtruim polygoon" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen Drone luchtruim<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polygoon</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Een polygoonextrusielaag aanpassen

De Polygon Extrusie laag heeft verschillende styling opties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Veelhoek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygoonextrusielaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Aanvullende bronnen:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON-specificatie-extensie](extend-geojson.md#circle)
