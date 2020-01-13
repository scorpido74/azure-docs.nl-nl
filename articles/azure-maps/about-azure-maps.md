---
title: Overzicht | Microsoft Azure kaarten
description: In dit artikel vindt u meer informatie over services en mogelijkheden in Microsoft Azure Maps en hoe u deze kunt gebruiken in uw toepassingen.
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 353850cacb06dcc8a0db1b957114db314d7002a3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911838"
---
# <a name="what-is-azure-maps"></a>Wat is Azure Maps?

Azure Maps is een verzameling georuimtelijke services waarvoor nieuwe kaartgegevens worden gebruikt, waarmee u webtoepassingen en mobiele toepassingen kunt voorzien van een nauwkeurige geografische context. Azure Maps biedt:

* REST-Api's voor het weer geven van kaarten in meerdere stijlen en satelliet installatie kopieën.
* Zoekt naar adressen, plaatsen en interessante onderwerpen over de hele wereld.
* Route Point-to-Point, multi point optimaliseren, multi point-optimalisatie, isochrone, bedrijfs voertuigen, verkeer dat wordt beïnvloed en matrix routering; verkeers stroom en incidenten weer geven.
* Mobiliteits Services voor het aanvragen van open bare door Voer en alternatieve transport methoden (zoals Bike share, scooter share, auto share) en planningen van routes in realtime. 
* Het instellen van de gebruikers locatie via geolocatie, en het converteren van de locatie naar tijd zones. 
* Services voor het geoomheiningen en toewijzen van gegevens opslag, met locatie gegevens die worden gehost in Azure. 
* Locatie informatie via georuimtelijke analyse. 

Naast REST Api's zijn Azure Maps services beschikbaar via de Web-SDK of de Android-SDK. Met deze hulpprogram ma's kunnen ontwikkel aars snel oplossingen ontwikkelen en schalen waarmee locatie gegevens in azure-oplossingen worden geïntegreerd. 

U kunt zich registreren voor een gratis [Azure Maps account](https://azure.microsoft.com/services/azure-maps/) en beginnen met het ontwikkelen.

In de volgende video wordt Azure Maps in detail uitgelegd:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kaartbesturingselementen

### <a name="web-sdk"></a>Web SDK

Met de Azure Maps Web-SDK kunt u interactieve kaarten met uw eigen inhoud en beelden aanpassen voor weer gave in uw web-of mobiele toepassingen. Dit besturings element maakt gebruik van WebGL, zodat u grote gegevens sets met hoge prestaties kunt weer geven. Ontwikkel met de SDK met behulp van Java script of type script.

![Voorbeeld toewijzing van een gewijzigde populatie](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Gebruik de Azure Maps Android-SDK om toepassingen voor mobiele toewijzing te maken. 

![Kaart voorbeelden op een mobiel apparaat](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Services in Azure Maps

Azure Maps bestaat uit de volgende negen services die geografische context kunnen bieden voor uw Azure-toepassingen.

### <a name="data-service"></a>Gegevensservice

Gegevens zijn essentieel voor kaarten. Gebruik gegevens service om georuimtelijke gegevens te uploaden en op te slaan voor gebruik met ruimtelijke bewerkingen of installatie kopieën samen te stellen.  Het nemen van klant gegevens dichter bij de Azure Maps service vermindert de latentie, verhoogt de productiviteit en maakt nieuwe scenario's in uw toepassingen. Zie de [Data Service API-documentatie](https://docs.microsoft.com/rest/api/maps/data)voor meer informatie over deze service.

### <a name="mobility-service"></a>Mobility-service

Met de Azure Maps Mobility-service kunt u in realtime een eenmalige excursie plannen. Het retourneert de best mogelijke route opties en biedt diverse reis modi. In het gebied van metro (stad) kunnen deze modi wandel, fietsen en open bare door Voer bevatten. Ontwikkel aars kunnen gegevens over transit trajecten, zoals geometrie, lijsten van stop bewerkingen, geplande en real-time aankomsten en service waarschuwingen aanvragen.

Met deze service kunt u ook zoeken naar specifieke object typen, zoals gedeelde fietsen, scooters of auto's rond een locatie. Gebruikers kunnen vragen hoeveel beschik bare gedeelde fietsen in de dichtstbijzijnde Dock blijven staan. Ze kunnen beschik bare auto-deel voertuigen doorzoeken en informatie vinden, zoals de toekomst Beschik baarheid en het huidige brandstof niveau.

Zie de [documentatie van Mobility API](https://docs.microsoft.com/rest/api/maps/mobility)voor meer informatie over de service.

### <a name="render-service"></a>Service voor rendering

De weergave service helpt ontwikkel aars bij het maken van webtoepassingen en mobiele toepassingen om ze te koppelen. De service maakt gebruik van een raster met grafische afbeeldingen van hoge kwaliteit, beschikbaar in 19 zoomniveaus, of volledig aanpasbare kaartafbeeldingen in een vectorindeling.

![Voor beeld van een kaart in de weergave service](media/about-azure-maps/Introduction_Map.png)

De Render Service biedt nu preview-API's die ontwikkelaars met satellietbeelden laten werken. Lees de documentatie van de [render-API](https://docs.microsoft.com/rest/api/maps/render)voor meer informatie.

### <a name="route-service"></a>Routeservice

De Route Service bevat robuuste geometrische berekeningen voor de infrastructuur in de echte wereld en routebeschrijvingen voor meerdere manieren van reizen. Met de service kunnen ontwikkelaars richtingen berekenen voor een aantal manieren van reizen, zoals met de auto, vrachtwagen, fiets of wandelen. In de service kan ook rekening worden gehouden met invoerwaarden voor verkeersomstandigheden, gewichtsbeperkingen of transport van gevaarlijke stoffen.

![Voor beeld van een kaart vanuit de route service](media/about-azure-maps/Introduction_Route.png)

De route service biedt een preview van geavanceerde functies, zoals: 

* Batch verwerking van meerdere route aanvragen.
* De matrices van reis tijd en afstand tussen een reeks oorsprongen en bestemmingen.
* Routes of afstanden zoeken die gebruikers kunnen reizen op basis van tijd-of brandstof vereisten. 

Lees de [route-API-documentatie](https://docs.microsoft.com/rest/api/maps/route)voor meer informatie over de routerings mogelijkheden.

### <a name="search-service"></a>Zoekservice

De zoek service helpt ontwikkel aars bij het zoeken naar adressen, plaatsen, bedrijfs vermeldingen op naam of categorie en andere geografische informatie. De zoek service kan [Geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) -adressen en Cross-Streets omkeren op basis van de breedte graad en lengte graad.

![Voor beeld van een zoek opdracht op een kaart](media/about-azure-maps/Introduction_Search.png)

De zoek service biedt ook geavanceerde functies zoals:

* Zoeken naar een route.
* Zoek binnen een breder gebied.
* Batch een groep met Zoek opdrachten.
* Zoeken naar een groter gebied in plaats van een locatie punt. 

Er zijn momenteel preview-versies beschikbaar van API’s voor zoeken in batches en zoeken naar gebieden. Lees de [documentatie van Search API](https://docs.microsoft.com/rest/api/maps/search)voor meer informatie over de zoek mogelijkheden.

### <a name="spatial-operations-service"></a>Ruimtelijke-bewerkingen service

De Azure Maps ruimtelijke bewerkings service neemt locatie-informatie en analyseert deze in de vlucht om klanten te informeren over lopende gebeurtenissen die in tijd en ruimte optreden. Het maakt bijna realtime analyse en voorspellende modellering van gebeurtenissen mogelijk. 

Met deze service kunnen klanten hun locatie gegevens uitbreiden met een bibliotheek met algemene georuimtelijke wiskundige berekeningen, waaronder het dichtstbijzijnde punt, een fantastische cirkel-afstand en buffers. Lees de [documentatie over ruimtelijk Operations API](https://docs.microsoft.com/rest/api/maps/spatial)voor meer informatie over de service en de verschillende functies.

### <a name="time-zone-service"></a>Time Zone Service

Met de time zone-service kunt u zoeken naar actuele, historische en toekomstige tijd zone-informatie met behulp van de Latitude/lengte graad-paren of een [IANA-id](https://www.iana.org/). De tijd zone-service biedt ook de volgende opties:

* De tijd zone-Id's van micro soft Windows converteren naar IANA-tijd zones.
* Een tijd zone-offset wordt opgehaald naar UTC.
* De huidige tijd in een tijd zone ophalen. 

Een typisch JSON-antwoord voor een query naar de tijd zone service ziet er als volgt uit:

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

Lees de [tijd zone API-documentatie](https://docs.microsoft.com/rest/api/maps/timezone)voor meer informatie over deze service.

### <a name="traffic-service"></a>Verkeersservice

De traffic-service is een reeks webservices die ontwikkel aars kunnen gebruiken voor het maken van webtoepassingen en mobiele toepassingen waarvoor verkeers gegevens nodig zijn. De service omvat twee gegevenstypen:

* Verkeers stroom: in realtime waargenomen snelheden en reis tijden voor alle belang rijke wegen in het netwerk.
* Verkeers incidenten: een up-to-date weer gave van storingen van verkeer en incidenten rond het wegnetwerk.

![Voor beeld van een kaart met verkeers gegevens](media/about-azure-maps/Introduction_Traffic.png)

Zie de [documentatie van Traffic API](https://docs.microsoft.com/rest/api/maps/traffic)voor meer informatie.

### <a name="ip-to-location-service"></a>IP-naar-locatie service

Gebruik de service voor de locatie van IP-adressen om een voor beeld te krijgen van de opgehaalde land code van twee letters voor een IP-adres. Deze service kan u helpen de gebruikers ervaring aan te passen en te verbeteren door aangepaste toepassings inhoud op te geven op basis van de geografische locatie.

Lees de [Azure Maps geolocatie API-documentatie](https://docs.microsoft.com/rest/api/maps/geolocation)voor meer informatie over de rest-api's voor de service IP to location.

## <a name="programming-model"></a>Programmeermodel

Azure Maps is gebouwd voor mobiliteit en kan helpen bij het ontwikkelen van toepassingen voor meerdere platforms. Er wordt gebruikgemaakt van een programmeer model dat taal neutraal is en die JSON-uitvoer ondersteunt via [rest-api's](https://docs.microsoft.com/rest/api/maps/).

Bovendien biedt Azure Maps een handig [kaartbesturingselement in JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) met een eenvoudig programmeermodel voor het snel en gemakkelijk ontwikkelen van webtoepassingen en mobiele toepassingen.

## <a name="usage"></a>Gebruik

Voor toegang tot de Azure Maps Services gaat u naar de [Azure Portal](https://portal.azure.com) en maakt u een Azure Maps-account.

Azure Maps maakt gebruik van een verificatieschema op basis van sleutels. Uw account wordt geleverd met twee sleutels die al voor u zijn gegenereerd. U kunt deze locatiemogelijkheden direct integreren in uw toepassing met behulp van een van de sleutels en een aanvraag aan de Azure Maps-service te maken.

## <a name="supported-regions"></a>Ondersteunde regio’s

De Azure Maps Api's zijn momenteel beschikbaar in alle landen/regio's, behalve de volgende:

* China
* Zuid-Korea

Verifieer of de locatie van uw IP-adres niet in een van de bovenstaande landen is, die niet worden ondersteund.

## <a name="next-steps"></a>Volgende stappen

Probeer een voor beeld-app met een demonstratie Azure Maps:

> [!div class="nextstepaction"]
> [Snelstartgids: een web-app maken](quick-demo-map-app.md)

Blijf op de hoogte van Azure Maps: 

> [!div class="nextstepaction"]
> [Azure Maps blog](https://azure.microsoft.com/blog/topics/azure-maps/)
