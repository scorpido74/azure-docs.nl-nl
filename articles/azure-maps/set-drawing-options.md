---
title: Teken opties instellen in Azure Maps | Microsoft Docs
description: Gegevens voor teken opties instellen met Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309816"
---
# <a name="set-drawing-options"></a>Teken opties instellen

In dit artikel wordt beschreven hoe u met de verschillende opties van het [tekening beheer](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) de gebruikers ervaring wijzigt. U kunt opties voor de tekening beheerder opgeven tijdens het instantiëren van de groep of de functie **drawingManager. setoptions ()** gebruiken om opties in te stellen.


## <a name="set-drawing-mode"></a>Teken modus instellen

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


## <a name="set-interaction-type"></a>Type interactie instellen

Met de volgende code wordt het type teken interactie ingesteld waarmee de tekening beheerder moet worden gerespecteerd. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Hieronder ziet u het code voorbeeld dat de functionaliteit implementeert waarmee u op de kaart vrij kunt tekenen, terwijl u de linkermuisknop ingedrukt houdt en sleept. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrije hand tekening" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie Azure Maps () op <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op de pen voor een <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>vrije hand tekening</a> .
</iframe>


## <a name="customizing-drawing-options"></a>Teken opties aanpassen

In de vorige voor beelden wordt uitgelegd hoe u teken opties kunt aanpassen tijdens het instantiëren van het tekening beheer. U kunt de opties voor teken beheer ook instellen met behulp van de functie **drawingManager. setoptions ()** . Hieronder vindt u een hulp programma voor het testen van de aanpassing van alle opties voor de tekening Manager met behulp van de functie setOption.

<br/>

<iframe height="685" title="Drawing Manager aanpassen" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get Shape Data</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Werk balk tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
