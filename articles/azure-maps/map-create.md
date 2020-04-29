---
title: Een kaart maken met Azure Maps | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een kaart op een webpagina kunt weer geven met behulp van de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802328"
---
# <a name="create-a-map"></a>Een kaart maken

Dit artikel laat u zien hoe u een kaart maakt en een kaart kunt animeren.  

## <a name="loading-a-map"></a>Een kaart laden

Als u een kaart wilt laden, maakt u een nieuw exemplaar van de [kaart klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Wanneer u de kaart initialiseert, geeft u de ID van een DIV-element door om de kaart weer te geven en een set opties te geven die moeten worden gebruikt bij het laden van de kaart. Als er geen standaard verificatie gegevens zijn opgegeven `atlas` in de naam ruimte, moet deze informatie worden opgegeven in de kaart opties bij het laden van de kaart. De kaart laadt verschillende bronnen asynchroon voor prestaties. Koppel, na het maken van het exemplaar van de kaart `ready` , `load` een of-gebeurtenis aan de kaart en voeg vervolgens extra code toe die met de kaart communiceert met de gebeurtenis-handler. De `ready` gebeurtenis wordt geactiveerd zodra de kaart voldoende resources heeft geladen om via een programma te communiceren. De `load` gebeurtenis wordt geactiveerd nadat het laden van de eerste kaart weergave volledig is voltooid. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eenvoudige kaart belasting" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> U kunt meerdere kaarten op dezelfde pagina laden. Meerdere kaarten op dezelfde pagina kunnen dezelfde of verschillende verificatie-en taal instellingen gebruiken.

## <a name="show-a-single-copy-of-the-world"></a>Eén exemplaar van de wereld weer geven

Wanneer de kaart op een breed scherm wordt ingezoomd, worden meerdere kopieën van de wereld horizon taal weer gegeven. Deze optie is ideaal voor sommige scenario's, maar voor andere toepassingen is het wenselijk om één exemplaar van de wereld te bekijken. Dit gedrag wordt geïmplementeerd door de toewijzings `renderWorldCopies` optie in `false`te stellen op.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = False" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = False</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Kaart opties

Wanneer u een kaart maakt, zijn er verschillende soorten opties die kunnen worden door gegeven om aan te passen hoe de kaart functies zoals hieronder worden weer gegeven.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) en [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) worden gebruikt om het gebied op te geven dat wordt weer gegeven op de kaart.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) worden gebruikt om op te geven hoe de kaart moet communiceren met services die de kaart uitschakelen.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) worden gebruikt om op te geven dat de kaart moet worden opgemaakt en gerenderd.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) worden gebruikt om op te geven hoe de kaart moet worden bereikt wanneer de gebruiker met de kaart communiceert. 

Deze opties kunnen ook worden bijgewerkt nadat de kaart is `setCamera`geladen met de `setServiceOptions` `setStyle` `setUserInteraction` functies,, en. 

## <a name="controlling-the-map-camera"></a>De kaart camera beheren

Er zijn twee manieren om het weer gegeven gebied van de kaart in te stellen met behulp van de camera van een kaart. U kunt de camera opties instellen tijdens het laden van de kaart. U kunt de `setCamera` optie ook op elk gewenst moment aanroepen nadat de kaart is geladen om de kaart weergave programmatisch bij te werken.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>De camera instellen

De kaart camera bepaalt wat wordt weer gegeven in de View Port van het canvas van de kaart. Camera opties kunnen worden door gegeven aan de kaart opties wanneer deze worden geïnitialiseerd of door gegeven aan `setCamera` de functie Maps.

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

In de volgende code wordt een [kaart object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) gemaakt en worden de opties voor centreren en zoomen ingesteld. Eigenschappen van kaarten, zoals centreren en zoom niveau, maken deel uit van de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>een kaart `CameraOptions` maken </a>met behulp van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Location Based Services () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>De grenzen van de camera instellen

Een selectie kader kan worden gebruikt om de kaart camera bij te werken. Als het selectie kader is berekend op basis van punt gegevens, is het vaak handig om ook een waarde voor pixel opvulling op te geven in de camera opties om te bepalen wat de grootte van het pictogram is. Dit helpt ervoor te zorgen dat punten de rand van de kaart-View Port niet vallen.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

In de volgende code wordt een [kaart object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) gemaakt via `new atlas.Map()`. Kaart eigenschappen zoals `CameraBoundsOptions` kan worden gedefinieerd via de functie [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) van de klasse map. Eigenschappen voor grenzen en opvulling worden ingesteld met `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>een kaart `CameraBoundsOptions` maken </a>met behulp van<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Kaart weergave animeren

Wanneer u de camera opties van de kaart instelt, kunnen er ook [animatie opties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) worden ingesteld. Met deze opties geeft u het type animatie en de duur op waarmee de camera moet worden verplaatst.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

In de volgende code wordt in het eerste code blok een kaart gemaakt en worden de stijlen voor het invoeren en inzoomen ingesteld. In het tweede code blok wordt een gebeurtenis-handler voor klikken gemaakt voor de knop animatie. Wanneer u op deze knop klikt, `setCamera` wordt de functie aangeroepen met een wille keurige waarde voor [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) en [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Kaart weergave animeren' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WayvbO/'>kaart weergave</a> van de pen animeren per Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>De code uitproberen

Bekijk de code voorbeelden. U kunt de Java script-code bewerken op het **tabblad js** en de kaart weergave wijzigingen weer geven op het **tabblad resultaat**. U kunt ook op **Bewerken klikken op CodePen**in de rechter bovenhoek en de code wijzigen in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Zie code voorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Stijl van de kaart wijzigen](choose-map-style.md)

> [!div class="nextstepaction"]
> [Besturingselementen toevoegen aan de kaart](map-add-controls.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
