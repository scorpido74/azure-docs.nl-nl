---
title: Een afbeeldingslaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het overlay van een afbeelding op een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209712"
---
# <a name="add-an-image-layer-to-a-map"></a>Een afbeeldingslaag toevoegen aan een kaart

In dit artikel ziet u hoe u een afbeelding bedekken met een vaste set coördinaten. Hier zijn een paar voorbeelden van verschillende soorten afbeeldingen die kunnen worden bedekt op kaarten:

* Beelden die van hommels worden gevangen
* Plattegronden van gebouwen
* Historische of andere gespecialiseerde kaartafbeeldingen
* Blauwdrukken van vacaturesites
* Weer radarbeelden

> [!TIP]
> Een [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) is een eenvoudige manier om een afbeelding op een kaart te bedekken. Houd er rekening mee dat browsers problemen kunnen hebben met het laden van een grote afbeelding. In dit geval u overwegen uw afbeelding op te splitsen in tegels en deze als [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)in de kaart te laden.

De afbeeldingslaag ondersteunt de volgende afbeeldingsindelingen:

- Jpeg
- PNG
- BMP
- GIF (geen animaties)

## <a name="add-an-image-layer"></a>Een afbeeldingslaag toevoegen

De volgende code bedekt een afbeelding van een [kaart van Newark, New Jersey, uit 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) op de kaart. Een [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) wordt gemaakt door een URL door te geven aan `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`een afbeelding en coördineert voor de vier hoeken in de indeling.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Hier is het volledige voorbeeld van de code van de voorgaande code.

<br/>

<iframe height='500' scrolling='no' title='Eenvoudige afbeeldingslaag' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/eQodRo/'>peneenvoudige afbeeldingslaag van</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Een KML-bestand importeren als grondoverlay

In dit voorbeeld wordt uitgelegd hoe u KML-grondoverlay-informatie toevoegt als een afbeeldingslaag op de kaart. KML grond overlays bieden noord-, zuid-, oost- en west coördinaten, en een tegen de klok in rotatie. De afbeeldingslaag verwacht echter coördinaten voor elke hoek van de afbeelding. De KML grondoverlay in dit monster is voor de Kathedraal van Chartres, en het is afkomstig van [Wikimedia.](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)

De code gebruikt `getCoordinatesFromEdges` de statische functie van de klasse [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Het berekent de vier hoeken van het beeld met behulp van de noord-, zuid-, oost-, west- en rotatie-informatie van de KML-grondoverlay.

<br/>

<iframe height='500' scrolling='no' title='KML-grondoverlay als afbeeldingslaag' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML-grondoverlay als afbeeldingslaag</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Een afbeeldingslaag aanpassen

De afbeeldingslaag heeft veel stylingopties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor afbeeldingslaag' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>penafbeeldingslaagopties</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [ImageLayer (ImageLayer)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](./map-add-tile-layer.md)