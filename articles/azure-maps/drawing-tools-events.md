---
title: Een werkbalk van een tekening toevoegen aan een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een werkbalk voor een tekening toevoegt aan een kaart met Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804670"
---
# <a name="drawing-tool-events"></a>Gebeurtenissen met tekengereedschap

Bij het gebruik van tekengereedschappen op een kaart is het handig om op bepaalde gebeurtenissen te reageren terwijl de gebruiker op de kaart tekent. In deze tabel worden alle gebeurtenissen `DrawingManager` weergegeven die door de klas worden ondersteund.

| Gebeurtenis | Beschrijving |
|-------|-------------|
| `drawingchanged` | Wordt geactiveerd wanneer een coördinaat in een vorm is toegevoegd of gewijzigd. | 
| `drawingchanging` | Wordt geactiveerd wanneer een voorbeeldcoördinaat voor een vorm wordt weergegeven. Deze gebeurtenis wordt bijvoorbeeld meerdere keren afvuurd wanneer een coördinaat wordt gesleept. | 
| `drawingcomplete` | Wordt afgevuurd wanneer een vorm is voltooid en uit de bewerkingsmodus is gehaald. |
| `drawingmodechanged` | Ontslagen wanneer de tekenmodus is gewijzigd. De nieuwe tekenmodus wordt doorgegeven aan de gebeurtenishandler. |
| `drawingstarted` | Wordt afgevuurd wanneer de gebruiker een vorm begint te tekenen of een vorm in de bewerkingsmodus plaatst.  |

In de volgende code ziet u hoe de gebeurtenissen in de module Hulpmiddelen voor tekenen werken. Teken vormen op de kaart en kijk hoe de gebeurtenissen vuren.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gebeurtenissen met tekengereedschappen" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>hulpprogramma's voor pentekenen van</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Voorbeelden

Laten we eens kijken naar een aantal veelvoorkomende scenario's die de gebeurtenissen met tekengereedschappen gebruiken.

### <a name="select-points-in-polygon-area"></a>Punten selecteren in veelhoekgebied

Deze code laat zien hoe u een gebeurtenis van een gebruiker die vormen tekent, controleren. In dit voorbeeld controleert de code vormen van veelhoeken, rechthoeken en cirkels. Vervolgens bepaalt het welke gegevenspunten op de kaart zich binnen het getekende gebied bevinden. De `drawingcomplete` gebeurtenis wordt gebruikt om de selectielogica te activeren. In de geselecteerde logica loopt de code door alle gegevenspunten op de kaart. Het controleert of er een kruispunt van het punt en het gebied van de getekende vorm. In dit voorbeeld wordt gebruik gemaakt van de open-source [Turf.js-bibliotheek](https://turfjs.org/) om een ruimtelijke snijberekening uit te voeren.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Gegevens selecteren in getekend veelhoekgebied" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/XWJdeja'>penselectiegegevens in getekend veelhoekgebied</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Tekenen en zoeken in veelhoekgebied

Deze code zoekt naar punten van belang in het gebied van een vorm nadat de gebruiker klaar is met het tekenen van de vorm. U de code wijzigen en uitvoeren door te klikken op 'Bewerken op codepen' in de rechterbovenhoek van het frame. De `drawingcomplete` gebeurtenis wordt gebruikt om de zoeklogica te activeren. Als de gebruiker een rechthoek of veelhoek tekent, wordt een zoekopdracht in de geometrie uitgevoerd. Als een cirkel wordt getekend, wordt de straal- en middelste positie gebruikt om een zoekpunt uit te voeren. De `drawingmodechanged` gebeurtenis wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar de tekenmodus en deze gebeurtenis wist het tekencanvas.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tekenen en zoeken in veelhoekgebied" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>pentekenen en zoeken in het veelhoekgebied</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Een meetinstrument maken

De onderstaande code laat zien hoe de tekengebeurtenissen kunnen worden gebruikt om een meetinstrument te maken. Het `drawingchanging` wordt gebruikt om de vorm te controleren, zoals het wordt getrokken. Als de gebruiker de muis beweegt, worden de afmetingen van de vorm berekend. De `drawingcomplete` gebeurtenis wordt gebruikt om een definitieve berekening op de vorm te doen nadat deze is getekend. De `drawingmodechanged` gebeurtenis wordt gebruikt om te bepalen wanneer de gebruiker overschakelt naar een tekenmodus. Ook wist `drawingmodechanged` het evenement het tekendoek en wist oude meetinformatie.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Meetgereedschap" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>gereedschap Penmeten</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>van Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van extra functies van de module tekengereedschappen:

> [!div class="nextstepaction"]
> [Vormgegevens ophalen](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interactietypen en sneltoetsen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over de Services-module:

> [!div class="nextstepaction"]
> [Services-module](how-to-use-services-module.md)

Bekijk meer code voorbeelden:

> [!div class="nextstepaction"]
> [Voorbeeldpagina code](https://aka.ms/AzureMapsSamples)
