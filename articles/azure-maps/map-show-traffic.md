---
title: Verkeer weergeven op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u verkeersgegevens op een kaart weergeeft met behulp van de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534811"
---
# <a name="show-traffic-on-the-map"></a>Verkeer weergeven op de kaart

Er zijn twee typen verkeersgegevens beschikbaar in Azure Maps:

- Incidentgegevens - bestaat uit punt- en lijngegevens voor zaken als de aanleg, wegafsluitingen en ongevallen.
- Stroomgegevens - biedt statistieken over de doorstroming van het verkeer op de wegen. Vaak worden verkeersstroomgegevens gebruikt om de wegen te kleuren. De kleuren zijn gebaseerd op hoeveel verkeer de stroom vertraagt, ten opzichte van de maximumsnelheid of een andere statistiek. De verkeersstroomgegevens in Azure Maps hebben drie verschillende meetstatistieken:
    - `relative`- is relatief ten opzichte van de vrije doorstromingsnelheid van de weg.
    - `absolute`- is de absolute snelheid van alle voertuigen op de weg.
    - `relative-delay`- geeft gebieden weer die langzamer zijn dan de gemiddelde verwachte vertraging.

In de volgende code ziet u hoe u verkeersgegevens op de kaart weergeeft.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Hieronder vindt u het volledige voorbeeld van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Verkeer weergeven op een kaart' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>penweerverkeer weergeven op een kaart</a> van Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opties voor overlay verkeer

Met het volgende gereedschap u schakelen tussen de verschillende instellingen voor verkeersoverlays om te zien hoe de weergave verandert. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor overlay verkeer" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>overlayopties voor penverkeer</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Verbeter uw gebruikerservaringen:

> [!div class="nextstepaction"]
> [Interactie met muisgebeurtenissen toewijzen](map-events.md)

> [!div class="nextstepaction"]
> [Een toegankelijke kaart bouwen](map-accessibility.md)

> [!div class="nextstepaction"]
> [Voorbeeldpagina code](https://aka.ms/AzureMapsSamples)
