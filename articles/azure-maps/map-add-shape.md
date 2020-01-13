---
title: Een polygoon laag toevoegen aan een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een veelhoek en meerdere veelhoeken kunt weer geven op een kaart in de Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 77d952666be12d7dea780b3aa8f094cf5f70f2d3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911136"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Een polygoon laag toevoegen aan de kaart

In dit artikel wordt beschreven hoe u de onderdelen van `Polygon` en `MultiPolygon` functie geometrie op de kaart kunt weer geven met behulp van een polygoon laag. De websdk van Azure Maps biedt ook ondersteuning voor het maken van cirkel geometrie zoals gedefinieerd in het [uitgebreide GEOjson-schema](extend-geojson.md#circle). Deze cirkels worden omgezet in veelhoeken wanneer ze op de kaart worden weer gegeven. Alle functie-geometrieën kunnen ook eenvoudig worden bijgewerkt als deze met de [Atlas wordt verpakt. ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)Klasse van vorm.

## <a name="use-a-polygon-layer"></a>Een polygoon laag gebruiken 

Wanneer een polygoon laag is verbonden met een gegevens bron en op de kaart is geladen, wordt het gebied van een `Polygon` en `MultiPolygon` functies weer gegeven. De volgende code laat zien hoe u een veelhoek maakt, deze toevoegt aan een gegevens bron en deze weergeeft met een polygoon laag met behulp van de [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) -klasse.

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een veelhoek aan een kaart toevoegen ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>een veelhoek toevoegen aan een kaart</a> op basis van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Een veelhoek en een lijn-laag samen gebruiken

Een line-laag kan worden gebruikt om het overzicht van veelhoeken weer te geven. In het volgende code voorbeeld wordt een veelhoek weer gegeven zoals in het vorige voor beeld, maar wordt nu een laag toegevoegd als een tweede laag die is verbonden met de gegevens bron.  

<iframe height='500' scrolling='no' title='Veelhoek en lijn-laag om veelhoek toe te voegen' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>veelhoek en de lijn-laag voor het toevoegen van veelhoeken</a> met Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Een veelhoek met een patroon vullen

Naast het vullen van een veelhoek met een kleur, kan een afbeeldings patroon ook worden gebruikt. Laad een afbeeldings patroon in de Maps-installatie kopie resources Sprite en vervolgens naar deze installatie kopie met de eigenschap `fillPattern` van de polygoon laag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opvul patroon veelhoek" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>opvul patroon</a> voor de pen-veelhoek op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> De Azure Maps Web-SDK biedt verschillende aanpas bare afbeeldings sjablonen die u als opvul patroon kunt gebruiken. Zie het document [Image-sjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

## <a name="customize-a-polygon-layer"></a>Een polygoon laag aanpassen

De polygoon laag heeft slechts enkele opmaak opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Een cirkel aan de kaart toevoegen

Azure Maps maakt gebruik van een uitgebreide versie van het geojson-schema met een definitie voor cirkels zoals [hier](extend-geojson.md#circle)wordt beschreven. Een cirkel kan worden weer gegeven op de kaart door een `Point`-functie te maken met een `subType`-eigenschap met de waarde `"Circle"` en een `radius`-eigenschap met een getal dat de RADIUS in meters vertegenwoordigt. Bijvoorbeeld:

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

Met de Azure Maps Web-SDK worden deze `Point` functies geconverteerd naar `Polygon` functies onder de kaften en kunnen ze worden weer gegeven op de kaart met behulp van veelhoek-en lijn lagen, zoals in het volgende code voorbeeld wordt weer gegeven.

<br/>

<iframe height='500' scrolling='no' title='Een cirkel aan een kaart toevoegen' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>een cirkel toevoegen aan een kaart</a> op basis van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Een geometrie gemakkelijk te updaten maken

Met een `Shape` klasse wordt een [geometrie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) of [functie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) ingepakt en kunnen deze eenvoudig worden bijgewerkt en onderhouden. Een shape kan worden gemaakt door door te geven in een geometrie en een set eigenschappen, of door door te geven in een functie, zoals wordt weer gegeven in de volgende code.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

In het volgende code voorbeeld ziet u hoe u een geojson-object in een cirkel met een vorm klasse bijwerkt en de RADIUS-eigenschap eenvoudig kunt bijwerken met behulp van een schuif regelaar. Als de RADIUS-waarde in de vorm verandert, wordt de weer gave van de cirkel automatisch bijgewerkt op de kaart.

<br/>

<iframe height='500' scrolling='no' title='Eigenschappen van shape bijwerken' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de eigenschappen van de <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>shape pen bijwerken</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Polygoon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw Maps:

> [!div class="nextstepaction"]
> [Een gegevens bron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-upvenster toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldings sjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een line laag toevoegen](map-add-line-layer.md)

Aanvullende bronnen:

> [!div class="nextstepaction"]
> [Uitbrei ding voor geojson-specificatie Azure Maps](extend-geojson.md#circle)