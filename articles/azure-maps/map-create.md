---
title: Een kaart maken met Azure Maps | Microsoft Azure Maps
description: In dit artikel leert u hoe u een kaart op een webpagina weergeeft met de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534913"
---
# <a name="create-a-map"></a>Een kaart maken

In dit artikel ziet u manieren om een kaart te maken en een kaart te animeren.  

## <a name="loading-a-map"></a>Een kaart laden

Als u een kaart wilt laden, maakt u een nieuw exemplaar van de [klasse Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Wanneer u de kaart initialiseert, geeft u een DIV-element-ID door om de kaart weer te geven en geeft u een reeks opties door die u gebruiken bij het laden van de kaart. Als standaardverificatiegegevens niet zijn `atlas` opgegeven in de naamruimte, moet deze informatie worden opgegeven in de kaartopties bij het laden van de kaart. De kaart laadt verschillende resources asynchroon voor prestaties. Als zodanig, na het maken `ready` van `load` de kaart instantie, een of gebeurtenis aan de kaart en voeg dan een extra code die interageert met de kaart aan de gebeurtenis handler. De `ready` gebeurtenis wordt geactiveerd zodra de kaart voldoende resources heeft geladen om programmatisch te worden gebruikt. De `load` gebeurtenis wordt geactiveerd nadat de eerste kaartweergave volledig is voltooid. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basiskaartbelasting" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic-kaartbelasting</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> U meerdere kaarten op dezelfde pagina laden. Meerdere kaarten op dezelfde pagina kunnen dezelfde of verschillende verificatie- en taalinstellingen gebruiken.

## <a name="show-a-single-copy-of-the-world"></a>Laat één exemplaar van de wereld zien

Wanneer de kaart wordt uitgezoomd op een breed scherm, worden meerdere kopieën van de wereld horizontaal weergegeven. Deze optie is ideaal voor sommige scenario's, maar voor andere toepassingen is het wenselijk om een enkele kopie van de wereld te zien. Dit gedrag wordt geïmplementeerd door `renderWorldCopies` de `false`optie Kaarten in te stellen op .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies =</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>false by Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Kaartopties

Bij het maken van een kaart zijn er verschillende soorten opties die kunnen worden doorgegeven om aan te passen hoe de kaart functioneert zoals hieronder vermeld.

- [CameraOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) en [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) worden gebruikt om het gebied op te geven dat de kaart moet weergeven.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) wordt gebruikt om op te geven hoe de kaart moet communiceren met services die de kaart van stroom voorzien.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) worden gebruikt om op te geven dat de kaart moet worden gestyled en weergegeven.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) worden gebruikt om aan te geven hoe de kaart moet bereiken wanneer de gebruiker interactie heeft met de kaart. 

Deze opties kunnen ook worden bijgewerkt nadat de `setCamera` `setServiceOptions`kaart `setStyle`is `setUserInteraction` geladen met behulp van de , , , en functies. 

## <a name="controlling-the-map-camera"></a>De kaartcamera bedienen

Er zijn twee manieren om het weergegeven gebied van de kaart in te stellen met behulp van de camera van een kaart. U de cameraopties instellen bij het laden van de kaart. U de `setCamera` optie ook op elk gewenst moment aanroepen nadat de kaart is geladen om de kaartweergave programmatisch bij te werken.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>De camera instellen

De kaartcamera bepaalt wat er wordt weergegeven in de viewport van het kaartcanvas. Camera-opties kunnen worden doorgegeven aan de kaart opties `setCamera` wanneer wordt geïnitialiseerd of doorgegeven in de kaarten functie.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

In de volgende code wordt een [mapobject](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) gemaakt en worden de opties voor midden en zoom ingesteld. Kaarteigenschappen, zoals midden- en zoomniveau, maken deel uit van de [cameraopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Een `CameraOptions` kaart maken </a>via<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Location Based Services () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>De cameragrenzen instellen

Een selectiekader kan worden gebruikt om de kaartcamera bij te werken. Als het selectiekader is berekend op basis van puntgegevens, is het vaak handig om ook een pixelopvullingswaarde op te geven in de cameraopties om rekening te houden met de pictogramgrootte. Dit zal ervoor zorgen dat punten niet van de rand van de kaart viewport vallen.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

In de volgende code wordt een `new atlas.Map()` [mapobject](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) geconstrueerd via . Kaarteigenschappen zoals `CameraBoundsOptions` kunnen worden gedefinieerd via [de functie setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) van de klasse Kaart. Grenzen en opvullingseigenschappen `setCamera`worden ingesteld met behulp van .

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Een `CameraBoundsOptions` kaart </a>maken<a href='https://codepen.io/azuremaps'>@azuremaps</a>via Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Kaartweergave met animatie-animatie

Bij het instellen van de camera-opties van de kaart kunnen ook [animatieopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) worden ingesteld. Deze opties geven het type animatie en de duur op die nodig zijn om de camera te verplaatsen.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

In de volgende code maakt het eerste codeblok een kaart en stelt u de stijlen Enter- en Zoommap in. In het tweede codeblok wordt een klikgebeurtenishandler gemaakt voor de animatieknop. Wanneer op deze knop `setCamera` wordt geklikt, wordt de functie aangeroepen met enkele willekeurige waarden voor de [Opties voor cameraopties](/javascript/api/azure-maps-control/atlas.cameraoptions) en [animaties](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Kaartweergave met animatie animeren' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WayvbO/'>kaartweergave Voor het animeren van</a> pen door Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Probeer de code

Kijk naar de code monsters. U de JavaScript-code bewerken op het **tabblad JS** en de wijzigingen in de kaartweergave bekijken op het **tabblad Resultaat**. U ook rechtsboven **op CodePen**bewerken klikken en de code in CodePen wijzigen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Cameraopties](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Opties voor animaties](/javascript/api/azure-maps-control/atlas.animationoptions)

Zie codevoorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Stijl van de kaart wijzigen](choose-map-style.md)

> [!div class="nextstepaction"]
> [Besturingselementen toevoegen aan de kaart](map-add-controls.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
