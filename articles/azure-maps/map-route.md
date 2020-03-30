---
title: Routebeschrijvingen weergeven op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u routebeschrijvingen weergeeft tussen twee locaties op een kaart met behulp van de Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371411"
---
# <a name="show-directions-from-a-to-b"></a>Routebeschrijving van A naar B

In dit artikel ziet u hoe u een routeaanvraag indienen en de route op de kaart weergeven.

Er zijn twee manieren om dit te doen. De eerste manier is om de [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) op te vragen via een servicemodule. De tweede manier is om de [API Ophalen](https://fetch.spec.whatwg.org/) te gebruiken om een zoekaanvraag in te dienen bij de Azure Maps [Route API.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Beide manieren worden hieronder besproken.

## <a name="query-the-route-via-service-module"></a>De route opvragen via servicemodule

<iframe height='500' scrolling='no' title='Routebeschrijving van A naar B weergeven op een kaart (servicemodule)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de <a href='https://codepen.io/azuremaps/pen/RBZbep/'>opdrachtvragen pen weergeven van A naar B op een kaart (Service Module)</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste blok een kaartobject en stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Het tweede codeblok `TokenCredential` maakt een code om HTTP-aanvragen te verifiëren voor Azure Maps met het toegangstoken. Vervolgens wordt `TokenCredential` de `atlas.service.MapsURL.newPipeline()` aan- en wordt een [pijplijninstantie](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) gegeven. Het `routeURL` vertegenwoordigt een URL naar Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) bewerkingen.

Het derde codeblok maakt en voegt een [DataSource-object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) toe aan de kaart.

Het vierde codeblok maakt begin- en [eindpuntobjecten](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) en voegt deze toe aan het object dataSource.

Een regel is een [functie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) voor LineString. Een [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) geeft lijnobjecten die in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt, weer als lijnen op de kaart. Het vierde codeblok maakt en voegt een lijnlaag toe aan de kaart. Zie eigenschappen van een lijnlaag bij [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Een [symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) gebruikt teksten of pictogrammen om op punten gebaseerde gegevens weer te geven die zijn verpakt in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). De teksten of de pictogrammen worden weergegeven als symbolen op de kaart. Het vijfde blok code maakt en voegt een symboollaag toe aan de kaart.

In het zesde blok code wordt de azure maps-routeringsservice opgevraagd, die deel uitmaakt van de [servicemodule.](how-to-use-services-module.md) De [methode calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) van de RouteURL wordt gebruikt om een route tussen de begin- en eindpunten te krijgen. Een GeoJSON-functieverzameling uit het antwoord `geojson.getFeatures()` wordt vervolgens geëxtraheerd met behulp van de methode en wordt toegevoegd aan de gegevensbron. Het maakt vervolgens de reactie als een route op de kaart. Zie een regel toevoegen op de kaart voor meer informatie over het toevoegen van een regel [aan de kaart.](map-add-line-layer.md)

Het laatste codeblok stelt de grenzen van de kaart in met de eigenschap [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

De routequery, gegevensbron, symbool, lijnlagen en cameragrenzen worden gemaakt in de [gebeurtenislistener.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Deze codestructuur zorgt ervoor dat de resultaten pas worden weergegeven nadat de kaart volledig is geladen.

## <a name="query-the-route-via-fetch-api"></a>De route opvragen via Fetch API

<iframe height='500' scrolling='no' title='Routebeschrijving van A naar B weergeven op een kaart' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de aanwijzingen van Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show van A naar B op een kaart</a> van Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste codeblok een kaartobject en stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Het tweede codeblok maakt en voegt een [DataSource-object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) toe aan de kaart.

Het derde codeblok maakt de begin- en bestemmingspunten voor de route. Vervolgens voegt het ze toe aan de gegevensbron. U [een pin op de kaart toevoegen](map-add-pin.md) voor instructies over het gebruik van [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Een [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) geeft lijnobjecten die in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt, weer als lijnen op de kaart. Het vierde codeblok maakt en voegt een lijnlaag toe aan de kaart. Zie eigenschappen van een lijnlaag bij [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Een [symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) gebruikt tekst of pictogrammen om op punten gebaseerde gegevens die in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt, weer te geven als symbolen op de kaart. Het vijfde blok code maakt en voegt een symboollaag toe aan de kaart. Zie eigenschappen van een symboollaag bij [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Het volgende codeblok maakt `SouthWest` en `NorthEast` wijst vanaf het begin en de doelpunten en stelt de grenzen van de kaart in met de eigenschap [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

Het laatste codeblok gebruikt de [API ophalen](https://fetch.spec.whatwg.org/) om een zoekaanvraag in te dienen bij de Azure Maps [Route API.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Het antwoord wordt vervolgens ontleed. Als de respons is geslaagd, wordt de informatie over breedte- en lengtegraad gebruikt om een array een lijn te maken door deze punten met elkaar te verbinden. De lijngegevens worden vervolgens toegevoegd aan de gegevensbron om de route op de kaart weer te geven. U [een regel op de kaart toevoegen](map-add-line-layer.md) voor instructies.

De routequery, gegevensbron, symbool, lijnlagen en cameragrenzen worden gemaakt in de [gebeurtenislistener.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Nogmaals, we willen ervoor zorgen dat de resultaten volledig worden weergegeven nadat de kaart volledig is geladen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de routeringsservice](how-to-use-best-practices-for-search.md)

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor volledige code voorbeelden:

> [!div class="nextstepaction"]
> [Verkeer weergeven op de kaart](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interactie met de kaart - muisgebeurtenissen](./map-events.md)
