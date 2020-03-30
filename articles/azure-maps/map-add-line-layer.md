---
title: Een lijnlaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een lijnlaag aan een kaart toevoegt met de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933470"
---
# <a name="add-a-line-layer-to-the-map"></a>Een lijnlaag toevoegen aan de kaart

Een lijnlaag kan worden `LineString` `MultiLineString` gebruikt om te renderen en functies als paden of routes op de kaart. Een lijnlaag kan ook worden gebruikt `Polygon` `MultiPolygon` om de omtrek van en functies weer te geven. Een gegevensbron is verbonden met een lijnlaag om deze te voorzien van gegevens die kunnen worden weergegeven. 

> [!TIP]
> Lijnlagen geven standaard de coördinaten van veelhoeken en lijnen weer in een gegevensbron. Om de laag zodanig te beperken dat `filter` alleen LineString-functies worden weergegeven, stel je de eigenschap van de laag in `['==', ['geometry-type'], 'LineString']` of `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` als je ook MultiLineString-functies wilt opnemen.

In de volgende code ziet u hoe u een regel maakt. Voeg de lijn toe aan een gegevensbron en maak deze weer met een lijnlaag met de klasse [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een regel toevoegen aan een kaart' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Een regel toevoegen aan een kaart</a> met Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Lijnlagen kunnen worden gestyled met [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) en [expressies op basis van gegevens gebruiken.](data-driven-style-expressions-web-sdk.md)

## <a name="add-symbols-along-a-line"></a>Symbolen langs een lijn toevoegen

In dit voorbeeld ziet u hoe u pijlpictogrammen toevoegt langs een lijn op de kaart. Wanneer u een symboollaag gebruikt, stelt u de optie 'plaatsing' in op 'regel'. Deze optie geeft de symbolen langs de lijn weer en draait de pictogrammen (0 graden = rechts).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pijl langs lijn weergeven" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pijl Pen <a href='https://codepen.io/azuremaps/pen/drBJwX/'>weergeven</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>langs lijn door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> De Azure Maps web SDK biedt verschillende aanpasbare afbeeldingssjablonen die u met de symboollaag gebruiken. Zie het document [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md) voor meer informatie.

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Een lijnverloop toevoegen aan een lijn

U één lijnkleur op een lijn toepassen. U ook een regel vullen met een kleurverloop om de overgang van het ene lijnsegment naar het volgende lijnsegment weer te geven. Lijnverlopen kunnen bijvoorbeeld worden gebruikt om veranderingen in tijd en afstand weer te geven, of om verschillende temperaturen in een verbonden lijn van objecten weer te geven. Om deze functie op een regel toe te `lineMetrics` passen, moet de gegevensbron de optie op `strokeColor` true hebben ingesteld en vervolgens kan een kleurverloopexpressie worden doorgegeven aan de optie van de regel. De expressie lijnverloop moet `['line-progress']` verwijzen naar de gegevensexpressie die de berekende lijnmetriek aan de expressie blootstelt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lijn met lijnverloop" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>penlijn met lijnverloop door</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Een lijnlaag aanpassen

De lijnlaag heeft verschillende stylingopties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor lijnlaag' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>penlijnlaagopties</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [LineLayer (LineLayer)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOpties](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](map-add-popup.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Afbeeldingssjablonen gebruiken](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)
