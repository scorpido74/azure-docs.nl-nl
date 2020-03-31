---
title: Een veelhoeklaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een veelhoek en veelhoek op een kaart weergeeft in de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535049"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Een veelhoeklaag aan de kaart toevoegen

In dit artikel ziet u `Polygon` hoe `MultiPolygon` u de gebieden van geometrieën op de kaart weergeven met behulp van een veelhoeklaag. De Azure Maps Web SDK ondersteunt ook het maken van Circle-geometrieën zoals gedefinieerd in het [uitgebreide GeoJSON-schema.](extend-geojson.md#circle) Deze cirkels worden omgezet in polygonen wanneer weergegeven op de kaart. Alle geometrieën van de functie kunnen eenvoudig worden bijgewerkt wanneer ze met de atlas zijn [verpakt. Vormklasse.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Een veelhoeklaag gebruiken 

Wanneer een veelhoeklaag is verbonden met een gegevensbron en op `Polygon` `MultiPolygon` de kaart wordt geladen, wordt het gebied met en functies weergegeven. Als u een veelhoek wilt maken, voegt u deze toe aan een gegevensbron en maakt u deze weer met een veelhoeklaag met de klasse [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Hieronder vindt u het volledige en lopende voorbeeld van de bovenstaande code.

<br/>

<iframe height='500' scrolling='no' title='Een veelhoek toevoegen aan een kaart ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Een veelhoek toevoegen aan een kaart</a> met Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Een veelhoek- en lijnlaag samen gebruiken

Een lijnlaag wordt gebruikt om de omtrek van veelhoeken weer te geven. In het volgende codevoorbeeld wordt een veelhoek weergegeven zoals in het vorige voorbeeld, maar wordt nu een lijnlaag toegevoegd. Deze lijnlaag is een tweede laag die is verbonden met de gegevensbron.  

<iframe height='500' scrolling='no' title='Veelhoek- en lijnlaag om veelhoek toe te voegen' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>lijnlaag Penveelhoek en lijn om veelhoek toe te voegen</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Een veelhoek vullen met een patroon

Naast het vullen van een veelhoek met een kleur, u een afbeeldingspatroon gebruiken om de veelhoek te vullen. Laad een afbeeldingspatroon in de bronnen van de `fillPattern` afbeeldingsafbeelding sprite en verwijs vervolgens naar deze afbeelding met de eigenschap van de veelhoeklaag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Veelhoekvulpatroon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>vulpatroon penveelhoek</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> De Azure Maps web SDK biedt verschillende aanpasbare afbeeldingssjablonen die u gebruiken als vulpatronen. Zie het document [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="customize-a-polygon-layer"></a>Een veelhoeklaag aanpassen

De Polygoonlaag heeft slechts een paar stylingopties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Een cirkel toevoegen aan de kaart

Azure Maps maakt gebruik van een uitgebreide versie van het GeoJSON-schema die een definitie biedt voor cirkels, zoals [hier](extend-geojson.md#circle)wordt opgemerkt. Een cirkel wordt weergegeven op de `Point` kaart door het maken van een functie. Dit `Point` heeft `subType` een eigenschap `"Circle"` met `radius` een waarde van en een eigenschap met een getal dat de straal in meters vertegenwoordigt. 

```javascript
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

De Azure Maps Web `Point` SDK `Polygon` zet deze functies om in functies. Vervolgens worden deze functies weergegeven op de kaart met behulp van veelhoek- en lijnlagen, zoals weergegeven in het volgende codevoorbeeld.

<br/>

<iframe height='500' scrolling='no' title='Een cirkel toevoegen aan een kaart' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Een cirkel toevoegen aan een kaart</a> met Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Een geometrie eenvoudig bijwerken

Een `Shape` klasse omsluit een [geometrie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) of [functie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) en maakt het eenvoudig om deze functies bij te werken en te onderhouden. Als u een vormvariabele wilt instantiëren, geeft u een geometrie of een set eigenschappen door aan de vormconstructor.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

In het volgende codevoorbeeld ziet u hoe u een geoJSON-object omcirkelt met een shapeklasse. Als de waarde van de straal in de vorm verandert, wordt de cirkel automatisch weergegeven op de kaart.

<br/>

<iframe height='500' scrolling='no' title='Shape-eigenschappen bijwerken' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de eigenschappen van de<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>vorm penupdate</a> van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Veelhoek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Veelhoeklaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Polygoonopties](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten wilt toevoegen:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

Aanvullende bronnen:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON-specificatie-extensie](extend-geojson.md#circle)