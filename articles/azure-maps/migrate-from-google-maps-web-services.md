---
title: 'Zelf studie: webservices migreren vanuit Google Maps | Microsoft Azure kaarten'
description: Het migreren van webservices van Google Maps naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: fac83a7a5137a50a26721da58395cc2e915f222d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086204"
---
# <a name="migrate-web-service-from-google-maps"></a>Webservice migreren vanuit Google Maps

Zowel Azure-als Google-kaarten bieden toegang tot ruimtelijke Api's via REST-webservices. De API-interfaces van deze platforms voeren vergelijk bare functionaliteit uit. Maar ze gebruiken echter verschillende naam conventies en antwoord objecten.

De tabel bevat de Azure Maps service-Api's, die een vergelijk bare functionaliteit hebben in de vermelde Google Maps service-Api's.

| Service-API voor Google Maps | API van Azure Maps-service                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Aanwijzing              | [Rond](https://docs.microsoft.com/rest/api/maps/route)                               |
| Afstands matrix         | [Route matrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodering               | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Locaties zoeken           | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Automatisch aanvullen plaatsen      | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statische toewijzing              | [Waardoor](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Tijdzone               | [Tijd zone](https://docs.microsoft.com/rest/api/maps/timezone)                        |

De volgende service-Api's zijn momenteel niet beschikbaar in Azure Maps:

- Uitbrei ding
- Geolocatie
- Plaats Details en plaats foto's. De telefoon nummers en de URL van de website zijn beschikbaar in de Azure Maps Search-API.
- Kaart-Url's
- Toegangs. Gegevens voor snelheids limieten zijn beschikbaar via de route ring en reverse geocoderings-Api's in Azure Maps.
- Statische straat weergave

Azure Maps heeft verschillende extra REST-webservices die van belang kunnen zijn:

- [Ruimtelijke bewerkingen](https://docs.microsoft.com/rest/api/maps/spatial): offload complexe ruimtelijke berekeningen en bewerkingen, zoals geoomheining, naar een service.
- [Verkeer](https://docs.microsoft.com/rest/api/maps/traffic): toegang tot realtime verkeers stromen en incident gegevens.

## <a name="geocoding-addresses"></a>Geocoderings adressen

Geocodering is het proces van het converteren van een adres naar een coördinaat. "1 micro soft Way, Redmond, WA" wordt bijvoorbeeld omgezet in lengte graad:-122,1298, Latitude: 47,64005. Vervolgens kunnen coördinaten worden gebruikt voor verschillende soorten doelen, zoals het plaatsen van een markeer stift op een kaart.

Azure Maps biedt verschillende methoden voor geocoderings adressen:

- [**Vrije-vorm adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Geef één adres teken reeks op en verwerk de aanvraag direct. "1 micro soft Way, Redmond, WA" is een voor beeld van een enkele teken reeks. Deze API wordt aanbevolen als u snel afzonderlijke adressen moet coderen.
- [**Gestructureerde adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): hier kunt u de onderdelen van één adres opgeven, zoals de straat naam, plaats, land en post code en de aanvraag onmiddellijk verwerken. Deze API wordt aanbevolen als u afzonderlijke adressen snel wilt coderen en de gegevens al zijn geparseerd in de afzonderlijke adres delen.
- [**Batch-adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): een aanvraag met maxi maal 10.000 adressen maken en deze gedurende een bepaalde tijd laten verwerken. Alle adressen worden parallel gecodeerd op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload. Dit wordt aanbevolen voor geocodering van grote gegevens sets.
- [**Fuzzy Search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): deze API combineert het adres Geocode ring met een belang rijke zoek opdracht. Deze API wordt gebruikt in een vrije teken reeks. Deze teken reeks kan een adres, plaats, oriëntatie punt, interesse of belang rijke categorie zijn. Deze API verwerkt de aanvraag bijna in real time. Deze API wordt aanbevolen voor toepassingen waar gebruikers naar adressen of punten van belang in hetzelfde tekstvak zoeken.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): een aanvraag maken met maxi maal 10.000 adressen, plaatsen, bezienswaardigheden of interesses en deze worden verwerkt gedurende een bepaalde periode. Alle gegevens worden parallel verwerkt op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` en `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-stad/plaats<br/>`municipalitySubdivision`: omgeving, sub/Super City<br/>`countrySubdivision`-staat of provincie<br/>`countrySecondarySubdivision` graafschap<br/>`countryTertiarySubdivision`-district<br/>`countryCode`-land code van twee letters |
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Een voor beeld van het gebruik van de zoek service wordt [hier](how-to-search-for-address.md)beschreven. Controleer de [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

> [!TIP]
> De vrije vorm-adres geocodering en fuzzy Zoek-Api's kunnen worden gebruikt in de modus automatisch volt ooien door `&amp;typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server op de hoogte gebracht van de invoer tekst waarschijnlijk gedeeltelijk en wordt de zoek opdracht in de voorspellende modus uitgevoerd.

## <a name="reverse-geocode-a-coordinate"></a>Geocode van een coördinaat omkeren

Omgekeerde geocodering is het proces van het converteren van geografische coördinaten naar een adres dat ongeveer gelijk is aan. Coördinaten met "lengte graad:-122,1298, Latitude: 47,64005" converteren naar "1 micro soft Way, Redmond, WA".

Azure Maps biedt verschillende methoden voor reverse geocodering:

- [**Omgekeerde geocodeer**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Geef een enkele geografische coördinaat op om het geschatte adres dat overeenkomt met deze coördinaat op te halen. De aanvraag wordt bijna in realtime verwerkt.
- [**Omgekeerde geocodeer**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): een enkele geografische coördinaat opgeven om informatie over de nabijgelegen straat op te halen en de aanvraag onmiddellijk te verwerken. U kunt bijvoorbeeld de volgende cross-straten, 1e Ave en Main St, ontvangen.
- [**Batch adres reverse geocodeer**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): een aanvraag met maxi maal 10.000 coördinaten maken en deze gedurende een bepaalde periode verwerken. Alle gegevens worden parallel verwerkt op de-server. Wanneer de aanvraag is voltooid, kunt u de volledige set met resultaten downloaden.

Deze tabel bevat kruis verwijzingen naar de Google Maps API-para meters met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter   | Vergelijk bare Azure Maps API-para meter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *N.v.t.*     |
| `result_type`               | `entityType`    |

Bekijk [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

De Azure Maps reverse geocoderings-API bevat enkele extra functies, die niet beschikbaar zijn in Google Maps. Deze functies kunnen nuttig zijn om te integreren met uw toepassing, terwijl u uw app migreert:

- Gegevens van snelheids limiet ophalen
- Informatie over het weggebruik ophalen: lokale weg, ARTerial, beperkte toegang, helling, enzovoort
- De zijde van de straat ophalen waarop een coördinaat zich bevindt

## <a name="search-for-points-of-interest"></a>Zoeken naar nuttige plaatsen

Belang rijke gegevens kunnen in Google Maps worden doorzocht met behulp van de locatie van de zoek-API. Deze API biedt drie verschillende manieren om te zoeken naar interessante onderwerpen:

- **Locatie van tekst zoeken:** Zoekt naar een belang rijke positie op basis van de naam, het adres of het telefoon nummer.
- In de **buurt zoeken**: zoekt naar geïnteresseerden die zich binnen een bepaalde afstand van een locatie bevinden.
- **Tekst zoeken:** Zoekt naar plaatsen met behulp van een vrije-tekst, die informatie over het belang en de locatie bevat. Bijvoorbeeld "pizza in New York" of "restaurants in de buurt van Main St".

Azure Maps biedt verschillende Zoek-Api's voor interessante onderwerpen:

- [**POI zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): zoeken naar punten van interesses op naam. Bijvoorbeeld ' Starbucks '.
- [**POI categorie zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): zoeken naar punten van belangen per categorie. Bijvoorbeeld ' restaurant '.
- In de [**buurt zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): zoekt naar geïnteresseerden die zich binnen een bepaalde afstand van een locatie bevinden.
- [**Fuzzy Search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): deze API combineert het adres Geocode ring met een belang rijke zoek opdracht. Deze API maakt deel uit van een vrije teken reeks die een adres, plaats, oriëntatie punt, interesse of belang rijke categorie kan zijn. De aanvraag wordt bijna in real time verwerkt. Deze API wordt aanbevolen voor toepassingen waar gebruikers naar adressen of punten van belang in hetzelfde tekstvak zoeken.
- [**Zoeken in geometrie**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): zoeken naar punten van interesses binnen een opgegeven geometrie. Zoek bijvoorbeeld naar een belang rijke plaats binnen een veelhoek.
- [**Zoeken langs Route**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): zoeken naar punten van interesses die langs een opgegeven traject staan.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): een aanvraag maken met maxi maal 10.000 adressen, plaatsen, bezienswaardigheden of interesse punten. De aanvraag is verwerkt gedurende een bepaalde periode. Alle gegevens worden parallel verwerkt op de-server. Wanneer de aanvraag is voltooid, kunt u de volledige set resultaten downloaden.

Momenteel heeft Azure Maps geen vergelijk bare API voor de tekst zoekopdracht-API in Google Maps.

> [!TIP]
> De POI Search, POI categorie Search en fuzzy Search-Api's kunnen worden gebruikt in de modus automatisch volt ooien door `&amp;typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server ervan op de hoogte dat de invoer tekst waarschijnlijk gedeeltelijk is. De API voert de zoek opdracht uit in voorspellende modus.

Bekijk de [Aanbevolen procedures voor zoek](how-to-use-best-practices-for-search.md) documentatie.

### <a name="find-place-from-text"></a>Locatie van tekst zoeken

Gebruik de Azure Maps [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) en [fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) om op naam of adres te zoeken naar punten van interesses.

De tabel kruis verwijst naar de API-para meters van Google Maps met de vergelijk bare Azure Maps API-para meters.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter |
|---------------------------|-------------------------------------|
| `fields`                  | *N.v.t.*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N.v.t.*                               |
| `key`                     | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` en `radius`<br/>`topLeft` en `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>In de buurt zoeken

Gebruik de [Zoek](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) -API in de buurt om geïnteresseerde punten op te halen in azure Maps.

De tabel toont de Google Maps API-para meters met de vergelijk bare Azure Maps API-para meters.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `keyword`                   | `categorySet` en `brandSet`        |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `location`                  | `lat` en `lon`                     |
| `maxprice`                  | *N.v.t.*                               |
| `minprice`                  | *N.v.t.*                               |
| `name`                      | `categorySet` en `brandSet`        |
| `opennow`                   | *N.v.t.*                               |
| `pagetoken`                 | `ofs` en `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N.v.t.*                               |
| `type`                      | Zie de documentatie voor [ondersteunde Zoek categorieën](supported-search-categories.md) `categorySet –`.   |

## <a name="calculate-routes-and-directions"></a>Routes en instructies berekenen

Routes en instructies berekenen met behulp van Azure Maps. Azure Maps heeft veel dezelfde functies als de Routing-service Google Maps, zoals:

- Aankomst-en vertrek tijden.
- Realtime en voorspellende verkeers routes.
- Verschillende transport methoden. Zoals, aangedreven, wandel, Fietsen.

> [!NOTE]
> Voor Azure Maps moeten alle waypoints coördinaten zijn. Adressen moeten eerst geogecodeerd zijn.

De Azure Maps Routing-service biedt de volgende Api's voor het berekenen van routes:

- [**Route berekenen**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): een route berekenen en de aanvraag onmiddellijk verwerken. Deze API biedt ondersteuning voor GET-en POST-aanvragen. POST-aanvragen gebruiken wanneer u een groot aantal waypoints opgeeft of wanneer u veel van de route opties gebruikt. Dat komt doordat het gebruik van POST ervoor zorgt dat de URL-aanvraag niet te lang wordt en problemen veroorzaakt.
- [**Batch route**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): een aanvraag met maxi maal 1.000 route aanvraag maken en deze over een bepaalde periode verwerken. Alle gegevens worden parallel verwerkt op de-server. Wanneer de verwerking is voltooid, kunt u de volledige set met resultaten downloaden.
- [**Mobiliteits Services**](https://docs.microsoft.com/rest/api/maps/mobility): routes en instructies berekenen met behulp van open bare door voer.

De tabel kruis verwijst naar de Google Maps API-para meters met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter    | Vergelijk bare Azure Maps API-para meter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`: coördinaten in de notatie `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                     | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N.v.t.* : deze functie is geocodering. Gebruik de para meter *landset* wanneer u de Azure Maps geocoderings-API gebruikt.  |
| `traffic_model`               | *N.v.t.* : kan alleen opgeven of verkeers gegevens moeten worden gebruikt met de *verkeers* parameter. |
| `transit_mode`                | Raadpleeg de [documentatie van Mobility Services](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Raadpleeg de [documentatie van Mobility Services](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N.v.t.* – Azure Maps maakt alleen gebruik van het metrieke systeem.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> De Azure Maps route-API retourneert standaard alleen een samen vatting. Hiermee worden de afstand en tijden en de coördinaten voor het traject geretourneerd. Gebruik de para meter `instructionsType` om instructies voor inschakeling op te halen. En gebruik de para meter `routeRepresentation` om de samen vatting en het traject uit te filteren.

Azure Maps Routing-API heeft aanvullende functies, die niet beschikbaar zijn in Google Maps. Wanneer u uw app migreert, kunt u deze functies gebruiken.

- Ondersteuning voor route type: Shortest, snelst, trilling en de meeste brandstof-efficiënt.
- Ondersteuning voor aanvullende reis modi: bus, motor rijwiel, taxi, vracht wagen en van.
- Ondersteuning voor 150 waypoints.
- Meerdere reis tijden berekenen in één aanvraag; historisch verkeer, Live verkeer, geen verkeer.
- Vermijd extra wegtypen: Carpool wegen, unpaved wegen, al gebruikt wegen.
- Geef aangepaste gebieden op om te voor komen.
- Beperk de uitbrei ding van bevoegdheden, die de route kan oplopen.
- Route op basis van engine specificaties. Routes voor verbranding of elektrische Voer tuigen berekenen op basis van engine specificaties en de resterende brand stof of belasting.
- Biedt ondersteuning voor route parameters voor commerciële Voer tuigen. Zoals voertuig dimensies, gewicht, aantal Axels en ladings type.
- De maximale snelheid van het Voer tuig opgeven.

Daarnaast biedt de route service in Azure Maps ondersteuning voor het [berekenen van omleid bare bereiken](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Het berekenen van omleid bare bereiken wordt ook wel van isochronen genoemd. Dit omvat het genereren van een veelhoek die een gebied bedekt dat kan worden verplaatst naar een wille keurige richting van een oorsprongs punt. Alle onder een bepaalde hoeveelheid tijd of hoeveelheid brand stof of kosten.

## <a name="retrieve-a-map-image"></a>Een kaart afbeelding ophalen

Azure Maps biedt een API voor het weer geven van de statische kaart installatie kopieën met gegevens die elkaar overlappen. De render-API voor de [kaart afbeelding](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) in azure Maps is vergelijkbaar met de statische map-API in Google Maps.

> [!NOTE]
> Voor Azure Maps zijn het midden, alle markeringen en de pad-locaties vereist voor coördinaten in de notatie lengte graad, breedte graad. Overwegende dat Google Maps de notatie ' breedte graad, lengte graad ' gebruikt. Adressen moeten eerst geogecodeerd zijn.

De tabel kruis verwijst naar de Google Maps API-para meters met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`: opgegeven als onderdeel van het URL-pad. Momenteel wordt alleen PNG ondersteund. |
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `maptype`                   | `layer` en `style` – zie documentatie over [ondersteunde kaart stijlen](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N.v.t.* : dit is een functie die betrekking heeft op Geocode ring. Gebruik de para meter `countrySet` wanneer u de Azure Maps geocoderings-API gebruikt.  |
| `scale`                     | *N.v.t.*                              |
| `size`                      | `width` en `height` – kunnen Maxi maal 8192x8192 groot zijn. |
| `style`                     | *N.v.t.*                              |
| `visible`                   | *N.v.t.*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> In het Azure Maps tegel systeem zijn tegels twee maal de grootte van kaart tegels die worden gebruikt in Google Maps. Omdat de waarde voor zoom niveau in Azure Maps één zoom niveau dichter in Azure Maps in vergelijking met Google Maps wordt weer gegeven. U kunt dit verschil compenseren door het zoom niveau te verlagen in de aanvragen die u migreert.

Zie voor meer informatie de [hand leiding op de kaart afbeelding render-API](how-to-render-custom-data.md).

Naast het genereren van een statische kaart afbeelding, biedt de Azure Maps render-service de mogelijkheid om rechtstreeks toegang te krijgen tot kaart tegels in raster (PNG) en de vector indeling:

- [**Kaart tegel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): raster (PNG) en vector tegels ophalen voor de basis kaarten (wegen, grenzen, achtergrond).
- [**Tegel afbeelding kaart**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Haal tegels voor lucht foto-en satelliet afbeeldingen op.

> [!TIP]
> Veel Google Maps-toepassingen die een paar jaar geleden overstappen van interactieve kaarten naar statische kaart installatie kopieën. Dit is gedaan als methode om kosten op te slaan. In Azure Maps is het doorgaans rendabeler voor het gebruik van het besturings element interactieve map in de Web-SDK. De interactieve toewijzings beheer kosten op basis van het aantal tegels geladen. Kaart tegels in Azure Maps groot zijn. Vaak worden er slechts enkele tegels gebruikt om dezelfde kaart weergave opnieuw te maken als een statische kaart. Kaart tegels worden automatisch in de cache opgeslagen door de browser. Als zodanig genereert het besturings element interactieve kaart vaak een fractie van een trans actie bij het reproduceren van een statische kaart weergave. Bij het pannen en zoomen worden meer tegels geladen. Er zijn echter opties in het kaart besturings element om dit gedrag uit te scha kelen. Het besturings element interactieve map biedt ook veel meer visualisatie opties dan de statische toewijzings Services.

### <a name="marker-url-parameter-format-comparison"></a>Vergelijking van parameter notatie voor markerings-URL

**Voor: Google Maps**

Voeg markeringen toe met behulp van de para meter `markers` in de URL. De para meter `markers` heeft een stijl en een lijst met locaties die moeten worden weer gegeven op de kaart met die stijl zoals hieronder wordt weer gegeven:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Als u extra stijlen wilt toevoegen, gebruikt u de para meters `markers` voor de URL met een andere stijl en set locaties.

Geef de markerings locaties met de notatie ' breedte graad, lengte graad ' op.

Voeg markerings stijlen met de `optionName:value` indeling toe, met meerdere stijlen, gescheiden door sluis tekens (\|), zoals deze ' optionName1: waarde1\|optionName2: waarde2 '. Houd er rekening mee dat de optie namen en waarden worden gescheiden door een dubbele punt (:). Gebruik de volgende namen van stijl opties voor stijl markeringen in Google Maps:

- `color`: de kleur van het pictogram standaard markering. Dit kan een 24-bits hex-kleur (`0xrrggbb`) of een van de volgende waarden zijn. `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`: een enkel hoofdletter alfanumeriek teken dat boven op het pictogram wordt weer gegeven.
- `size`-de grootte van de markering. Kan `tiny`, `mid`of `small`zijn.

Gebruik de volgende stijl opties namen voor aangepaste pictogrammen in Google Maps:

- `anchor`: Hiermee geeft u op hoe de pictogram afbeelding moet worden uitgelijnd op de coördinaat. Dit kan een pixel waarde (x, y) of een van de volgende waarden zijn. `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`of `bottomright`.
- `icon`: een URL die verwijst naar de pictogram afbeelding.

Laten we bijvoorbeeld een rode, middel grote markering toevoegen aan de kaart met de lengte graad:-110, Latitude: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-markering](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Na: Azure Maps**

Voeg markeringen toe aan een statische kaart installatie kopie door de para meter `pins` op te geven in de URL. Net als bij Google Maps geeft u een stijl en een lijst met locaties op in de para meter. De para meter `pins` kan meerdere keren worden opgegeven om markeringen met verschillende stijlen te ondersteunen.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Als u aanvullende stijlen wilt gebruiken, voegt u extra `pins` para meters toe aan de URL met een andere stijl en set locaties.

In Azure Maps moet de locatie van de pincode de indeling ' breedte graad ' hebben. Google Maps maakt gebruik van de indeling ' breedte graad, lengte graad '. Een spatie, geen komma, scheidt de lengte graad en breedte graad in de Azure Maps indeling.

Met de `iconType` geeft u het type pincode op dat moet worden gemaakt. Dit kan de volgende waarden hebben:

- `default`: het pictogram voor de standaard pincode.
- `none`: er wordt geen pictogram weer gegeven, alleen labels worden gerenderd.
- `custom`: Hiermee geeft u een aangepast pictogram moet worden gebruikt. Een URL die verwijst naar de pictogram afbeelding kan worden toegevoegd aan het einde van de para meter `pins` na de locatie gegevens van de pincode.
- `{udid}`: een unieke gegevens-ID (UDID) voor een pictogram dat is opgeslagen in het Azure Maps gegevens opslag platform.

Voeg PIN-stijlen toe met de `optionNameValue` indeling. Scheid meerdere stijlen met de sluis tekens (\|). Bijvoorbeeld: `iconType|optionName1Value1|optionName2Value2`. De optie namen en waarden worden niet gescheiden. Gebruik de volgende stijl namen voor stijl Markeringen:

- `al`: geeft de dekking (alpha) van de markering aan. Kies een getal tussen 0 en 1.
- `an`: Hiermee geeft u het anker van de pincode. Geef X-en y-pixel waarden op in de notatie x y.
- `co`: de kleur van de pincode. Geef een 24-bits hex-kleur op: `000000` `FFFFFF`.
- `la`: geeft het anker van het label aan. Geef X-en y-pixel waarden op in de notatie x y.
- `lc`: de kleur van het label. Geef een 24-bits hex-kleur op: `000000` `FFFFFF`.
- `ls`: de grootte van het label in pixels. Kies een getal dat groter is dan 0.
- `ro`: een waarde in graden voor het draaien van het pictogram. Kies een getal tussen-360 en 360.
- `sc`: een schaal waarde voor het speld pictogram. Kies een getal dat groter is dan 0.

Geef label waarden op voor elke pincode locatie. Deze aanpak is efficiënter dan het Toep assen van één label waarde op alle markeringen in de lijst met locaties. De label waarde kan een teken reeks van meerdere tekens zijn. Verdeel de teken reeks met enkele aanhalings tekens om er zeker van te zijn dat deze niet wordt omheen als een stijl of locatie waarde.

We gaan een rood (`FF0000`) standaard pictogram toevoegen, met het label "Space naald", onder (15 50). Het pictogram heeft de lengte graad:-122,349300, Latitude: 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps markering](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Voeg drie pincodes toe met de label waarden 1, 2 en 3:

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps meerdere markeringen](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Vergelijking van de pad-URL-para meter

**Voor: Google Maps**

Voeg regels en veelhoek toe aan een statische kaart installatie kopie met behulp van de para meter `path` in de URL. De para meter `path` heeft een stijl en een lijst met locaties die moeten worden weer gegeven op de kaart, zoals hieronder wordt weer gegeven:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Gebruik aanvullende stijlen door extra `path` para meters toe te voegen aan de URL met een andere stijl en set locaties.

Er zijn pad locaties opgegeven met de `latitude1,longitude1|latitude2,longitude2|…`-indeling. Paden kunnen worden gecodeerd of adressen bevatten voor punten.

Voeg stijlen voor paden met de `optionName:value` indeling toe, waarbij u meerdere stijlen van de sluis tekens (\|) scheidt. En u kunt optie namen en-waarden scheiden met een dubbele punt (:). Zo: `optionName1:value1|optionName2:value2`. De volgende stijl optie namen kunnen worden gebruikt voor het opmaken van paden in Google Maps:

- `color`: de kleur van het pad of het overzicht van de veelhoek. Dit kan een 24-bits hex-kleur (`0xrrggbb`), een 32-bits hex-kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `fillColor`: de kleur voor het vullen van het pad naar de locatie (veelhoek). Dit kan een 24-bits hex-kleur (`0xrrggbb`), een 32-bits hex-kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `geodesic`: geeft aan of het pad een lijn is die de kromte van de aarde volgt.
- `weight`: de dikte van de padregel in pixels.

Voeg een rode lijn dekking en pixel dikte toe aan de kaart tussen de coördinaten, in de URL-para meter. In het onderstaande voor beeld heeft de lijn een dekking van 50% en een dikte van vier pixels. De coördinaten zijn lengte graad:-110, Latitude: 45 en lengte graad:-100, Latitude: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-poly lijn](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Na: Azure Maps**

Voeg lijnen en veelhoeken toe aan een statische kaart installatie kopie door de para meter `path` op te geven in de URL. Specificeer net als Google Maps een stijl en een lijst met locaties in deze para meter. Geef de para meter `path` meerdere keren op om meerdere cirkels, lijnen en veelhoeken met verschillende stijlen weer te geven.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Wanneer dit het geval is, moet de coördinaten in de notatie ' breedte graad ' zijn gebaseerd op Azure Maps. Google Maps maakt gebruik van de indeling ' breedte graad, lengte graad '. Een spatie, geen komma, scheidt de lengte graad en breedte graad in de Azure Maps indeling. Azure Maps ondersteunt geen gecodeerde paden of adressen voor punten. Upload grotere gegevens sets als een geojson-bestand naar de Azure Maps Data Storage-API zoals [hier](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)wordt beschreven.

Voeg stijlen voor paden toe met de `optionNameValue` indeling. Scheid meerdere stijlen van het sluis teken (\|), zoals in deze `optionName1Value1|optionName2Value2`. De optie namen en waarden worden niet gescheiden. Gebruik de volgende stijl opties voor stijl paden in Azure Maps:

- `fa`: de opvul kleur dekking (alpha) die wordt gebruikt bij het renderen van veelhoeken. Kies een getal tussen 0 en 1.
- `fc`: de opvul kleur die wordt gebruikt om het gebied van een veelhoek weer te geven.
- `la`: de lijn kleur dekking (alpha) die wordt gebruikt bij het renderen van lijnen en het overzicht van veelhoeken. Kies een getal tussen 0 en 1.
- `lc`: de lijn kleur die wordt gebruikt om lijnen en het overzicht van veelhoeken weer te geven.
- `lw`: de breedte van de lijn in pixels.
- `ra`: Hiermee geeft u een straal van cirkels op in meters.

Voeg een rode lijn dekking en pixel dikte tussen de coördinaten toe, in de URL-para meter. In het onderstaande voor beeld heeft de lijn een dekking van 50% en een dikte van vier pixels. De coördinaten hebben de volgende waarden: lengte graad:-110, Latitude: 45 en lengte graad:-100, Latitude: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps poly lijn](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Een afstands matrix berekenen

Azure Maps biedt de afstand matrix-API. Gebruik deze API om de reis tijden en de afstanden tussen een set locaties met een afstands matrix te berekenen. Het is vergelijkbaar met de API voor de afstands matrix in Google Maps.

- [**Route matrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): berekent op asynchrone wijze reis tijden en afstanden voor een reeks oorsprongen en bestemmingen. Ondersteunt Maxi maal 700 cellen per aanvraag. Dat is het aantal oorsprong vermenigvuldigd met het aantal bestemmingen. Voor beelden van mogelijke matrix dimensies zijn: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Een aanvraag naar de afstands matrix-API kan alleen worden gemaakt met behulp van een POST-aanvraag met de bron-en doel informatie in de hoofd tekst van de aanvraag. Daarnaast moeten voor Azure Maps alle oorsprongen en doelen coördinaten zijn. Adressen moeten eerst geogecodeerd zijn.

Deze tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare Azure Maps API-para meters.

| Google Maps API-para meter      | Vergelijk bare Azure Maps API-para meter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`: Geef in de hoofd tekst van de aanvraag op als geojson. |
| `key`                          | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                     | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`: Geef in de hoofd tekst van de aanvraag op als geojson.  |
| `region`                       | *N.v.t.* : deze functie is geocodering. Gebruik de para meter `countrySet` wanneer u de Azure Maps geocoderings-API gebruikt. |
| `traffic_model`                | *N.v.t.* : kan alleen opgeven of verkeers gegevens moeten worden gebruikt met de para meter `traffic`. |
| `transit_mode`                 | *N/A* -op Transit gebaseerde afstands matrices worden momenteel niet ondersteund.  |
| `transit_routing_preference`   | *N/A* -op Transit gebaseerde afstands matrices worden momenteel niet ondersteund.  |
| `units`                        | *N.v.t.* – Azure Maps maakt alleen gebruik van het metrieke systeem. |

> [!TIP]
> Alle geavanceerde routerings opties die beschikbaar zijn in de Azure Maps routerings-API worden ondersteund in de Azure Maps distance matrix-API. Geavanceerde routerings opties zijn: vracht routering, Engine specificaties, enzovoort.

## <a name="get-a-time-zone"></a>Een tijd zone ophalen

Azure Maps biedt een API voor het ophalen van de tijd zone van een coördinaat. De API voor de Azure Maps tijd zone is vergelijkbaar met de tijd zone-API in Google Maps:

- [**Tijd zone per coördinaat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Geef een coördinaat op en ontvang de tijdzone gegevens van de coördinaat.

Deze tabel bevat kruis verwijzingen naar de Google Maps API-para meters met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie.       |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Naast deze API biedt Azure Maps een aantal tijd zone-Api's. Deze Api's converteren de tijd op basis van de namen of de Id's van de tijd zone:

- [**Tijd zone op id**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): retourneert actuele, historische en toekomstige tijd zone-informatie voor de opgegeven IANA-tijd zone-id.
- [**Time zone Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): retourneert een volledige lijst met tijd zone-id's van de IANA. Updates van de IANA-service worden binnen één dag in het systeem weer gegeven.
- [**Time zone Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): retourneert een volledige lijst met Windows-tijd zone-id's.
- [**Tijd zone IANA-versie**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): retourneert het huidige IANA-versie nummer dat wordt gebruikt door Azure Maps.
- [**Tijd zone vensters naar IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): retourneert een CORRESPONDERENDE IANA-id, gezien een geldige Windows-tijd zone-id. Er kunnen meerdere IANA-Id's voor één Windows-ID worden geretourneerd.

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps biedt client bibliotheken voor de volgende programmeer talen:

- Java script, type script, node. js – [documentation](how-to-use-services-module.md) \| [NPM-pakket](https://www.npmjs.com/package/azure-maps-rest)

Deze open-source client bibliotheken zijn voor andere programmeer talen:

- .NET Standard 2,0 – [github project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-pakket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Aanvullende resources

Hieronder vindt u aanvullende documentatie en bronnen voor de Azure Maps REST-services.

- [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md)
- [Een adres zoeken](how-to-search-for-address.md)
- [Naslag documentatie voor de API van Azure Maps REST-service](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps REST-services.

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de zoek service](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [De Services-module gebruiken (Web-SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)