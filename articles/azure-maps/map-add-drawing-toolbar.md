---
title: Een werkbalk van een tekening toevoegen aan een kaart | Microsoft Azure Maps
description: Een werkbalk voor een tekening toevoegen aan een kaart met Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334488"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Een werkbalk voor tekengereedschappen toevoegen aan een kaart

In dit artikel ziet u hoe u de module Hulpmiddelen voor tekenen gebruikt en de werkbalk tekenen op de kaart weergeeft. Met het besturingselement [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) wordt de werkbalk tekenen op de kaart toegevoegd. U leert hoe u kaarten maakt met slechts één en alle tekengereedschappen en hoe u de weergave van de tekenvormen in de tekenvorm aanpassen.

## <a name="add-drawing-toolbar"></a>Tekenwerkbalk toevoegen

Met de volgende code wordt een instantie van de tekenmanager gemaakt en wordt de werkbalk op de kaart weergegeven.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Hieronder vindt u het volledige voorbeeld van de functie van de bovenstaande functionaliteit:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenwerkbalk toevoegen" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>werkbalk Tekening pen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>toevoegen door Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Weergegeven werkbalkopties beperken

Met de volgende code wordt een instantie van de tekenmanager gemaakt en wordt de werkbalk weergegeven met slechts een veelhoektekengereedschap op de kaart. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Hieronder vindt u het volledige voorbeeld van de functie van de bovenstaande functionaliteit:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Een veelhoektekengereedschap toevoegen" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>gereedschap Pen Een veelhoektekenprogramma toevoegen</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Tekenweergavestijl wijzigen

De stijl van de getekende vormen kan worden aangepast door de onderliggende lagen van de tekenmanager op te halen door de `drawingManager.getLayers()` functie te gebruiken en vervolgens opties in te stellen op de afzonderlijke lagen. De sleepgrepen die worden weergegeven voor coördinaten bij het bewerken van een vorm zijn HTML-markeringen. De stijl van de sleepgrepen kan worden aangepast `dragHandleStyle` `secondaryDragHandleStyle` door HTML-markeringsopties door te geven aan de opties en opties van de tekenmanager.  

De volgende code krijgt de renderinglagen van de tekenmanager en wijzigt hun opties om de weergavestijl voor tekenen te wijzigen. In dit geval worden punten weergegeven met een blauw markeringspictogram. Lijnen zijn rood en vier pixels breed. Polygonen hebben een groene vulkleur en een oranje omtrek. Vervolgens worden de stijlen van de sleepgrepen gewijzigd als vierkante pictogrammen. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Hieronder vindt u het volledige voorbeeld van de functie van de bovenstaande functionaliteit:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenweergavestijl wijzigen" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>tekenweergavestijl penwijzigen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van extra functies van de module tekengereedschappen:

> [!div class="nextstepaction"]
> [Vormgegevens ophalen](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reageren op tekengebeurtenissen](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interactietypen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Werkbalk Tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Tekenmanager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
