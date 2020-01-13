---
title: Informatie over een coördinaat op een kaart weer geven | Microsoft Azure kaarten
description: Informatie over het weer geven van gegevens over een adres op de kaart wanneer een gebruiker een coördinaat selecteert.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5450ada04a1af44a3fff0402b30540e899cc4dd5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911050"
---
# <a name="get-information-from-a-coordinate"></a>Informatie ophalen uit een coördinaat

In dit artikel wordt beschreven hoe u een omgekeerde adres zoekactie kunt maken met het adres van een klik op de pop-upbesturingselementnaam.

Er zijn twee manieren om een omgekeerde adres zoekactie te maken. Een manier is om de [Azure Maps reverse lookup-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) van het adres te doorzoeken via een service module. De andere manier is om de [ophaal-API](https://fetch.spec.whatwg.org/) te gebruiken om een aanvraag in te dienen bij de [Azure Maps reverse lookup-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) om een adres te vinden. Beide manieren worden hieronder weer gegeven.

## <a name="make-a-reverse-search-request-via-service-module"></a>Een Reverse Search-aanvraag maken via de service module

<iframe height='500' scrolling='no' title='Informatie ophalen uit een coördinaat (Service module)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>informatie ophalen uit een coördinaat (Service module)</a> door Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste code blok een kaart object en stelt het verificatie mechanisme in voor gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Met het tweede code blok wordt een `TokenCredential` voor het verifiëren van HTTP-aanvragen voor Azure Maps met het toegangs token gemaakt. Vervolgens wordt de `TokenCredential` door gegeven aan `atlas.service.MapsURL.newPipeline()` en wordt een [pijplijn](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instantie gemaakt. De `searchURL` vertegenwoordigt een URL voor het Azure Maps van [Zoek](https://docs.microsoft.com/rest/api/maps/search) bewerkingen.

Met het derde code blok wordt de stijl van de muis aanwijzer naar een wijzer bijgewerkt en wordt een [pop](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) -upobject gemaakt. U kunt [een pop-upvenster toevoegen op de kaart](./map-add-popup.md) voor instructies.

Het vierde code blok voegt een gebeurtenislistener toe aan een [muis klik.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Als deze wordt geactiveerd, wordt er een zoek query gemaakt met de coördinaten van het geklikte punt. Vervolgens wordt de methode [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) van de service module gebruikt om een query uit te zoeken naar de [Reverse-API Get-adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) voor het adres van de coördinaten. Een verzameling geojson-functies uit het antwoord wordt vervolgens geëxtraheerd met behulp van de `geojson.getFeatures()` methode.

Met het vijfde code blok wordt de inhoud van het HTML-pop-upvenster ingesteld om het antwoord adres voor de geklikte coördinaat positie weer te geven.

De wijziging van de cursor, een pop-upobject en de Click-gebeurtenis worden allemaal gemaakt in de [Load event-listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) van de kaart om ervoor te zorgen dat de kaart volledig wordt geladen voordat de coördinaten gegevens kunnen worden opgehaald.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Een Reverse Search-aanvraag indienen via de API voor ophalen

Klik op de kaart om een reverse Geocode-aanvraag voor die locatie te maken met behulp van ophalen.

<iframe height='500' scrolling='no' title='Informatie ophalen uit een coördinaat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Raadpleeg de pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>informatie ophalen uit een coördineren</a> per Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste code blok een kaart object en stelt het verificatie mechanisme in voor gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Met het tweede code blok wordt de stijl van de muis aanwijzer bijgewerkt naar een aanwijzer en een [pop](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) -upobject. U kunt [een pop-upvenster toevoegen op de kaart](./map-add-popup.md) voor instructies.

Het derde code blok voegt een gebeurtenislistener toe voor muis klikken. Wanneer u op een muis klik klikt, wordt de [ophaal-API](https://fetch.spec.whatwg.org/) gebruikt om een query uit te [zoeken naar Azure Maps reverse lookup-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) voor het geklikte coördinaten adres. Voor een geslaagde reactie verzamelt het het adres voor de geklikte locatie en definieert de pop-up-inhoud en de positie via de functie [SetOption](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) van de pop-upklasse.

De wijziging van de cursor, een pop-upobject en de Click-gebeurtenis worden allemaal gemaakt in de [Load event-listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) van de kaart om ervoor te zorgen dat de kaart volledig wordt geladen voordat de coördinaten gegevens kunnen worden opgehaald.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Route beschrijving van A naar B weer geven](./map-route.md)

> [!div class="nextstepaction"]
> [Verkeer weer geven](./map-show-traffic.md)
