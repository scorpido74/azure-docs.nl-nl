---
title: Informatie over een coördinaat weergeven op een kaart | Microsoft Azure Maps
description: Meer informatie over het weergeven van informatie over een adres op de kaart wanneer een gebruiker een coördinaat selecteert.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371421"
---
# <a name="get-information-from-a-coordinate"></a>Informatie ophalen uit een coördinaat

In dit artikel ziet u hoe u een omgekeerde adreszoekopdracht maken waarin het adres van een aangeklikte pop-uplocatie wordt weergegeven.

Er zijn twee manieren om een omgekeerd adres te zoeken. Een manier is om de [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) op te vragen via een servicemodule. De andere manier is om de [API Ophalen](https://fetch.spec.whatwg.org/) te gebruiken om een aanvraag in te dienen bij de Azure Maps Reverse Address [Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) om een adres te vinden. Beide manieren worden hieronder onderzocht.

## <a name="make-a-reverse-search-request-via-service-module"></a>Een omgekeerde zoekaanvraag indienen via de servicemodule

<iframe height='500' scrolling='no' title='Informatie ophalen van een coördinaat (servicemodule)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>informatie ophalen van een coördinaat (Service Module)</a> door Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste blok een kaartobject en stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Met het tweede `TokenCredential` codeblok wordt een code gemaakt om HTTP-aanvragen te verifiëren voor Azure Maps met het toegangstoken. Vervolgens wordt `TokenCredential` de `atlas.service.MapsURL.newPipeline()` aan- en wordt een [pijplijninstantie](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) gegeven. Het `searchURL` vertegenwoordigt een URL naar Azure Maps [Search](https://docs.microsoft.com/rest/api/maps/search) operations.

Het derde codeblok werkt de stijl van de muiscursor bij aan een aanwijzer en maakt een [pop-upobject.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) U [een pop-up op de kaart toevoegen](./map-add-popup.md) voor instructies.

Het vierde codeblok voegt een [muisklikgebeurtenislistener toe](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Wanneer geactiveerd, wordt een zoekopdracht met de coördinaten van het klikpunt. Vervolgens wordt de [methode getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)gebruikt om de [Reverse API voor zoekadres op](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) te vragen voor het adres van de coördinaten. Een GeoJSON-functieverzameling wordt vervolgens `geojson.getFeatures()` geëxtraheerd met behulp van de methode uit het antwoord.

In het vijfde codeblok wordt de HTML-pop-upinhoud ingesteld om het antwoordadres voor de aangeklikte coördinatenpositie weer te geven.

De wijziging van de cursor, het pop-upobject en de klikgebeurtenis worden allemaal gemaakt in de [gebeurtenislistener van](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)de kaart. Deze codestructuur zorgt ervoor dat de kaart volledig wordt geladen voordat de coördinateninformatie worden opgehaald.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Een omgekeerde zoekaanvraag indienen via Fetch API

Klik op de kaart om een omgekeerde geocode aanvraag voor die locatie met behulp van fetch.

<iframe height='500' scrolling='no' title='Informatie ophalen uit een coördinaat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen Informatie ophalen van<a href='https://codepen.io/azuremaps'>@azuremaps</a>een <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>coördinaat</a> van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code construeert het eerste codeblok een kaartobject en stelt het verificatiemechanisme in om het toegangstoken te gebruiken. U [een kaart maken](./map-create.md) voor instructies.

Het tweede codeblok werkt de stijl van de muiscursor bij aan een aanwijzer. Het instantiates een [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) object. U [een pop-up op de kaart toevoegen](./map-add-popup.md) voor instructies.

Het derde codeblok voegt een gebeurtenislistener toe voor muisklikken. Bij een muisklik gebruikt het de [API Ophalen](https://fetch.spec.whatwg.org/) om de Azure Maps Reverse Address [Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) op te vragen voor het aangeklikte coördinatenadres. Voor een succesvol antwoord verzamelt het adres voor de aangeklikte locatie. Het definieert de pop-upinhoud en -positie met behulp van de functie [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) van de pop-upklasse.

De wijziging van de cursor, het pop-upobject en de klikgebeurtenis worden allemaal gemaakt in de [gebeurtenislistener van](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)de kaart. Deze codestructuur zorgt ervoor dat de kaart volledig wordt geladen voordat de coördinateninformatie worden opgehaald.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de zoekservice](how-to-use-best-practices-for-search.md)

Meer informatie over de lessen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zie de volgende artikelen voor volledige code voorbeelden:

> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](./map-route.md)

> [!div class="nextstepaction"]
> [Verkeer weergeven](./map-show-traffic.md)
