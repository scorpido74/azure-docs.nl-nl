---
title: Shapegegevens ophalen uit de teken beheer in Azure Maps | Microsoft Docs
description: Shapegegevens ophalen met Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bc8efe46453f76d353842b32bd52b41838979b49
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432828"
---
# <a name="get-shape-data"></a>Vormgegevens ophalen

In dit artikel wordt beschreven hoe u shape-gegevens kunt ophalen die op de kaart zijn getekend met de functie **drawingManager. getSource ()** van het [tekening beheer](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) . Er kunnen verschillende scenario's zijn waarin u de geojson-gegevens van een getekende vorm wilt extra heren en deze elders kunt gebruiken.  


## <a name="get-data-from-drawn-shape"></a>Gegevens ophalen van een teken vorm

De volgende functie haalt de bron gegevens van de getekende vorm op en voert deze uit op het scherm. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Hieronder ziet u het volledige programma voor het uitvoeren van code, waarin u een vorm kunt tekenen om de functionaliteit te testen:

<br/>

<iframe height="686" title="Vormgegevens ophalen" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get Shape Data</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

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
