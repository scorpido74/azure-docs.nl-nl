---
title: Een werk balk voor tekenen toevoegen aan Azure Maps | Microsoft Docs
description: Een werk balk voor tekenen toevoegen aan een kaart met Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ed16d8797e541ee474b4f52f3d4379721922a734
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432870"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Een werk balk voor teken hulpprogramma's toevoegen aan een kaart

In dit artikel wordt beschreven hoe u de module teken Hulpprogramma's gebruikt en hoe u de werk balk tekenen op de kaart weergeeft. Met het besturings element [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) wordt de werk balk tekenen op de kaart toegevoegd. U leert hoe u kaarten met slechts één en alle teken hulpmiddelen maakt en hoe u de rendering van de teken vormen in de tekening Manager aanpast.

## <a name="add-drawing-toolbar"></a>Tekenwerkbalk toevoegen

Met de volgende code wordt een exemplaar van de tekening Manager gemaakt en wordt de werk balk op de kaart weer gegeven.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Hieronder ziet u het volledige uitvoerings voorbeeld code van de bovenstaande functies:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenwerkbalk toevoegen" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ga naar de pen <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>werk balk tekening toevoegen</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Opties voor weer gave van werk balken beperken

Met de volgende code wordt een exemplaar van het teken beheer gemaakt en wordt de werk balk met een veelhoek tekening op de kaart weer gegeven. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Hieronder ziet u het volledige uitvoerings voorbeeld code van de bovenstaande functies:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Een hulp programma voor veelhoek tekenen toevoegen" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Ga naar de pen <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>een hulp programma voor veelhoek tekenen toevoegen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Stijl van tekening weergave wijzigen

Met de volgende code worden de weergave lagen opgehaald uit de tekening Manager en worden de opties voor het wijzigen van de stijl van de tekening gewijzigd. In dit geval worden punten weer gegeven met een pictogram met een blauwe markering, worden lijnen rood en vier pixels breed, worden veelhoeken een groene opvul kleur en een oranje overzicht.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Hieronder ziet u het volledige uitvoerings voorbeeld code van de bovenstaande functies:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Stijl van tekening weergave wijzigen" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de stijl voor het <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>weer geven van wijzigingen</a> in de Pen op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Shapegegevens ophalen](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reageren op teken gebeurtenissen](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interactie typen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Werk balk tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)