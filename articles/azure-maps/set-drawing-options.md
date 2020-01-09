---
title: Gebruik de module teken hulpprogramma's in Azure Maps | Microsoft Docs
description: Gegevens voor teken opties instellen met Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0ac9bc775798a14e6431718bc602d8ff41288c10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408155"
---
# <a name="use-the-drawing-tools-module"></a>De module teken hulpprogramma's gebruiken

De Azure Maps Web-SDK bevat een *module voor teken hulpprogramma's*. Met deze module kunt u gemakkelijk shapes op de kaart tekenen en bewerken met behulp van een invoer apparaat, zoals een muis, ons aanraak scherm. De kern klasse van deze module is de [teken Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) en biedt alle mogelijkheden die nodig zijn om shapes op de kaart te tekenen en te bewerken. De drawing Manager kan rechtstreeks worden gebruikt en geïntegreerd met een aangepaste werk balk GEBRUIKERSINTERFACE, of u kunt gebruikmaken van de ingebouwde [werk balk voor tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>De module teken hulpprogramma's laden op een webpagina

1. Maak een nieuw HTML-bestand en [Implementeer de kaart zoals gebruikelijk](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Laad de module Azure Maps drawing tools. U kunt deze op twee manieren laden:
    - Gebruik de wereld wijd gehoste Azure Content Delivery Network-versie van de module Azure Maps Services. Voeg een verwijzing naar het opmaak model java script en CSS toe in het `<head>` element van het bestand:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - U kunt ook de module teken hulpprogramma's laden voor de Azure Maps Web SDK-bron code lokaal met behulp van het NPM-pakket [Azure-Maps-drawing tools](https://www.npmjs.com/package/azure-maps-drawing-tools) en het vervolgens hosten met uw app. Dit pakket bevat ook type script definities. Gebruik deze opdracht:
    
        > **NPM Azure-Maps-draw-tools installeren**
    
        Voeg vervolgens een verwijzing naar het opmaak model java script en CSS toe in het `<head>` element van het bestand:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>De tekening Manager rechtstreeks gebruiken

Nu de module teken hulpprogramma's in uw toepassing is geladen, kunt u de [tekening Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) gebruiken om de mogelijkheden voor tekenen en bewerken in de kaart in te scha kelen. U kunt opties voor de tekening beheerder opgeven tijdens het instantiëren of de functie `drawingManager.setOptions()` gebruiken.

### <a name="set-the-drawing-mode"></a>De teken modus instellen

Met de volgende code wordt een exemplaar van de teken beheerder gemaakt en wordt de optie voor de teken **modus** ingesteld. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

De onderstaande code is een volledig actief voor beeld van het instellen van een teken modus van de tekening Manager. Klik op de kaart om te beginnen met het tekenen van een veelhoek.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Een veelhoek tekenen" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>een veelhoek</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Het interactie type instellen

De tekening beheerder ondersteunt drie verschillende manieren van interactie met de kaart om vormen te tekenen.

* `click`-coördinaten worden toegevoegd wanneer er met de muis of het aanraken wordt geklikt.
* `freehand `-coördinaten worden toegevoegd wanneer de muis of het aanraak scherm op de kaart wordt gesleept. 
* `hybrid`-coördinaten worden toegevoegd wanneer de muis of het aanraken wordt geklikt of gesleept.

Met de volgende code wordt de polygoon teken modus ingeschakeld en wordt het type teken interactie ingesteld waaraan de tekening manager moet voldoen `freehand`. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Hieronder ziet u het code voorbeeld waarmee u een veelhoek op de kaart vrij kunt tekenen, terwijl u de linkermuisknop ingedrukt houdt en sleept. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrije hand tekening" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) voor een <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>vrije hand tekening</a> van de pen op <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Teken opties aanpassen

In de vorige voor beelden wordt uitgelegd hoe u teken opties kunt aanpassen tijdens het instantiëren van het tekening beheer. U kunt de opties voor teken beheer ook instellen met behulp van de functie `drawingManager.setOptions()`. Hieronder vindt u een hulp programma voor het testen van de aanpassing van alle opties voor de tekening Manager met behulp van de functie setOption.

<br/>

<iframe height="685" title="Drawing Manager aanpassen" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get Shape Data</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Een werk balk tekenen toevoegen](map-add-drawing-toolbar.md)

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
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Werk balk tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
