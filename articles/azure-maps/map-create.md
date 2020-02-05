---
title: Een kaart maken met Azure Maps | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een kaart op een webpagina kunt weer geven met behulp van de Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988580"
---
# <a name="create-a-map"></a>Een kaart maken

Dit artikel laat u zien hoe u een kaart maakt en een kaart kunt animeren.  

## <a name="loading-a-map"></a>Een kaart laden

Als u een kaart wilt laden, maakt u een nieuw exemplaar van de [kaart klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Wanneer u de kaart initialiseert, geeft u de ID van een DIV-element door om de kaart weer te geven en een set opties te geven die moeten worden gebruikt bij het laden van de kaart. Als er geen standaard verificatie gegevens zijn opgegeven voor de `atlas` naam ruimte, moet deze informatie worden opgegeven in de kaart opties bij het laden van de kaart. De kaart laadt verschillende bronnen asynchroon voor prestaties. Als dat zo is, koppelt u na het maken van het exemplaar van de kaart een `ready`-of `load` gebeurtenis aan de kaart en voegt u vervolgens extra code toe die met de kaart communiceert met de gebeurtenis-handler. De gebeurtenis `ready` wordt geactiveerd zodra de kaart voldoende resources heeft geladen om via een programma te communiceren. De gebeurtenis `load` wordt gestart nadat het laden van de eerste kaart weergave volledig is voltooid. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eenvoudige kaart belasting" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> U kunt meerdere kaarten op dezelfde pagina laden. Meerdere kaarten op dezelfde pagina kunnen dezelfde of verschillende verificatie-en taal instellingen gebruiken.

## <a name="show-a-single-copy-of-the-world"></a>Eén exemplaar van de wereld weer geven

Wanneer de kaart op een breed scherm wordt ingezoomd, worden meerdere kopieën van de wereld horizon taal weer gegeven. Deze optie is ideaal voor sommige scenario's, maar voor andere toepassingen is het wenselijk om één exemplaar van de wereld te bekijken. Dit gedrag wordt geïmplementeerd door het instellen van de toewijzings `renderWorldCopies` optie voor `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = False" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = False</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>De kaart camera beheren

Er zijn twee manieren om het weer gegeven gebied van de kaart in te stellen met behulp van de camera van een kaart. U kunt de camera opties instellen tijdens het laden van de kaart. U kunt de `setCamera`-optie op elk gewenst moment aanroepen nadat de kaart is geladen om de kaart weergave programmatisch bij te werken.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>De camera instellen

In de volgende code wordt een [kaart object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) gemaakt en worden de opties voor centreren en zoomen ingesteld. Eigenschappen van kaarten, zoals centreren en zoom niveau, maken deel uit van de [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>een kaart maken via `CameraOptions` </a>door Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>De grenzen van de camera instellen

In de volgende code wordt een [kaart object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) gemaakt via `new atlas.Map()`. Eigenschappen van de kaart, zoals `CameraBoundsOptions`, kunnen worden gedefinieerd via de functie [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) van de klasse map. Eigenschappen voor grenzen en opvulling worden ingesteld met behulp van `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>een kaart maken via `CameraBoundsOptions` </a>door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Kaart weergave animeren

In de volgende code wordt in het eerste code blok een kaart gemaakt en worden de stijlen voor het invoeren en inzoomen ingesteld. In het tweede code blok wordt een gebeurtenis-handler voor klikken gemaakt voor de knop animatie. Wanneer u op deze knop klikt, wordt de functie `setCamera` aangeroepen met een aantal wille keurige waarden voor [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) en [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Kaart weergave animeren' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WayvbO/'>kaart weergave</a> van de pen-animatie per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>De code uitproberen

Bekijk de code voorbeelden. U kunt de Java script-code bewerken op het **tabblad js** en de kaart weergave wijzigingen weer geven op het **tabblad resultaat**. U kunt ook op **Bewerken klikken op CodePen**in de rechter bovenhoek en de code wijzigen in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Zie code voorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Stijl van de kaart wijzigen](choose-map-style.md)

> [!div class="nextstepaction"]
> [Besturings elementen toevoegen aan de kaart](map-add-controls.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
