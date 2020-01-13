---
title: Een laag voor een tegel aan een kaart toevoegen | Microsoft Azure kaarten
description: In dit artikel wordt beschreven hoe u een tegel laag kunt bedekken op een kaart met behulp van de Microsoft Azure Maps Web SDK. Met de tegel lagen kunt u afbeeldingen op een kaart weer geven.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911095"
---
# <a name="add-a-tile-layer-to-a-map"></a>Een titellaag toevoegen aan een kaart

In dit artikel wordt beschreven hoe u een tegel laag kunt bedekken op de kaart. Met tegel lagen kunt u afbeeldingen boven op Azure Maps basis kaart tegels plaatsen. Meer informatie over Azure Maps tegel systeem vindt u in de documentatie over het [Zoom niveau en het tegel raster](zoom-levels-and-tile-grid.md) .

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven en opgeslagen, zoals elke andere installatie kopie op een server met behulp van een naamgevings Conventie die de tegel laag begrijpt, of een dynamische service die de afbeeldingen op de vlucht genereert. Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door Azure Maps klasse [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) : 

* X, Y, zoom notatie: gebaseerd op het zoom niveau, x is de kolom en Y de rijpositie van de tegel in het tegel raster.
* Quadkey notatie: combi natie x, y en zoom informatie in een enkele teken reeks waarde die een unieke id voor een tegel is.
* Omsluitende Box-coördinaten kunnen worden gebruikt om een afbeelding op te geven in de indeling `{west},{south},{east},{north}` die vaak wordt gebruikt door [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Een [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) is een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag. Door vector gegevens als een tegel laag te renderen, hoeft het kaart besturings element alleen de tegels te laden die veel kleiner kunnen zijn dan de vector gegevens die ze vertegenwoordigen. Deze techniek wordt gebruikt door veel die miljoenen rijen met gegevens op de kaart moeten weer geven.

De tegel-URL die wordt door gegeven aan een tegel laag moet een HTTP/HTTPS-URL zijn naar een TileJSON-resource of een tegel-URL-sjabloon die gebruikmaakt van de volgende para meters: 

* `{x}`-X-positie van de tegel. Ook moet `{y}` en `{z}`.
* `{y}`-Y-positie van de tegel. Ook moet `{x}` en `{z}`.
* `{z}`-zoom niveau van de tegel. Ook moet `{x}` en `{y}`.
* `{quadkey}`-tegel quadkey-id op basis van de naam Conventie voor Bing Maps-tegel systemen.
* `{bbox-epsg-3857}`-een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het spatiale referentie systeem van EPSG 3857.
* `{subdomain}`: een tijdelijke aanduiding waarbij de opgegeven subdomein waarden worden toegevoegd.

## <a name="add-a-tile-layer"></a>Een titellaag toevoegen

 Dit voor beeld laat zien hoe u een tegel laag maakt die verwijst naar een set tegels die gebruikmaken van het systeem x, y en zoomen. De bron van deze tegel laag is een weers radar-overlay van de [Iowa Environment Mesonet van de Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Bij het weer geven van radar gegevens kunnen gebruikers in het ideale geval duidelijk de labels van steden zien wanneer ze door de kaart navigeren. Dit kan worden gedaan door de laag van de tegel onder de laag `labels` in te voegen.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Tegel laag met X, Y en Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de laag met de hoek van de pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>met X, Y en Z</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Een laag voor een tegel aanpassen

De klasse van de tegel laag heeft veel stijl opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor de laag van de tegel' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>laag opties</a> van de Pen naast Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een afbeelding slaag toevoegen](./map-add-image-layer.md)
