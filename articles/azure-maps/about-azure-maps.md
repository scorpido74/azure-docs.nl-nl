---
title: Overzicht | Microsoft Azure Maps
description: Meer informatie over services en mogelijkheden in Microsoft Azure Maps en hoe u deze gebruiken in uw toepassingen.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdac12350db785e6194cd3d057f4d2adfefa5969
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335737"
---
# <a name="what-is-azure-maps"></a>Wat is Azure Maps?

Azure Maps is een verzameling georuimtelijke services die nieuwe toewijzingsgegevens gebruiken om geografische context te bieden aan web- en mobiele toepassingen. Azure Maps biedt:

* REST API's om kaarten in meerdere stijlen en in satellietbeelden weer te geven.''
* Zoekservices om adressen, plaatsen en bezienswaardigheden over de hele wereld te vinden.
* Verschillende routeringsopties; zoals point-to-point, multipoint, multipoint optimalisatie, isochrone, bedrijfsvoertuig, verkeer beïnvloed, en matrix routing.
* Weergave verkeersstroom weergave en incidenten bekijken, voor toepassingen die verkeersinformatie vereisen.
* Mobiliteitsdienst om de logistiek van het openbaar vervoer aan te vragen, routes in realtime te plannen en informatie op te vragen voor alternatieve vervoerswijzen.
* Tijdzone- en geolocatieservices en het converteren van een locatie naar tijdzones.
* Geofencing-service en het toewijzen van gegevensopslag, met locatiegegevens die worden gehost in Azure. 
* Locatie-intelligentie door middel van geospatiale analyse. 

Daarnaast zijn Azure Maps-services beschikbaar via de Web SDK of de Android SDK. Met deze tools kunnen ontwikkelaars snel oplossingen ontwikkelen en schalen die locatiegegevens integreren in Azure-oplossingen. 

U zich aanmelden voor een gratis [Azure Maps-account](https://azure.microsoft.com/services/azure-maps/) en beginnen met ontwikkelen.

In de volgende video wordt Azure Maps in detail uitgelegd:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kaartbesturingselementen

### <a name="web-sdk"></a>Web SDK

Met de Azure Maps Web SDK u interactieve kaarten aanpassen met uw eigen inhoud en afbeeldingen. U deze interactieve kaart gebruiken voor zowel uw web- als mobiele applicaties. Het kaartbesturingselement maakt gebruik van WebGL, zodat u grote gegevenssets met hoge prestaties renderen. Ontwikkel met de SDK met JavaScript of TypeScript.

![Voorbeeldkaart van bevolkingsverandering](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Gebruik de Azure Maps Android SDK om mobiele toewijzingstoepassingen te maken. 

![Voorbeelden van kaarten op een mobiel apparaat](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Services in Azure Maps

Azure Maps bestaat uit de volgende negen services die geografische context kunnen bieden voor uw Azure-toepassingen.

### <a name="data-service"></a>Gegevensservice

Gegevens zijn noodzakelijk voor kaarten. Gebruik Data Service om georuimtelijke gegevens te uploaden en op te slaan voor gebruik met ruimtelijke bewerkingen of beeldsamenstelling.  Door klantgegevens dichter bij de Azure Maps-service te brengen, wordt de latentie verminderd, de productiviteit verhoogd en worden nieuwe scenario's in uw toepassingen gemaakt. Zie de [API-documentatie van](https://docs.microsoft.com/rest/api/maps/data)de Data Service API voor meer informatie over deze service.

### <a name="mobility-service"></a>Mobility-service

De Azure Maps Mobility-service maakt real-time reisplanning mogelijk. Het retourneert de best mogelijke routeopties en biedt verschillende soorten reismodi. Voor metro (stad) gebieden, deze modi kunnen bestaan uit wandelen, fietsen, en openbaar vervoer. U een transitroute, lijngeometrie, lijsten met stops, geplande aankomsten, realtime aankomsten en servicewaarschuwingen aanvragen.

De service maakt het ook mogelijk om te zoeken naar specifieke objecttypen rond een locatie. Gebruikers kunnen rond een locatie zoeken naar gedeelde fietsen, scooters of auto's. Gebruikers kunnen het aantal beschikbare fietsen aanvragen in het dichtstbijzijnde dock en beschikbare voertuigen doorzoeken voor het delen van ritten. En, gebruikers kunnen details vinden, zoals de toekomstige beschikbaarheid van voertuigen en het huidige brandstofniveau.

Zie de [Mobility API-documentatie](https://docs.microsoft.com/rest/api/maps/mobility)voor meer informatie over de service.

### <a name="render-service"></a>Service voor rendering

De Render-service helpt ontwikkelaars bij het maken van web- en mobiele toepassingen met kaartfuncties. De service maakt gebruik van een raster met grafische afbeeldingen van hoge kwaliteit, beschikbaar in 19 zoomniveaus, of volledig aanpasbare kaartafbeeldingen in een vectorindeling.

![Voorbeeld van een kaart van de Render-service](media/about-azure-maps/intro_map.png)

De Render Service biedt nu preview-API's die ontwikkelaars met satellietbeelden laten werken. Lees voor meer informatie de [API-documentatie render](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Routeservice

De Route Service bevat robuuste geometrische berekeningen voor de infrastructuur in de echte wereld en routebeschrijvingen voor meerdere manieren van reizen. De service stelt ontwikkelaars in staat om een routebeschrijving te berekenen over een aantal reismodi, zoals auto, vrachtwagen, fiets of wandelen. De dienst houdt ook rekening met ingangen zoals verkeersomstandigheden, gewichtsbeperkingen of vervoer van gevaarlijke stoffen.

![Voorbeeld van een kaart van de routeservice](media/about-azure-maps/intro_route.png)

De Route-service biedt een voorbeeld van geavanceerde functies, zoals: 

* Batchverwerking van meerdere routeaanvragen.
* Matrices van reistijd en afstand tussen een reeks oorsprong en bestemmingen.
* Het vinden van routes of afstanden die gebruikers kunnen reizen op basis van tijd of brandstofvereisten. 

Lees de [route-API-documentatie](https://docs.microsoft.com/rest/api/maps/route)voor meer informatie over de routebeheer.

### <a name="search-service"></a>Zoekservice

Met de zoekservice kunnen ontwikkelaars zoeken naar adressen, plaatsen, bedrijfsvermeldingen op naam of rubriek en andere geografische informatie. De zoekservice kan [geocodeadressen omkeren](https://en.wikipedia.org/wiki/Reverse_geocoding) en straten doorkruisen op basis van breedte- en lengtegraden.

![Voorbeeld van een zoekopdracht op een kaart](media/about-azure-maps/intro_search.png)

De Zoek-service biedt ook geavanceerde functies zoals:

* Zoek langs een route.
* Zoek in een groter gebied.
* Batch een groep zoekverzoeken.
* Zoek naar groter gebied in plaats van een locatiepunt. 

Er zijn momenteel preview-versies beschikbaar van API’s voor zoeken in batches en zoeken naar gebieden. Lees de documentatie van de [Search API](https://docs.microsoft.com/rest/api/maps/search)voor meer informatie over de zoekmogelijkheden.

### <a name="spatial-operations-service"></a>Dienst Ruimtelijke Bewerkingen

De Azure Maps Spatial Operations-service neemt locatiegegevens op. Het analyseert snel locatiegegevens om klanten te informeren over lopende gebeurtenissen in tijd en ruimte. Het maakt bijna real-time analyse en voorspellende modellering van gebeurtenissen mogelijk. 

De dienst stelt klanten in staat om hun locatie-intelligentie te verbeteren met een bibliotheek van gemeenschappelijke georuimtelijke wiskundige berekeningen. Veelvoorkomende berekeningen omvatten het dichtstbijzijnde punt, grote cirkelafstand en buffers. Lees de Documentatie Van de [API voor ruimtelijke bewerkingen](https://docs.microsoft.com/rest/api/maps/spatial)voor meer informatie over de service en de verschillende functies.

### <a name="time-zone-service"></a>Time Zone Service

Met de tijdzoneservice u actuele, historische en toekomstige tijdzone-informatie opvragen. U breedte- en lengteparen of een [IANA-id](https://www.iana.org/)gebruiken. De Time Zone-service maakt het ook mogelijk om:

* Microsoft Windows-tijdzone-id's converteren naar IANA-tijdzones.
* Een tijdzone-verschuiving ophalen naar UTC.
* De huidige tijd in een gekozen tijdzone krijgen. 

Een typisch JSON-antwoord voor een query naar de tijdzoneservice lijkt op het volgende voorbeeld:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Lees de [API-documentatie time zone voor](https://docs.microsoft.com/rest/api/maps/timezone)meer informatie over deze service.

### <a name="traffic-service"></a>Verkeersservice

De Traffic-service is een reeks webservices die ontwikkelaars kunnen gebruiken voor web- of mobiele toepassingen waarvoor verkeersinformatie vereist is. De service omvat twee gegevenstypen:

* Verkeersstroom: Real-time waargenomen snelheden en reistijden voor alle belangrijke wegen in het netwerk.
* Verkeersincidenten: Een up-to-date overzicht van files en incidenten rond het wegennet.

![Voorbeeld van een kaart met verkeersinformatie](media/about-azure-maps/intro_traffic.png)

Zie de documentatie [van](https://docs.microsoft.com/rest/api/maps/traffic)de Traffic API voor meer informatie .

### <a name="ip-to-location-service"></a>IP-naar-locatieservice

Gebruik de service IP-to-locatie om een voorbeeld te bekijken van de opgehaalde landcode met twee letters voor een IP-adres. Deze service kan u helpen de gebruikerservaring te verbeteren door aangepaste toepassingsinhoud aan te bieden op basis van geografische locatie.

Lees de [API-documentatie](https://docs.microsoft.com/rest/api/maps/geolocation)voor Geolocatie azure Maps voor meer informatie over de IP-naar-locatieservice in de REST-API.

## <a name="programming-model"></a>Programmeermodel

Azure Maps is gebouwd voor mobiliteit en kan u helpen bij het ontwikkelen van cross-platform applicaties. Het maakt gebruik van een programmeermodel dat taal agnostisch en ondersteunt JSON output via [REST API's](https://docs.microsoft.com/rest/api/maps/).

Azure Maps biedt ook een handig [JavaScript-kaartbeheer](https://docs.microsoft.com/javascript/api/azure-maps-control) met een eenvoudig programmeermodel. De ontwikkeling is snel en eenvoudig voor zowel web- als mobiele toepassingen.

## <a name="usage"></a>Gebruik

Toegang tot de Azure Maps-services is een kwestie van naar de [Azure-portal](https://portal.azure.com) gaan en een Azure Maps-account maken.

Azure Maps maakt gebruik van een verificatieschema op basis van sleutels. Uw account wordt geleverd met twee sleutels die al voor u zijn gegenereerd, gebruik beide sleutels. Begin met het integreren van deze locatiemogelijkheden in uw toepassing en vraag aanvragen in bij de Azure Maps-services.

Opmerking - Azure Maps deelt door de klant verstrekte adres-/locatiequery's ('Query's') met tomTom van derden voor het toewijzen van functionaliteitsdoeleinden. Query's worden niet gekoppeld aan een klant of eindgebruiker wanneer ze worden gedeeld met TomTom en kunnen niet worden gebruikt om personen te identificeren. Microsoft is momenteel bezig met het toevoegen van TomTom aan de Lijst met onderaannemers voor online services. Houd er rekening mee dat de mobiliteits- en weerservices, waaronder integratie met Moovit en AccuWeather, momenteel in [PREVIEW staan.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="supported-regions"></a>Ondersteunde regio’s

De Azure Maps API's zijn momenteel beschikbaar in alle landen en regio's, behalve deze:

* China
* Zuid-Korea

Controleer of de locatie van uw huidige IP-adres zich in een ondersteund land bevindt.

## <a name="next-steps"></a>Volgende stappen

Probeer een voorbeeld-app met Azure Maps:

> [!div class="nextstepaction"]
> [Snelstart: een web-app maken](quick-demo-map-app.md)

Blijf op de hoogte van Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps-blog](https://azure.microsoft.com/blog/topics/azure-maps/)
