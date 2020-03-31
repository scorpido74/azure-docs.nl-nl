---
title: Module tekengereedschappen | Microsoft Azure Maps
description: In dit artikel leert u hoe u tekenopties instelt met de Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334312"
---
# <a name="use-the-drawing-tools-module"></a>De module voor tekenprogramma's gebruiken

De Azure Maps Web SDK biedt een *module tekengereedschappen.* Deze module maakt het eenvoudig om vormen op de kaart te tekenen en te bewerken met behulp van een invoerapparaat zoals een muis of aanraakscherm. De kernklasse van deze module is de [tekenmanager.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) De tekenmanager biedt alle mogelijkheden die nodig zijn om vormen op de kaart te tekenen en te bewerken. Het kan direct worden gebruikt en het is geïntegreerd met een aangepaste werkbalk-gebruikersinterface. U ook de ingebouwde [werkbalkklasse voor tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) gebruiken. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>De module tekengereedschappen laden op een webpagina

1. Maak een nieuw HTML-bestand en [implementeer de kaart zoals gewoonlijk.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Laad de module voor tekengereedschappen van Azure Maps. U het op twee manieren laden:
    - Gebruik de wereldwijd gehoste Azure Content Delivery Network-versie van de Azure Maps-servicesmodule. Voeg verwijzing toe naar het JavaScript- en CSS-stijlblad in het `<head>` element van het bestand:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - U de module tekengereedschappen voor de Azure Maps Web SDK-broncode lokaal laden met behulp van het [npm-pakket azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) en deze vervolgens hosten met uw app. Dit pakket bevat ook TypeScript-definities. Gebruik deze opdracht:
    
        > **npm installeert azure-maps-drawing-tools**
    
        Voeg vervolgens een verwijzing toe naar het JavaScript- en CSS-stijlblad in het `<head>` element van het bestand:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>De tekenmanager direct gebruiken

Zodra de module tekengereedschappen in uw toepassing is geladen, u teken- en bewerkingsmogelijkheden inschakelen met behulp van de [tekenmanager.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) U opties opgeven voor de tekenmanager terwijl u `drawingManager.setOptions()` deze wilt instantiën of de functie gebruiken.

### <a name="set-the-drawing-mode"></a>De tekenmodus instellen

Met de volgende code wordt een instantie van de tekenmanager gemaakt en wordt de optie **tekenmodus** ingesteld. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

De onderstaande code is een volledig lopend voorbeeld van het instellen van een tekenmodus van de tekenmanager. Klik op de kaart om een veelhoek te tekenen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Een veelhoek tekenen" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen Teken een<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>veelhoek</a> door Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Het interactietype instellen

De tekenmanager ondersteunt drie verschillende manieren van interactie met de kaart om vormen te tekenen.

* `click`- Coördinaten worden toegevoegd wanneer op de muis of aanraking wordt geklikt.
* `freehand `- Coördinaten worden toegevoegd wanneer de muis of aanraking op de kaart wordt gesleept. 
* `hybrid`- Coördinaten worden toegevoegd wanneer de muis of aanraking wordt geklikt of gesleept.

Met de volgende code wordt de veelhoektekenmodus ingeschakeld en wordt `freehand`het type tekeninteractie ingesteld waaraan de tekenmanager zich moet houden. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Dit codevoorbeeld implementeert de functionaliteit van het tekenen van een veelhoek op de kaart. Houd gewoon de linker muisknop ingedrukt en sleep het rond, vrij.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekening met vrije hand" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>penvrije tekening van</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Tekenopties aanpassen

In de vorige voorbeelden werd aangetoond hoe u tekenopties aanpassen terwijl u de tekenmanager instantiëring. U ook de opties Voor `drawingManager.setOptions()` tekenbeheer instellen met behulp van de functie. Hieronder vindt u een tool om aanpassing van alle opties voor de tekenmanager uit te testen met behulp van de functie setOptions.

<br/>

<iframe height="685" title="Tekenbeheer aanpassen" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>vormgegevens pen opvragen</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van extra functies van de module tekengereedschappen:

> [!div class="nextstepaction"]
> [Een tekenwerkbalk toevoegen](map-add-drawing-toolbar.md)

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
> [Tekenmanager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Werkbalk Tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
