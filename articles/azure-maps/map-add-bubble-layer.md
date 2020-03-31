---
title: Een bellaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het toevoegen van een bellenlaag aan een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208553"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Een bellenlaag toevoegen aan een kaart

In dit artikel ziet u hoe u puntgegevens uit een gegevensbron renderen als een bellenlaag op een kaart. Bellenlagen geven punten weer als cirkels op de kaart met een vaste pixelstraal. 

> [!TIP]
> Bellenlagen geven standaard de coördinaten van alle geometrieën weer in een gegevensbron. Als u de laag zodanig wilt beperken dat `filter` alleen puntgeometriefuncties worden weergegeven, stelt u de eigenschap van de laag in `['==', ['geometry-type'], 'Point']` of `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` als u ook MultiPoint-functies wilt opnemen.

## <a name="add-a-bubble-layer"></a>Een bubbellaag toevoegen

De volgende code laadt een array van punten in een gegevensbron. Vervolgens verbindt het de gegevenspunten met een [bellenlaag.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) De bellenlaag geeft de straal van elke bel weer met vijf pixels en een vulkleur wit. En, een lijn kleur van blauw, en een lijn breedte van zes pixels. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Labels weergeven met een bellenlaag

Deze code laat zien hoe u een bellenlaag gebruikt om een punt op de kaart weer te geven. En, hoe u een symboollaag gebruikt om een label weer te geven. Als u het pictogram van de `image` symboollaag wilt `'none'`verbergen, stelt u de eigenschap van de pictogramopties in op .

<br/>

<iframe height='500' scrolling='no' title='Meerlaagse gegevensbron' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Een bellenlaag aanpassen

De Bubble laag heeft slechts een paar styling opties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor bellenlaag' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>penbellenlaagopties</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [BubbleLayer BubbleLayer BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
