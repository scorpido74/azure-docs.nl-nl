---
title: Een werk balk tekenen toevoegen aan een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een werk balk tekenen aan een kaart kunt toevoegen met behulp van Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911670"
---
# <a name="drawing-tool-events"></a>Gebeurtenissen voor het tekenen van gereedschappen

Wanneer u teken hulpprogramma's op een kaart gebruikt, is het vaak handig om te reageren op bepaalde gebeurtenissen wanneer de gebruiker op de kaart tekent. De volgende tabel geeft een lijst van alle gebeurtenissen die door de `DrawingManager` klasse worden ondersteund.

| Gebeurtenis | Beschrijving |
|-------|-------------|
| `drawingchanged` | Deze gebeurtenis wordt gestart wanneer een coördinaat in een vorm is toegevoegd of gewijzigd. | 
| `drawingchanging` | Deze gebeurtenis wordt gestart wanneer een voor beeld-coördinaat voor een vorm wordt weer gegeven. Er worden bijvoorbeeld meerdere keren geactiveerd wanneer een coördinaat wordt gesleept. | 
| `drawingcomplete` | Deze gebeurtenis wordt gestart wanneer een vorm is getekend of uit de bewerkings modus is gehaald. |
| `drawingmodechanged` | Deze gebeurtenis wordt gestart wanneer de teken modus is gewijzigd. De nieuwe teken modus wordt door gegeven aan de gebeurtenis-handler. |
| `drawingstarted` | Deze gebeurtenis wordt gestart wanneer de gebruiker een vorm tekent of een vorm in de bewerkings modus plaatst.  |

De volgende code laat zien hoe de gebeurtenissen in de module teken Hulpprogramma's werken. Teken vormen op de kaart en kijk wanneer de gebeurtenissen worden gestart.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenen hulpprogram ma's gebeurtenissen" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de gebeurtenissen van de pen <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>Drawing</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>voor meer informatie.
</iframe>

<br/>

## <a name="examples"></a>Voorbeelden

Hier volgen enkele voor beelden van veelvoorkomende scenario's die gebruikmaken van de teken hulpprogramma's gebeurtenissen.

### <a name="select-points-in-polygon-area"></a>Punten in het gebied veelhoek selecteren

De volgende code laat zien hoe u de tekening van shapes bewaakt die veelhoek gebieden (veelhoeken, rechthoeken en cirkels) vertegenwoordigen en bepalen welke gegevens punten op de kaart zich in het teken gebied bevinden. De gebeurtenis `drawingcomplete` wordt gebruikt om de Select Logic te activeren. In de logica selecteren worden alle gegevens punten op de kaart door lopen en getest op de snij punt met het polygoon gebied van de getekende vorm. In dit voor beeld wordt het gebruik van de open-source [turf. js](https://turfjs.org/) -bibliotheek gebruikt voor het uitvoeren van een ruimtelijke intersectie berekening.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gegevens selecteren in een getekend veelhoek gebied" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>gegevens selecteren in getekend veelhoek</a> van het gebied Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Tekenen en zoeken in veelhoek gebied

De volgende code laat zien hoe u een zoek opdracht naar punten van belangen binnen een vorm gebied kunt uitvoeren nadat de gebruiker de vorm heeft getekend. De gebeurtenis `drawingcomplete` wordt gebruikt om de zoek logica te activeren. Als de gebruiker een rechthoek of veelhoek tekent, wordt er een zoek opdracht in geometrie uitgevoerd. Als een cirkel wordt getekend, wordt de straal en de positie van het middel punt gebruikt om een zoek opdracht op de interesse uit te voeren. De gebeurtenis `drawingmodechanged` wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar een teken modus en het teken papier wist.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenen en zoeken in veelhoek gebied" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>tekenen en zoek in het gebied veelhoek</a> op Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Een meet programma maken

De volgende code toont hoe de teken gebeurtenissen kunnen worden gebruikt voor het maken van een meet programma. De `drawingchanging` wordt gebruikt om de shape te bewaken terwijl deze wordt getekend. Wanneer de gebruiker de muis verplaatst, worden de afmetingen van de vorm berekend. De gebeurtenis `drawingcomplete` wordt gebruikt om een uiteindelijke berekening uit te voeren op de vorm nadat deze is getekend. De gebeurtenis `drawingmodechanged` wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar een teken modus en het teken papier en de oude meet gegevens wist.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Meet gereedschap" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>meet hulpprogramma's</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van aanvullende functies van de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Shapegegevens ophalen](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interactie typen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de module Services:

> [!div class="nextstepaction"]
> [Services-module](how-to-use-services-module.md)

Meer code voorbeelden bekijken:

> [!div class="nextstepaction"]
> [Voorbeeld pagina voor code](https://aka.ms/AzureMapsSamples)
