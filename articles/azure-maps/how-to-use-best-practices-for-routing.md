---
title: Aanbevolen procedures voor Azure Maps-routeservice | Microsoft Azure Maps
description: Meer informatie over het efficiënt routeren met Routeservice vanuit Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335407"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Aanbevolen procedures voor Azure Maps-routeservice

De Routeroutebeschrijving en Routematrix-API's in Azure [Maps-routeservice](https://docs.microsoft.com/rest/api/maps/route) kunnen worden gebruikt om de geschatte aankomsttijden (ETA's) voor elke aangevraagde route te berekenen. Route-API's houden rekening met factoren zoals realtime verkeersinformatie en historische verkeersgegevens, zoals de typische wegsnelheden op de gevraagde dag van de week en het tijdstip van de dag. De API's sturen de kortste of snelste routes terug die beschikbaar zijn naar meerdere bestemmingen tegelijk of in geoptimaliseerde volgorde, op basis van tijd of afstand. Gebruikers kunnen ook gespecialiseerde routes en details aanvragen voor wandelaars, fietsers en bedrijfsvoertuigen zoals vrachtwagens. In dit artikel delen we de aanbevolen procedures om Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route)te bellen en leert u hoe:

* Kies tussen de API's routerichtingen en de Matrix Routing API
* Historische en voorspelde reistijden aanvragen op basis van realtime en historische verkeersgegevens
* Routegegevens aanvragen, zoals tijd en afstand, voor de hele route en elke etappe van de route
* Aanvraag route voor een bedrijfsvoertuig, zoals een vrachtwagen
* Verkeersinformatie aanvragen langs een route, zoals files en tolinformatie
* Een route aanvragen die bestaat uit een of meer haltes (waypoints)
* Optimaliseer een route van een of meer haltes om de beste volgorde te krijgen om elke stop te bezoeken (waypoint)
* Optimaliseer alternatieve routes met ondersteunende punten. Bied bijvoorbeeld alternatieve routes aan die langs een laadstation voor elektrische voertuigen gaan.
* De [routeservice gebruiken](https://docs.microsoft.com/rest/api/maps/route) met de Azure Maps Web SDK

## <a name="prerequisites"></a>Vereisten

Als u wilt bellen naar de Azure Maps-API's, hebt u een Azure Maps-account en een sleutel nodig. Zie [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) en een primaire sleutel [opvragen voor](quick-demo-map-app.md#get-the-primary-key-for-your-account)meer informatie. De primaire sleutel wordt ook wel de primaire abonnementssleutel of abonnementssleutel genoemd.

Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md)voor informatie over verificatie in Azure Maps. En voor meer informatie over de dekking van de routeservice, zie de [routedekking](routing-coverage.md).

In dit artikel wordt de [Postman-app](https://www.postman.com/downloads/) gebruikt om REST-oproepen te maken, maar u elke API-ontwikkelomgeving kiezen.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Kies tussen routerichtingen en matrixroutering

De API's routerichtingen geven instructies terug, inclusief de reistijd en de coördinaten voor een routepad. Met de Route Matrix API u de reistijd en afstanden berekenen voor een reeks routes die worden gedefinieerd op basis van herkomst en bestemmingslocaties. Voor elke bepaalde oorsprong berekent de Matrix API de kosten (reistijd en afstand) van routering van die oorsprong naar elke bepaalde bestemming. Met al deze API's u parameters opgeven zoals de gewenste vertrektijd, aankomsttijden en het voertuigtype, zoals een auto of vrachtwagen. Ze gebruiken allemaal realtime of voorspellende verkeersgegevens om de meest optimale routes terug te geven.

Overweeg omleidingsrouteroutebeschrijvingen te bellen als uw scenario:

* Vraag de kortste of snelste rijroute tussen twee of meer bekende locaties aan om precieze aankomsttijden voor uw bezorgvoertuigen te krijgen.
* Gedetailleerde routebegeleiding aanvragen, inclusief routegeometrie, om routes op de kaart te visualiseren
* Met een lijst van klantlocaties bereken je de kortst mogelijke route om elke klantlocatie te bezoeken en terug te keren naar de oorsprong. Dit scenario is algemeen bekend als de reizende verkoper probleem. U maximaal 150 waypoints (stops) in één aanvraag doorgeven.
* Verzend batches query's naar de Route Directions Batch API met slechts één API-aanroep.

Overweeg om Matrix Routing API aan te roepen als uw scenario:

* Bereken de reistijd of afstand tussen een set van oorsprong en bestemmingen. U hebt bijvoorbeeld 12 chauffeurs en u moet de dichtstbijzijnde beschikbare chauffeur vinden om de maaltijdbezorging bij het restaurant op te halen.
* Sorteer potentiële routes op hun werkelijke reisafstand of tijd. De Matrix API retourneert alleen reistijden en afstanden voor elke oorsprongs- en doelcombinatie.
* Clustergegevens op basis van reistijd of afstanden. Uw bedrijf heeft bijvoorbeeld 50 werknemers, vind alle medewerkers die binnen 20 minuten na uw kantoor wonen.

Hier volgt een vergelijking om enkele mogelijkheden van de routeroutebeschrijvingen en matrix-API's weer te geven:

| Azure Maps-API | Maximaal aantal query's in de aanvraag | Vermijd gebieden | Vrachtwagen- en elektrische voertuigroute | waypoints en Traveling Salesman optimalisatie | Ondersteunende punten |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Routeroutebeschrijving opvragen | 1 | | X | X | |
| Routeroutebeschrijvingen plaatsen | 1 | X | X | X | X |
| Batch routeroutebeschrijvingen plaatsen | 700 | | X | X | |
| Matrix voor postroutes | 700 | | X | | |

Zie onze zelfstudie over het routeren van elektrische voertuigen voor meer informatie over het [routeren van elektrische voertuigen met Azure Notebooks met Python.](tutorial-ev-routing.md)

## <a name="request-historic-and-real-time-data"></a>Historische en realtime gegevens opvragen

Standaard gaat de Route-service ervan uit dat de reismodus een auto is en dat de vertrektijd nu is. Het retourneert route op basis van real-time verkeersomstandigheden, tenzij een routeberekeningsaanvraag anders aangeeft. Vaste tijdsafhankelijke verkeersbeperkingen, zoals 'Links afslagen zijn niet toegestaan tussen 16:00 en 18:00 uur' worden vastgelegd en worden in aanmerking genomen door de routeringsengine. Wegafsluitingen, zoals wegwerkzaamheden, worden overwogen, tenzij u specifiek een route aanvraagt die het huidige live verkeer negeert. Als u het huidige `traffic` `false` verkeer wilt negeren, stelt u in uw API-aanvraag in.

De **routeberekeningsreisTimeInSeconds-waarde** omvat de vertraging als gevolg van het verkeer. Het wordt gegenereerd door gebruik te maken van de huidige en historische reistijdgegevens, wanneer de vertrektijd is ingesteld op nu. Als uw vertrektijd in de toekomst wordt ingesteld, geven de API's voorspelde reistijden terug op basis van historische gegevens.

Als u de parameter **computeTravelTimeFor=all** in uw aanvraag opneemt, bevat het overzichtselement in het antwoord de volgende extra velden, waaronder historische verkeersomstandigheden:

| Element | Beschrijving|
| :--- | :--- |
| noTrafficTravelTimeInSeconden | Geschatte reistijd berekend alsof er geen vertragingen op de route als gevolg van de verkeersomstandigheden, bijvoorbeeld als gevolg van congestie |
| historischTrafficTravelTimeInSeconden | Geschatte reistijd berekend aan de hand van tijdsafhankelijke historische verkeersgegevens |
| liveTrafficIncidentsTravelTimeInSeconds | Geschatte reistijd berekend aan de hand van realtime snelheidsgegevens |

In de volgende secties wordt uitgelegd hoe u naar de Route-API's bellen met behulp van de besproken parameters.

### <a name="sample-query"></a>Voorbeeldquery

In het eerste voorbeeld hieronder wordt de vertrektijd ingesteld op de toekomst, op het moment van schrijven.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Het antwoord bevat een overzichtselement, zoals hieronder. Omdat de vertrektijd is ingesteld op de toekomst, is de waarde **trafficDelayInSeconds** nul. De **waarde van travelTimeInSeconds** wordt berekend aan de hand van tijdsafhankelijke historische verkeersgegevens. Dus, in dit geval, de **travelTimeInSeconds** waarde is gelijk aan de **historischeTrafficTravelTimeInSeconds** waarde.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Voorbeeldquery

In het tweede voorbeeld hieronder hebben we een real-time routing-aanvraag, waar de vertrektijd nu is. Het is niet expliciet opgegeven in de URL omdat het de standaardwaarde is.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Het antwoord bevat een samenvatting zoals hieronder weergegeven. Vanwege congestie, de **trafficDelaysInSeconds** waarde is groter dan nul. Het is ook groter dan **historischTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Route- en beengegevens aanvragen

Standaard retourneert de routeservice een array met coördinaten. Het antwoord bevat de coördinaten die deel `points`uitmaken van het pad in een lijst met de naam . Routerespons omvat ook de afstand vanaf het begin van de route en de geschatte verstreken tijd. Deze waarden kunnen worden gebruikt om de gemiddelde snelheid voor de hele route te berekenen.

De volgende afbeelding `points` toont het element.

<center>

![puntenlijst](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Vouw `point` het element uit om de lijst met coördinaten voor het pad te bekijken:

<center>

![puntenlijst](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

De API's routerichtingen ondersteunen verschillende indelingen van instructies die kunnen worden gebruikt door de parameter **instructionsType** op te geven. Als u instructies wilt opmaken voor eenvoudige computerverwerking, gebruikt u **instructiesType=gecodeerd**. Gebruik **instructionsType=tagged** om instructies weer te geven als tekst voor de gebruiker. Ook kunnen instructies worden opgemaakt als tekst waar sommige elementen van de instructies zijn gemarkeerd, en de instructie wordt gepresenteerd met speciale opmaak. Zie voor meer informatie de [lijst met ondersteunde instructietypen](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

Wanneer instructies worden gevraagd, retourneert `guidance`het antwoord een nieuw element met de naam . Het `guidance` element bevat twee stukken informatie: turn-by-turn richtingen en samengevatinstructies.

<center>

![Type Instructies](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

Het `instructions` element houdt afslag-voor-afslag richtingen voor `instructionGroups` de reis, en de heeft samengevat instructies. Elke instructiesamenvatting heeft betrekking op een segment van de reis dat meerdere wegen kan bestrijken. De API's kunnen details retourneren voor delen van een route. zoals het coördinatenbereik van een file of de huidige snelheid van het verkeer.

<center>

![Afslag-instructies](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Samengevatte instructies](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Een route aanvragen voor een bedrijfsvoertuig

Azure Maps Routing API's ondersteunen de routering van bedrijfsvoertuigen, met betrekking tot de routering van commerciële vrachtwagens. De API's houden rekening met bepaalde limieten. Zoals de hoogte en het gewicht van het voertuig en als het voertuig gevaarlijke lading vervoert. Als een voertuig bijvoorbeeld ontvlambaar is, vermijdt de routingmotor bepaalde tunnels die zich in de buurt van woonwijken bevinden.

### <a name="sample-query"></a>Voorbeeldquery

In de onderstaande voorbeeldaanvraag wordt een route voor een bedrijfswagen gevraagd. De truck vervoert gevaarlijk afvalmateriaal van klasse 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

De Route API retourneert richtingen die passen bij de afmetingen van de truck en het gevaarlijke afval. U de route-instructies `guidance` lezen door het element uit te breiden.

<center>

![Vrachtwagen met klasse 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Voorbeeldquery

Als u de Amerikaanse Hazmat-klasse wijzigt, uit de bovenstaande query, wordt een andere route gevonden om aan deze wijziging tegemoet te komen.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

De respons hieronder is voor een vrachtwagen met een gevaarlijk materiaal van klasse 9, dat minder gevaarlijk is dan een gevaarlijk materiaal van klasse 1. Wanneer u `guidance` het element uitvouwt om de aanwijzingen te lezen, zult u merken dat de aanwijzingen niet hetzelfde zijn. Er zijn meer route-instructies voor de vrachtwagen die klasse 1 gevaarlijk materiaal vervoert.

<center>

![Vrachtwagen met klasse 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Verkeersinformatie aanvragen langs een route

Met de AZURE Maps Route Direction API's kunnen ontwikkelaars details `sectionType` voor elk sectietype opvragen door de parameter in de aanvraag op te nemen. U bijvoorbeeld de snelheidsinformatie voor elk filesegment opvragen. Raadpleeg de [lijst met waarden voor de sectieType-toets](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) voor meer informatie over de verschillende details die u opvragen.

### <a name="sample-query"></a>Voorbeeldquery

In de volgende `sectionType` `traffic`query wordt de query ingesteld op . Het vraagt om de secties die verkeersinformatie bevatten van Seattle naar San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Het antwoord bevat de secties die geschikt zijn voor verkeer langs de opgegeven coördinaten.

<center>

![verkeerssecties](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Deze optie kan worden gebruikt om de secties te kleuren bij het renderen van de kaart, zoals in de afbeelding hieronder: 

<center>

![verkeerssecties](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Een route met meerdere stop's berekenen en optimaliseren

Azure Maps biedt momenteel twee vormen van routeoptimalisatie:

* Optimalisaties op basis van het gevraagde routetype, zonder de volgorde van waypoints te wijzigen. De ondersteunde [routetypen](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) vindt u hier

* Reizende verkoper optimalisatie, die de volgorde van de waypoints verandert om de beste volgorde te verkrijgen om elke stop te bezoeken

Voor multi-stop routing kunnen maximaal 150 waypoints worden opgegeven in één routeaanvraag. De begin- en eindcoördinatenlocaties kunnen hetzelfde zijn, zoals het geval zou zijn bij een rondreis. Maar u moet ten minste één extra waypoint bieden om de routeberekening te maken. Waypoints kunnen worden toegevoegd aan de query tussen de oorsprong en de bestemming coördinaten.

Als u de beste volgorde wilt optimaliseren om de gegeven waypoints te bezoeken, moet u **computeBestOrder=true**opgeven. Dit scenario is ook bekend als de reizende verkoper optimalisatie probleem.

### <a name="sample-query"></a>Voorbeeldquery

In de volgende query wordt het pad `computeBestOrder` aangevraagd `false`voor zes waypoints, waarbij de parameter is ingesteld op . Het is ook de standaardwaarde voor de `computeBestOrder` parameter.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

De reactie beschrijft de lengte van het pad op 140.851 meter en dat het 9.991 seconden zou duren om dat pad af te leggen.

<center>

![Niet-geoptimaliseerde respons](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

De onderstaande afbeelding illustreert het pad dat uit deze query voortvloeit. Dit pad is een mogelijke route. Het is niet het optimale pad op basis van tijd of afstand.

<center>

![Niet-geoptimaliseerde afbeelding](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Deze routewaypoint order is: 0, 1, 2, 3, 4, 5 en 6.

### <a name="sample-query"></a>Voorbeeldquery

In de volgende query wordt het pad aangevraagd voor dezelfde zes waypoints, zoals in het bovenstaande voorbeeld. Deze keer, `computeBestOrder` de `true` parameter ingesteld op (de reizende verkoper optimalisatie).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

De reactie beschrijft de lengte van het pad op 91.814 meter, en dat het 7.797 seconden zou duren om dat pad te bewandelen. De reisafstand en de reistijd zijn hier beide lager omdat de API de geoptimaliseerde route heeft geretourneerd.

<center>

![Niet-geoptimaliseerde respons](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

De onderstaande afbeelding illustreert het pad dat uit deze query voortvloeit.

<center>

![Niet-geoptimaliseerde afbeelding](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

De optimale route heeft de volgende waypointvolgorde: 0, 5, 1, 2, 4, 3 en 6.

>[!TIP]
> De geoptimaliseerde waypointordergegevens van de routeringsservice bieden een set indexen. Deze sluiten de oorsprong en de bestemmingsindexen uit. U moet deze waarden met 1 verhogen om rekening te houden met de oorsprong. Voeg vervolgens uw bestemming toe aan het einde om de volledige geordende waypoint-lijst te krijgen.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Alternatieve routes berekenen en voorinnemen met ondersteunende punten

U situaties hebben waarin u een route wilt reconstrueren om nul of meer alternatieve routes voor een referentieroute te berekenen. U klanten bijvoorbeeld alternatieve routes laten zien die langs uw winkel gaan. In dit geval moet u een locatie bias met behulp van ondersteunende punten. Hier zijn de stappen om een locatie te vernemen:

1. Bereken een route als is en haal het pad uit het routeantwoord
2. Gebruik het routepad om de gewenste locaties langs of in de buurt van het routepad te vinden. U bijvoorbeeld azure maps [Point of Interest API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) gebruiken of uw eigen gegevens in uw database opvragen.  
3. Bestel de locaties op basis van de afstand vanaf het begin van de route
4. Voeg deze locaties toe als ondersteuningspunten in een nieuwe routeaanvraag aan de [API Routeroutebeschrijvingen](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Zie de [API-documentatie voor routerichtingen.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) 

Wanneer u de [API Voor routerichtingen plaatsen](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)aanroept, u de minimale afwijkingstijd of de afstandsbeperkingen instellen, samen met de ondersteuningspunten. Gebruik deze parameters als u alternatieve routes wilt aanbieden, maar u wilt ook de reistijd beperken. Wanneer deze beperkingen worden gebruikt, volgen de alternatieve routes de referentieroute vanaf het oorsprongspunt voor de gegeven tijd of afstand. Met andere woorden, de andere routes wijken af van de referentieroute per bepaalde beperkingen.

De afbeelding hieronder is een voorbeeld van het renderen van alternatieve routes met opgegeven afwijkingslimieten voor de tijd en de afstand.

<center>

![Alternatieve routes](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>De routeringsservice gebruiken in een web-app

De Azure Maps Web SDK biedt een [Servicemodule.](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest) Deze module is een helperbibliotheek waarmee u de Azure Maps REST API's eenvoudig gebruiken in web- of Node.js-toepassingen, met JavaScript of TypeScript. De Servicemodule kan worden gebruikt om de geretourneerde routes op de kaart weer te geven. De module bepaalt automatisch welke API moet worden gebruikt met GET- en POST-aanvragen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

> [!div class="nextstepaction"]
> [Azure Maps-routeservice](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [De servicemodule gebruiken](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Route weergeven op de kaart](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [NPM-pakket azure maps](https://www.npmjs.com/package/azure-maps-rest  )
