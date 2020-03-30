---
title: Besturingselementen toevoegen aan een kaart | Microsoft Azure Maps
description: Zoombesturingselement, toonhoogtebeheer, draaibesturingselement en een stijlkiezer toevoegen aan een kaart in Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334566"
---
# <a name="add-controls-to-a-map"></a>Besturingselementen toevoegen aan een kaart

In dit artikel ziet u hoe u besturingselementen aan een kaart toevoegt. Je leert ook hoe je een kaart maakt met alle besturingselementen en een [stijlkiezer.](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

## <a name="add-zoom-control"></a>Zoombesturingselement toevoegen

Een zoombesturingselement voegt knoppen toe om de kaart in en uit te zoomen. In het volgende codevoorbeeld wordt een instantie van de klasse [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) gemaakt en wordt de rechterbenedenhoek van de kaart toegevoegd.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een zoombesturingselement toevoegen' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen Het toevoegen van<a href='https://codepen.io/azuremaps'>@azuremaps</a>een <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>zoombesturingselement</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Toonhoogtecontrole toevoegen

Een toonhoogtebesturingselement voegt knoppen toe voor het kantelen van de toonhoogte om de toonhoogte ten opzichte van de horizon in kaart te brengen. In het volgende codevoorbeeld wordt een instantie van de klasse [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) gemaakt. Het voegt de PitchControl toe aan de rechterbovenhoek van de kaart.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een toonhoogtecontrole toevoegen' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen Het toevoegen van<a href='https://codepen.io/azuremaps'>@azuremaps</a>een <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>toonhoogtebesturingselement</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Kompasbesturing toevoegen

Een kompasbesturingselement voegt een knop toe voor het roteren van de kaart. Met het volgende codevoorbeeld wordt een instantie van de klasse [Kompascontrole](/javascript/api/azure-maps-control/atlas.control.compasscontrol) gemaakt en wordt de linkerbenedenhoek van de kaart toegevoegd.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een roterend besturingselement toevoegen' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen Het toevoegen van<a href='https://codepen.io/azuremaps'>@azuremaps</a>een <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>roterende besturingselement</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Een kaart met alle besturingselementen

Meerdere besturingselementen kunnen in een array worden geplaatst en in één keer aan de kaart worden toegevoegd en in hetzelfde gebied van de kaart worden geplaatst om de ontwikkeling te vereenvoudigen. Het volgende voegt de standaard navigatiebesturingselementen toe aan de kaart met behulp van deze aanpak.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

In het volgende codevoorbeeld worden de besturingselementen voor zoom-, kompas-, toonhoogte- en stijlkiezer toegevoegd aan de rechterbovenhoek van de kaart. Merk op hoe ze automatisch stapelen. De volgorde van de besturingselementobjecten in het script bepaalt de volgorde waarin ze op de kaart worden weergegeven. Als u de volgorde van de besturingselementen op de kaart wilt wijzigen, u de volgorde in de array wijzigen.

<br/>

<iframe height='500' scrolling='no' title='Een kaart met alle besturingselementen' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A-kaart met alle besturingselementen</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Het besturingselement voor stijlkiezer wordt gedefinieerd door de klasse [StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Zie [een kaartstijl kiezen voor](choose-map-style.md)meer informatie over het gebruik van het besturingselement stijlkiezer.

## <a name="customize-controls"></a>Besturingselementen aanpassen

Hier is een hulpmiddel om de verschillende opties voor het aanpassen van de besturingselementen uit te testen.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigatiebesturingselementen" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>opties voor pennavigatiebesturingselementen</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Als u aangepaste navigatiebesturingselementen wilt maken, `atlas.Control` maakt u een klasse die zich uitstrekt van de klasse of maakt u een HTML-element en plaatst u deze boven de mapdiv. Laat deze gebruikersinterface de `setCamera` kaartfunctie aanroepen om de kaart te verplaatsen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kompascontrole](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl (StyleControl)](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [Zoombesturingselement](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Zie de volgende artikelen voor volledige code:

> [!div class="nextstepaction"]
> [Een speld toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een pop-up toevoegen](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"]
> [Een bubbellaag toevoegen](map-add-bubble-layer.md)

