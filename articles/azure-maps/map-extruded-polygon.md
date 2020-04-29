---
title: Een laag met een polygoon effect toevoegen aan een kaart | Microsoft Azure kaarten
description: Een Layer extrusie-laag toevoegen aan de Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334546"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Een laag met een polygoon effect toevoegen aan de kaart

In dit artikel leest u hoe u de laag voor het 3D-effect kunt `Polygon` gebruiken `MultiPolygon` om gebieden en functie-geometrie weer te geven als geëxtrudeerde vormen. De Azure Maps Web-SDK ondersteunt rendering van cirkel geometrie zoals gedefinieerd in het [uitgebreide GEOjson-schema](extend-geojson.md#circle). Deze cirkels kunnen worden omgezet in veelhoeken wanneer ze op de kaart worden weer gegeven. Alle functie-geometrieën kunnen eenvoudig worden bijgewerkt wanneer deze met de [Atlas wordt verpakt. ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)Klasse van vorm.

## <a name="use-a-polygon-extrusion-layer"></a>Een laag met een polygoon effect gebruiken

Verbind de laag met de [polygoon extrusie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) met een gegevens bron. Vervolgens wordt deze op de kaart geladen. De laag met het polygoon reliëf geeft de gebieden van `Polygon` een `MultiPolygon` en-functies weer als geëxtrudeerde vormen. De `height` eigenschappen `base` en van de Layer extrusie-laag definiëren de basis afstand van de grond en hoogte van de geëxtrudeerde vorm in **meters**. De volgende code laat zien hoe u een veelhoek maakt, voegt deze toe aan een gegevens bron en geeft deze weer met behulp van de klasse van de polygoon laag.

> [!Note]
> De `base` waarde die is gedefinieerd in de Layer extrusie laag moet kleiner zijn dan of gelijk zijn aan `height`die van de.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Geëxtrudeerde veelhoek" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>geëxtrudeerde veelhoek</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Gegevensgestuurde veelhoeken toevoegen

Een choropleth-kaart kan worden gerenderd met behulp van de Layer extrusie-laag. Stel de `height` eigenschappen `fillColor` en van de laag extrusie in op de meting van de statistische variabele in `Polygon` de `MultiPolygon` functies en geometrie. Het volgende code voorbeeld toont een geëxtrudeerde choropleth toewijzing van de U. S op basis van de meting van de populatie densiteit per status.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Geëxtrudeerde choropleth-kaart" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/eYYYNox'>geëxtrudeerde choropleth-toewijzing</a> van Pen per<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Een cirkel aan de kaart toevoegen

Azure Maps maakt gebruik van een uitgebreide versie van het geojson-schema met een definitie voor cirkels zoals [hier](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)wordt beschreven. Een `point` 3D-cirkel kan worden weer gegeven op de kaart door een functie te maken met `subType` een eigenschap `Circle` van en een `Radius` genummerde eigenschap die de RADIUS in **meters**vertegenwoordigt. Bijvoorbeeld:

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

Met de Azure Maps Web-SDK `Point` worden deze `Polygon` functies geconverteerd naar functies onder de schermen. Deze `Point` functies kunnen worden weer gegeven op de kaart met behulp van de Layer extrusie-laag, zoals wordt weer gegeven in het volgende code voorbeeld.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone-lucht ruimte veelhoek" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>drone lucht spatie veelhoek</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Een laag met een polygoon effect aanpassen

De laag met polygoon extrusie heeft verschillende opties voor opmaak. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Polygoon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygoon-3D-laag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Aanvullende bronnen:

> [!div class="nextstepaction"]
> [Uitbrei ding voor geojson-specificatie Azure Maps](extend-geojson.md#circle)
