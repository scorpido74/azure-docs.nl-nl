---
title: Een tegellaag toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een tegellaag op een kaart overlayt met behulp van de Microsoft Azure Maps Web SDK. Met tegellagen u afbeeldingen weergeven op een kaart.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988597"
---
# <a name="add-a-tile-layer-to-a-map"></a>Een titellaag toevoegen aan een kaart

In dit artikel ziet u hoe u een tegellaag op de kaart overlayt. Met tegellagen u afbeeldingen boven op basiskaarttegels van Azure Maps plaatsen. Zie [Zoomniveaus en tegelraster voor](zoom-levels-and-tile-grid.md)meer informatie over het tegelsysteem voor Azure Maps.

Een tegellaag laadt tegels van een server. Deze afbeeldingen kunnen vooraf worden weergegeven of dynamisch worden weergegeven. Vooraf gerenderde afbeeldingen worden opgeslagen als elke andere afbeelding op een server met behulp van een naamgevingsconventie die de tegellaag begrijpt. Dynamisch weergegeven afbeeldingen gebruiken een service om de afbeeldingen dicht bij realtime te laden. Er zijn drie verschillende tegelservicenaamgevingsconventies die worden ondersteund door de klasse Azure Maps [TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* X, Y, Zoomnotatie - X is de kolom, Y is de rijpositie van de tegel in het tegelraster en de zoomnotatie een waarde op basis van het zoomniveau.
* Quadkey-notatie - Combineert x-, y- en zoominformatie in één tekenreekswaarde. Deze tekenreekswaarde wordt een unieke id voor één tegel.
* Selectiekader : Geef een afbeelding op in de `{west},{south},{east},{north}`coördinatennotatie van het selectiekader: . Deze indeling wordt vaak gebruikt door [Web Mapping Services (WMS).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> Een [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) is een geweldige manier om grote datasets op de kaart te visualiseren. Niet alleen kan een tegellaag worden gegenereerd uit een afbeelding, vectorgegevens kunnen ook worden weergegeven als een tegellaag. Door vectorgegevens als een tegellaag weer te geven, hoeft kaartbeheer alleen de tegels te laden die kleiner zijn in bestandsgrootte dan de vectorgegevens die ze vertegenwoordigen. Deze techniek wordt vaak gebruikt om miljoenen rijen met gegevens op de kaart weer te geven.

De tegel-URL die in een tegellaag wordt doorgegeven, moet een http- of een https-URL zijn naar een TileJSON-bron of een URL-sjabloon voor tegels die de volgende parameters gebruikt: 

* `{x}`- X-positie van de tegel. Ook `{y}` behoeften `{z}`en .
* `{y}`- Y positie van de tegel. Ook `{x}` behoeften `{z}`en .
* `{z}`- Zoomniveau van de tegel. Ook `{x}` behoeften `{y}`en .
* `{quadkey}`- Tile quadkey-id op basis van de naamconventie van het Bing Maps-tegelsysteem.
* `{bbox-epsg-3857}`- Een selectielijst met `{west},{south},{east},{north}` het formaat in het EPSG 3857 Spatial Reference System.
* `{subdomain}`- Een tijdelijke aanduiding voor de subdomeinwaarden, indien opgegeven, wordt deze `subdomain` toegevoegd.

## <a name="add-a-tile-layer"></a>Een titellaag toevoegen

 In dit voorbeeld ziet u hoe u een tegellaag maakt die naar een set tegels verwijst. Dit voorbeeld maakt gebruik van het betegelsysteem x, y, zoom. De bron van deze tegellaag is een weerradar overlay van de [Iowa Environmental Mesonet van Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Bij het bekijken van radargegevens zouden gebruikers idealiter duidelijk de labels van steden zien terwijl ze door de kaart navigeren. Dit gedrag kan worden geïmplementeerd door de `labels` tegellaag onder de laag in te voegen.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Tegellaag met X, Y en Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>pentegellaag met X, Y en Z</a> by Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Een tegellaag aanpassen

De tegellaagklasse heeft veel stylingopties. Hier is een hulpmiddel om ze uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor tegellaag' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>pentegellaagopties</a> van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Tegellaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor meer codevoorbeelden die u aan uw kaarten toevoegen:

> [!div class="nextstepaction"]
> [Een afbeeldingslaag toevoegen](./map-add-image-layer.md)
