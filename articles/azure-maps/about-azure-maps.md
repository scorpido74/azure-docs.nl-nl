---
title: Overzicht | Microsoft Azure Maps
description: Meer informatie over services en mogelijkheden in Microsoft Azure Maps en hoe u deze kunt gebruiken in uw toepassingen.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, references_regions
ms.openlocfilehash: 4b53c4d1a26d8e4b8d784087dcb9b9feba9c7ded
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191757"
---
# <a name="what-is-azure-maps"></a>Wat is Azure Maps?

Azure Maps is een verzameling georuimtelijke services die nieuwe kaartgegevens gebruiken om webtoepassingen en mobiele toepassingen te voorzien van een nauwkeurige geografische context. Azure Maps biedt:

* REST-API's voor het weergeven van kaarten in meerdere stijlen en satellietbeelden.''
* Search-services om adressen, plaatsen en interessante zaken over de hele wereld te vinden.
* Verschillende routeringsopties; zoals point-to-point, multipoint, multipoint-optimalisatie, isochrone, bedrijfsvoertuigen, verkeer dat wordt beïnvloed en matrixroutering.
* Weergave van verkeersstroom en incidenten, voor toepassingen waarvoor verkeersgegevens nodig zijn.
* Mobility-service om openbaar vervoerlogistiek aan te vragen, routes in realtime te plannen en informatie aan te vragen voor alternatieve transportmethoden.
* Time Zone en Geolocation-services en een locatie naar tijdzones converteren.
* Geofencing-service en toewijzing van gegevensopslag, met locatiegegevens die worden gehost in Azure. 
* Locatie-informatie via georuimtelijke analyse. 

Daarnaast zijn Azure Maps-services beschikbaar via de Web-SDK of de Android-SDK. Met deze hulpprogramma's kunnen ontwikkelaars snel oplossingen ontwikkelen en schalen waarmee locatiegegevens worden geïntegreerd in Azure-oplossingen. 

U kunt zich aanmelden voor een gratis [Azure Maps-account](https://azure.microsoft.com/services/azure-maps/) en begin met ontwikkelen.

In de volgende video wordt Azure Maps in detail uitgelegd:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Kaartbesturingselementen

### <a name="web-sdk"></a>Web SDK

Met de Azure Maps Web-SDK kunt u interactieve kaarten aanpassen met uw eigen inhoud en beelden. U kunt deze interactieve kaart gebruiken voor uw web- of mobiele toepassingen. Het besturingselement maakt gebruik van WebGL, zodat u grote gegevenssets kunt weergeven met hoge prestaties. Ontwikkel met de SDK met behulp van JavaScript of TypeScript.

![Voorbeeldtoewijzing van een verandering in de populatie](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Gebruik de Azure Maps Android-SDK om toepassingen voor mobiele toewijzing te maken. 

![Voorbeelden van kaarten op een mobiel apparaat](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Services in Azure Maps

Azure Maps bestaat uit de volgende negen services die uw Azure-toepassingen van geografische context kunnen voorzien.

### <a name="data-service"></a>Gegevensservice

Gegevens zijn van cruciaal belang voor kaarten. Gebruik gegevensservice om georuimtelijke gegevens te uploaden en op te slaan voor gebruik met ruimtelijke bewerkingen of om installatiekopieën samen te stellen.  Door klantgegevens dichter bij de Azure Maps-service te brengen, wordt de latentie verminderd, de productiviteit verhoogd en nieuwe scenario's gemaakt in uw toepassingen. Zie de [documentatie van de Data Service-API](https://docs.microsoft.com/rest/api/maps/data) voor meer informatie over deze service.

### <a name="mobility-service"></a>Mobility-service

Met de Azure Maps Mobility-service kunt u in realtime reizen plannen. Het retourneert de best mogelijke route-opties en biedt verschillende soorten reismodi. In het gebied van metro (stad) kunnen deze modi wandel-, fiets- en openbaar vervoerroutes bevatten. U kunt een doorreistraject, lijngeometrie, lijsten met haltes, geplande aankomsten, realtime aankomsten en servicewaarschuwingen aanvragen.

Met de service kunt u ook zoeken naar specifieke objecttypen rond een locatie. Gebruikers kunnen zoeken naar gedeelde fietsen, scooters of auto's rond een locatie. Gebruikers kunnen het aantal beschikbare fietsen in de dichtstbijzijnde garage aanvragen en zoeken naar beschikbare voertuigen voor het delen van ritten. En gebruikers kunnen informatie vinden, zoals de toekomstige beschikbaarheid van de voertuigen en het huidige brandstofniveau.

Zie de [Mobility API-documentatie](https://docs.microsoft.com/rest/api/maps/mobility) voor meer informatie over de service.

### <a name="render-service"></a>Service voor rendering

De Render-service helpt ontwikkelaars bij het maken van webtoepassingen en mobiele toepassingen voor het maken van functies voor kaarten. De service maakt gebruik van een raster met grafische afbeeldingen van hoge kwaliteit, beschikbaar in 19 zoomniveaus, of volledig aanpasbare kaartafbeeldingen in een vectorindeling.

![Voorbeeld van een kaart uit de Render-service](media/about-azure-maps/intro_map.png)

De Render Service biedt nu preview-API's die ontwikkelaars met satellietbeelden laten werken. Lees de pagina [Render-API-documentatie](https://docs.microsoft.com/rest/api/maps/render) voor meer informatie.

### <a name="route-service"></a>Routeservice

De Route Service bevat robuuste geometrische berekeningen voor de infrastructuur in de echte wereld en routebeschrijvingen voor meerdere manieren van reizen. Met de service kunnen ontwikkelaars richtingen berekenen voor een aantal manieren van reizen, zoals met de auto, vrachtwagen, fiets of wandelen. De service houdt ook rekening worden met invoerwaarden voor verkeersomstandigheden, gewichtsbeperkingen of transport van gevaarlijke stoffen.

![Voorbeeld van een kaart uit de Route-service](media/about-azure-maps/intro_route.png)

De Route-service biedt een preview van geavanceerde functies, zoals: 

* Batchverwerking van meerdere route-aanvragen.
* De matrices van reistijd en afstand tussen een reeks aanvangspunten en bestemmingen.
* Routes of afstanden zoeken die gebruikers kunnen reizen op basis van tijd- of brandstofvereisten. 

Lees de pagina [Routering-API-documentatie](https://docs.microsoft.com/rest/api/maps/route) voor meer informatie over de routeringsmogelijkheden.

### <a name="search-service"></a>Zoekservice

De Search-service helpt ontwikkelaars bij het zoeken naar adressen, plaatsen, bedrijfsvermeldingen op naam of categorie, en andere geografische informatie. Met de Search-service kan [de geocode worden omgekeerd](https://en.wikipedia.org/wiki/Reverse_geocoding) voor adressen en dwarsstraten op basis van breedte- en lengtegraden.

![Voorbeeld van een zoekopdracht op een kaart](media/about-azure-maps/intro_search.png)

De Search-service biedt ook geavanceerde functies zoals:

* Zoeken langs een route.
* Zoeken binnen een breder gebied.
* Batch een groep met zoekopdrachten.
* Zoeken naar een groter gebied in plaats van een locatiepunt. 

Er zijn momenteel preview-versies beschikbaar van API’s voor zoeken in batches en zoeken naar gebieden. Lees de pagina [Zoek-API-documentatie](https://docs.microsoft.com/rest/api/maps/search) voor meer informatie over de zoekmogelijkheden.

### <a name="spatial-operations-service"></a>Spatial Operations-service

De Spatial Operations-service van Azure Maps heeft locatiegegevens. De locatiegegevens worden snel geanalyseerd om klanten te informeren over lopende gebeurtenissen die in de tijd en ruimte plaatsvinden. De service maakt bijna realtime analyse en voorspellende modellering van gebeurtenissen mogelijk. 

Met de service kunnen klanten hun locatiegegevens verbeteren met een bibliotheek met algemene georuimtelijke wiskundige berekeningen. Veelvoorkomende berekeningen zijn onder andere het dichtstbijzijnde punt, orthodromische afstand en buffers. Lees voor meer informatie over de service en de verschillende functies de [Spatial Operations-API-documentatie](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Time Zone Service

Met de Time Zone-service kunt u de huidige, historische en toekomstige tijdzone-informatie opvragen. U kunt gebruikmaken van de breedte- en lengtegraad-paren of een [IANA-id](https://www.iana.org/). De Time Zone-service biedt ook het volgende:

* De tijdzone-id's van Microsoft Windows converteren naar IANA-tijdzones.
* Een tijdzone-offset wordt opgehaald naar UTC.
* De huidige tijd in een gekozen tijdzone ophalen. 

Een typisch JSON-antwoord voor een query in de Time Zone-service ziet er als volgt uit:

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

Lees de [Time Zone-documentatie](https://docs.microsoft.com/rest/api/maps/timezone) voor meer informatie over deze service.

### <a name="traffic-service"></a>Verkeersservice

De Traffic-service is een suite van webservices die ontwikkelaars kunnen gebruiken voor web- of mobiele toepassingen waarvoor verkeersgegevens nodig zijn. De service omvat twee gegevenstypen:

* Verkeersstroom: Waargenomen snelheden en reistijden in realtime voor alle belangrijke wegen in het netwerk.
* Verkeersincidenten: Een up-to-date weergave van files en incidenten rond het wegnetwerk.

![Voorbeeld van een kaart met verkeersgegevens](media/about-azure-maps/intro_traffic.png)

Zie de [documentatie van de Traffic-API](https://docs.microsoft.com/rest/api/maps/traffic) voor meer informatie.

### <a name="ip-to-location-service"></a>IP naar Location-service

Gebruik de IP van de Location-service om een voorbeeld te zien van het opgehaalde tweeletterige land-/regionummer voor een IP-adres. Deze service kan u helpen om de gebruikerservaring te verbeteren door een aangepaste toepassingsinhoud op basis van geografische locatie te bieden.

Lees de [Azure Maps Geolocation-API-documentatie](https://docs.microsoft.com/rest/api/maps/geolocation) voor meer informatie over de IP naar Location-service in de REST API.

## <a name="programming-model"></a>Programmeermodel

Azure Maps is gebouwd voor mobiliteit en kan u helpen platformoverschrijdende toepassingen te ontwikkelen. Het maakt gebruik van een programmeermodel dat taalonafhankelijk is en dat JSON-uitvoer ondersteunt door middel van [REST API's](https://docs.microsoft.com/rest/api/maps/).

Azure Maps biedt ook een handig [kaartbesturingselement van JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) met een eenvoudig programmeermodel. De ontwikkeling is snel en eenvoudig voor zowel web- als mobiele toepassingen.

## <a name="usage"></a>Gebruik

Voor toegang tot de services van Azure Maps gaat u naar de [Azure-portal](https://portal.azure.com) en maakt u een Azure Maps-account.

Azure Maps maakt gebruik van een verificatieschema op basis van sleutels. Uw account wordt geleverd met twee sleutels die al voor u zijn gegenereerd, gebruik een van beide. U kunt deze locatiemogelijkheden direct integreren in uw toepassing en aanvragen doen bij de Azure Maps-services.

Opmerking - Azure Maps deelt door klanten verstrekte adressen/locatie-query's ("Query's") met derde partij TomTom voor het toewijzen van functionaliteit. Query's worden niet aan een klant of eindgebruiker gekoppeld wanneer ze worden gedeeld met TomTom en kunnen niet worden gebruikt om individuen te identificeren. Microsoft is momenteel bezig met het toevoegen van TomTom aan de lijst met toeleveranciers van online services. Houd er rekening mee dat de Mobility- en Weather-services die integratie met Moovit en AccuWeather bevatten, momenteel in [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) staan.

## <a name="supported-regions"></a>Ondersteunde regio’s

De Azure Maps-API’s zijn momenteel wereldwijd beschikbaar, met uitzondering van de volgende landen/regio's:

* China
* Zuid-Korea

Verifieer of de locatie van uw IP-adres in een ondersteund land/regio is.

## <a name="next-steps"></a>Volgende stappen

Probeer een sample app uit die Azure Maps weergeeft:

> [!div class="nextstepaction"]
> [Snelstart: Een webtoepassing maken](quick-demo-map-app.md)

Houd Azure Maps up-to-date: 

> [!div class="nextstepaction"]
> [Azure Maps-blog](https://azure.microsoft.com/blog/topics/azure-maps/)
