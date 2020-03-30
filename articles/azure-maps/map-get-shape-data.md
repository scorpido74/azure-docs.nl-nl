---
title: Gegevens ophalen van vormen op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u vormgegevens op een kaart laten tekenen met de Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334413"
---
# <a name="get-shape-data"></a>Vormgegevens ophalen

In dit artikel ziet u hoe u gegevens krijgen van vormen die op de kaart zijn getekend. We gebruiken de functie **drawingManager.getSource()** binnen [tekenmanager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Er zijn verschillende scenario's waarin u geojsongegevens van een getekende vorm wilt extraheren en elders wilt gebruiken.  


## <a name="get-data-from-drawn-shape"></a>Gegevens ophalen uit getekende vorm

Met de volgende functie worden de brongegevens van de getekende vorm weergegeven en wordt deze naar het scherm uitgevoerd. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Hieronder vindt u het voorbeeld van de volledige hardloopcode, waarin u een vorm tekenen om de functionaliteit te testen:

<br/>

<iframe height="686" title="Vormgegevens ophalen" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>vormgegevens pen opvragen</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van extra functies van de module tekengereedschappen:

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
