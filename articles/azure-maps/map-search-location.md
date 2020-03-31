---
title: Zoekresultaten weergeven op een kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u een zoekaanvraag uitvoert met Microsoft Azure Maps Web SDK en de resultaten op de kaart weergeeft.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371402"
---
# <a name="show-search-results-on-the-map"></a>Zoekresultaten weergeven op de kaart

In dit artikel ziet u hoe u zoeken naar de locatie van interesse en de zoekresultaten op de kaart weergeven.

Er zijn twee manieren om te zoeken naar een locatie van belang. Een manier is om een servicemodule te gebruiken om een zoekverzoek in te dienen. De andere manier is om een zoekaanvraag te maken voor [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via de Fetch [API](https://fetch.spec.whatwg.org/). Beide manieren worden hieronder besproken.

## <a name="make-a-search-request-via-service-module"></a>Een zoekverzoek indienen via de servicemodule

<iframe height='500' scrolling='no' title='Zoekresultaten weergeven op een kaart (servicemodule)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de zoekresultaten van Pen Tonen op een kaart<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>(ServiceModule)</a> van Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste blok een kaartobject en stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Het tweede codeblok `TokenCredential` maakt een code om HTTP-aanvragen te verifiëren voor Azure Maps met het toegangstoken. Vervolgens wordt `TokenCredential` de `atlas.service.MapsURL.newPipeline()` aan- en wordt een [pijplijninstantie](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) gegeven. Het `searchURL` vertegenwoordigt een URL naar Azure Maps [Search](https://docs.microsoft.com/rest/api/maps/search) operations.

Het derde codeblok maakt een gegevensbronobject met de klasse [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) en voegt er zoekresultaten aan toe. Een [symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) gebruikt tekst of pictogrammen om op punten gebaseerde gegevens die in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt, weer te geven als symbolen op de kaart.  Vervolgens wordt een symboollaag gemaakt. De gegevensbron wordt toegevoegd aan de symboollaag, die vervolgens aan de kaart wordt toegevoegd.

Het vierde codeblok gebruikt de [methode SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) in de [servicemodule](how-to-use-services-module.md). Hiermee u een gratis formulier tekst zoeken via de [Get Search Fuzzy rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) om te zoeken naar punt van belang uit te voeren. Als u aanvragen naar de Search Fuzzy API krijgt, u elke combinatie van vage ingangen verwerken. Een GeoJSON-functieverzameling uit het antwoord `geojson.getFeatures()` wordt vervolgens geëxtraheerd met behulp van de methode en toegevoegd aan de gegevensbron, wat automatisch resulteert in de gegevens die via de symboollaag op de kaart worden weergegeven.

Het laatste codeblok past de cameragrenzen voor de kaart aan met de eigenschap [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

De zoekaanvraag, gegevensbron, symboollaag en cameragrenzen bevinden zich in de [gebeurtenislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) van de kaart. We willen ervoor zorgen dat de resultaten worden weergegeven na de kaart volledig wordt geladen.


## <a name="make-a-search-request-via-fetch-api"></a>Een zoekaanvraag indienen via Fetch API

<iframe height='500' scrolling='no' title='Zoekresultaten weergeven op een kaart' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de zoekresultaten van Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Weergeven</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>op een kaart van Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste codeblok een kaartobject. Het stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Met het tweede codeblok wordt een URL aangegaan om een zoekverzoek in te dienen. Het maakt ook twee arrays om grenzen en pinnen op te slaan voor zoekresultaten.

Het derde codeblok maakt gebruik van de [API Ophalen](https://fetch.spec.whatwg.org/). De [API ophalen](https://fetch.spec.whatwg.org/) wordt gebruikt om een aanvraag in te dienen voor Azure Maps Fuzzy search [API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) om te zoeken naar de bezienswaardigheden. De Fuzzy search API kan elke combinatie van fuzzy ingangen aan. Vervolgens wordt het zoekantwoord verwerkt en ontrelijkt en wordt de resultaatpins toegevoegd aan de array searchPins.

Met het vierde codeblok wordt een gegevensbronobject gemaakt met de klasse [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) In de code voegen we zoekresultaten toe aan het bronobject. Een [symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) gebruikt tekst of pictogrammen om op punten gebaseerde gegevens die in de [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) zijn verpakt, weer te geven als symbolen op de kaart. Vervolgens wordt een symboollaag gemaakt. De gegevensbron wordt toegevoegd aan de symboollaag, die vervolgens aan de kaart wordt toegevoegd.

Met het laatste codeblok wordt een [BoundingBox-object](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) gemaakt. Het maakt gebruik van de array van de resultaten, en vervolgens past de camera grenzen voor de kaart met behulp van de [map setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Het maakt dan het resultaat pinnen.

De zoekaanvraag, de gegevensbron, de symboollaag en de cameragrenzen zijn ingesteld in de [gebeurtenislistener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) van de kaart om ervoor te zorgen dat de resultaten worden weergegeven nadat de kaart volledig is geladen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de zoekservice](how-to-use-best-practices-for-search.md)

Meer informatie over **Fuzzy Search:**

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor volledige code voorbeelden:

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](map-route.md)
