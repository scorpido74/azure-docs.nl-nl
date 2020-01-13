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
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910763"
---
# <a name="migrate-web-service-from-google-maps"></a>Webservice migreren vanuit Google Maps

Zowel Azure-als Google-kaarten bieden toegang tot ruimtelijke Api's via REST-webservices. De API-interfaces voor deze platforms voeren vergelijk bare functies uit, maar gebruiken verschillende naam conventies en antwoord objecten.

De volgende tabel bevat de Azure Maps service-Api's die vergelijk bare functionaliteit bieden aan de vermelde Google Maps service-Api's.

| Service-API voor Google Maps | API van Azure Maps-service                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Routebeschrijving              | [Rond](https://docs.microsoft.com/rest/api/maps/route)                               |
| Distance Matrix         | [Route matrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodering               | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Locaties zoeken           | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Automatisch aanvullen plaatsen      | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statische toewijzing              | [Weergave](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Tijdzone               | [Tijd zone](https://docs.microsoft.com/rest/api/maps/timezone)                        |

De volgende service-Api's zijn momenteel niet beschikbaar in Azure Maps:

- Uitbrei ding
- Geolocatie
- Plaatst Details en foto's. De telefoon nummers en de URL van de website zijn beschikbaar in de Azure Maps Search-API.
- Kaart-Url's
- Wegen: snelheids limiet gegevens zijn beschikbaar via de routerings-en reverse geocoderings-Api's in Azure Maps.
- Statische straat weergave

Azure Maps heeft verschillende extra REST-webservices die van belang kunnen zijn:

- [Ruimtelijke bewerkingen](https://docs.microsoft.com/rest/api/maps/spatial): offload complexe ruimtelijke berekeningen en bewerkingen, zoals geoomheining, naar een service.
- [Verkeer](https://docs.microsoft.com/rest/api/maps/traffic): toegang tot realtime verkeers stromen en incident gegevens.

## <a name="geocoding-addresses"></a>Geocoderings adressen

Geocodering is het proces van het converteren van een adres (zoals "1 micro soft Way, Redmond, WA") in een coördinaat (zoals lengte graad:-122,1298, Latitude: 47,64005). Coördinaten worden vervolgens vaak gebruikt voor het positioneren van een markering op een kaart of het centreren van een kaart.

Azure Maps biedt verschillende methoden voor geocoderings adressen:

- [**Vrije-vorm adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Geef een teken reeks voor één adres op (bijvoorbeeld ' 1 micro soft Way, REDMOND, WA ') en verwerk de aanvraag direct. Dit wordt aanbevolen als u snel afzonderlijke adressen moet coderen.
- [**Gestructureerde adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): hier kunt u de onderdelen van één adres opgeven, zoals de straat naam, plaats, land en post code en de aanvraag onmiddellijk verwerken. Dit wordt aanbevolen als u afzonderlijke adressen snel wilt coderen en de gegevens al zijn geparseerd in de afzonderlijke adres delen.
- [**Batch-adres geocodering**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): een aanvraag met maxi maal 10.000 adressen maken en deze gedurende een bepaalde tijd laten verwerken. Alle adressen worden parallel gecodeerd op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload. Dit wordt aanbevolen voor geocodering van grote gegevens sets.
- [**Fuzzy Search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): deze API combineert het adres Geocode ring met een belang rijke zoek opdracht. Deze API maakt deel uit van een vrije teken reeks die een adres, plaats, oriëntatie punt, interesse of belang rijke categorie kan zijn en de aanvraag onmiddellijk kan verwerken. Deze API wordt aanbevolen voor toepassingen waar gebruikers kunnen zoeken naar adressen of punten van belang in hetzelfde tekstvak.
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

Een voor beeld van het gebruik van de zoek service wordt [hier](how-to-search-for-address.md)beschreven. Raadpleeg de [Aanbevolen procedures voor zoek documentatie voor](how-to-use-best-practices-for-search.md) meer informatie.

> [!TIP]
> De vrije vorm-adres geocodering en fuzzy Zoek-Api's kunnen worden gebruikt in de modus automatisch volt ooien door `&amp;typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server ervan op de hoogte gebracht dat de invoer tekst waarschijnlijk gedeeltelijk is en in de modus voor spelling wordt weer geven.

## <a name="reverse-geocode-a-coordinate"></a>Geocode van een coördinaat omkeren

Omgekeerde geocodering is het proces van het converteren van geografische coördinaten (zoals lengte graad:-122,1298, Latitude: 47,64005) naar het geschatte adres (bijvoorbeeld "1 micro soft Way, Redmond, WA").

Azure Maps biedt verschillende methoden voor reverse geocodering:

- [**Omgekeerde geocodeer**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Geef een enkele geografische coördinaat op om het geschatte adres op te halen en de aanvraag direct te verwerken.
- [**Omgekeerde geocodeer**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Geef een enkele geografische coördinaat op om informatie over Cross Streets in de buurt te krijgen (bijvoorbeeld 1e & Main) en de aanvraag onmiddellijk te verwerken.
- [**Batch adres reverse geocodeer**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): een aanvraag met maxi maal 10.000 coördinaten maken en deze gedurende een bepaalde periode verwerken. Alle gegevens worden parallel verwerkt op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter   | Vergelijk bare Azure Maps API-para meter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *N.v.t.*     |
| `result_type`               | `entityType`    |

Raadpleeg de [Aanbevolen procedures voor zoek documentatie voor](how-to-use-best-practices-for-search.md) meer informatie.

De Azure Maps reverse geocoderings-API bevat enkele extra functies die niet beschikbaar zijn in Google Maps en die mogelijk handig zijn om te integreren bij het migreren van uw app:

- Gegevens van snelheids limiet ophalen.
- Informatie over het gebruiks terrein ophalen: lokale weg, ARTerial, beperkte toegang, helling, enzovoort.
- De zijde van de straat waarop de coördinaat zich bevindt.

## <a name="search-for-points-of-interest"></a>Zoeken naar nuttige plaatsen

Belang rijke gegevens kunnen in Google Maps worden doorzocht met behulp van de locatie zoeken-API. Deze API biedt drie verschillende manieren om te zoeken naar interessante onderwerpen:

- **Locatie van tekst zoeken:** Zoekt naar een belang rijke positie op basis van de naam, het adres of het telefoon nummer.
- In de **buurt zoeken**: zoekt naar geïnteresseerden die zich binnen een bepaalde afstand van een locatie bevinden.
- **Tekst zoeken:** Zoekt naar plaatsen met behulp van een vrije-vorm tekst die informatie over het belang en de locatie bevat. Bijvoorbeeld "pizza in New York" of "restaurants in de buurt van Main St".

Azure Maps biedt verschillende Zoek-Api's voor interessante onderwerpen:

- [**POI zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): zoeken naar punten van interesses op naam. Bijvoorbeeld ' Starbucks '.
- [**POI categorie zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): zoeken naar punten van belangen per categorie. Bijvoorbeeld ' restaurant '.
- In de [**buurt zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): zoekt naar geïnteresseerden die zich binnen een bepaalde afstand van een locatie bevinden.
- [**Fuzzy Search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): deze API combineert het adres Geocode ring met een belang rijke zoek opdracht. Deze API maakt deel uit van een vrije teken reeks die een adres, plaats, oriëntatie punt, interesse of belang rijke categorie kan zijn en de aanvraag onmiddellijk kan verwerken. Deze API wordt aanbevolen voor toepassingen waar gebruikers kunnen zoeken naar adressen of punten van belang in hetzelfde tekstvak.
- [**Zoeken in geometrie**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): zoeken naar punten van interesses die zich binnen een opgegeven geometrie (veelhoek) bevinden.
- [**Zoeken langs Route**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): zoeken naar punten van interesses die langs een opgegeven traject staan.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): een aanvraag maken met maxi maal 10.000 adressen, plaatsen, bezienswaardigheden of interesses en deze worden verwerkt gedurende een bepaalde periode. Alle gegevens worden parallel verwerkt op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload.

Momenteel heeft Azure Maps geen vergelijk bare API voor de tekst zoeken-API van Google Maps.

> [!TIP]
> De POI zoeken, POI categorie zoeken en fuzzy Search-Api's kunnen worden gebruikt in de modus automatisch volt ooien door `&amp;typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server ervan op de hoogte gebracht dat de invoer tekst waarschijnlijk gedeeltelijk is en in de modus voor spelling wordt weer geven.

Raadpleeg de [Aanbevolen procedures voor zoek documentatie voor](how-to-use-best-practices-for-search.md) meer informatie.

### <a name="find-place-from-text"></a>Locatie van tekst zoeken

Als u wilt zoeken naar punten van interesses op naam of adres, kunt u de Azure Maps [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) en [fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -api's gebruiken.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter |
|---------------------------|-------------------------------------|
| `fields`                  | *N.v.t.*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N.v.t.*                               |
| `key`                     | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie. |
| `language`                | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` en `radius`<br/>`topLeft` en `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>In de buurt zoeken

Nabijgelegen punten kunnen worden opgehaald in Azure Maps met behulp van de [nabije Zoek](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) -API.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

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

Azure Maps kunnen worden gebruikt voor het berekenen van routes en instructies. Azure Maps heeft veel dezelfde functies als de Routing-service Google Maps, zoals:

- aankomst-en vertrek tijden.
- realtime en voorspellende verkeers routes.
- verschillende vervoers takken; aangedreven, wandel, Fietsen.

> [!NOTE]
> Voor Azure Maps moeten alle waypoints coördinaten zijn. Adressen moeten eerst geogecodeerd zijn.

De Azure Maps Routing-service biedt de volgende Api's voor het berekenen van routes:

- [**Route berekenen**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): een route berekenen en de aanvraag onmiddellijk verwerken. Deze API biedt ondersteuning voor GET-en POST-aanvragen. POST-aanvragen worden aanbevolen bij het opgeven van een groot aantal waypoints of bij het gebruik van veel van de route opties om ervoor te zorgen dat de URL-aanvraag niet te lang wordt en problemen veroorzaakt.
- [**Batch route**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): een aanvraag met maxi maal 1.000 route aanvraag maken en deze over een bepaalde periode verwerken. Alle gegevens worden parallel verwerkt op de server en wanneer de volledige resultatenset is voltooid, kan deze worden gedownload.
- [**Mobiliteits Services**](https://docs.microsoft.com/rest/api/maps/mobility): routes en instructies berekenen met behulp van open bare door voer.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

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
| `region`                       | *N.v.t.* : dit is een functie die betrekking heeft op Geocode ring. Gebruik de para meter *landset* wanneer u de Azure Maps geocoderings-API gebruikt.  |
| `traffic_model`               | *N.v.t.* : kan alleen opgeven of verkeers gegevens moeten worden gebruikt met de *verkeers* parameter. |
| `transit_mode`                | Raadpleeg de [documentatie van Mobility Services](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Raadpleeg de [documentatie van Mobility Services](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N.v.t.* – Azure Maps maakt alleen gebruik van het metrieke systeem.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> De Azure Maps route-API retourneert standaard alleen een samen vatting (afstand en tijden) en de coördinaten voor het pad van de route. Gebruik de para meter `instructionsType` om instructies voor inschakeling op te halen. De para meter `routeRepresentation` kan worden gebruikt om de samen vatting en het traject te filteren.

De Azure Maps-routerings-API heeft veel extra functies die niet beschikbaar zijn in Google-kaarten die nuttig kunnen zijn voor integratie bij het migreren van uw app:

- Ondersteuning voor route type: Shortest, snelst, trilling en de meeste brandstof-efficiënt.
- Ondersteuning voor aanvullende reis modi: bus, motor rijwiel, taxi, vracht wagen en van.
- Ondersteuning voor 150 waypoints.
- Meerdere reis tijden berekenen in één aanvraag; historisch verkeer, Live verkeer, geen verkeer.
- Vermijd extra wegtypen: Carpool wegen, unpaved wegen, al gebruikt wegen.
- Geef aangepaste gebieden op om te voor komen.
- Beperk de verhoging van de uitbrei ding van de route.
- Op engine-specificatie gebaseerde route ring. Routes voor verbranding of elektro wagens berekenen op basis van de resterende brandstof/kosten en motor specificaties.
- Ondersteuning voor route parameter voor commerciële Voer tuigen; voertuig afmetingen, gewicht, aantal axelss en vracht type.
- De maximale snelheid van het Voer tuig opgeven.

Daarnaast biedt de route service in Azure Maps ook ondersteuning voor het [berekenen van Routeer bare bereiken](https://docs.microsoft.com/rest/api/maps/route/getrouterange), ook wel bekend als van isochronen, waarmee een veelhoek wordt gegenereerd over een gebied dat kan worden verplaatst naar een wille keurige richting van een oorsprongs punt binnen een bepaalde tijd of hoeveelheid brandstof/kosten.

## <a name="retrieve-a-map-image"></a>Een kaart afbeelding ophalen

Azure Maps biedt een API voor het weer geven van de statische kaart installatie kopieën met gegevens die elkaar overlappen. De Azure Maps [kaart afbeelding render](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) -API is vergelijkbaar met de statische map-API in Google Maps.

> [!NOTE]
> Azure Maps vereist het middel punt, alle markerings-en pad-locaties moeten zijn coördinaten in de notatie lengte graad, breedte graad, terwijl Google Maps de notatie ' breedte graad, lengte graad ' gebruikt. Adressen moeten eerst geogecodeerd zijn.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

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
> Azure Maps maakt gebruik van een tegel systeem met tegels die twee maal zo groot zijn als de kaart tegels die worden gebruikt in Google Maps. Omdat de waarde voor zoom niveau in Azure Maps één zoom niveau dichter in Azure Maps in vergelijking met Google Maps wordt weer gegeven. Verlaag het zoom niveau in de aanvragen die u door één migreert om dit te compenseren.

Zie voor meer informatie de [hand leiding op de kaart afbeelding render-API](how-to-render-custom-data.md).

Naast het genereren van een statische kaart afbeelding, biedt de Azure Maps render-service ook de mogelijkheid om rechtstreeks toegang te krijgen tot kaart tegels in raster (PNG) en de vector indeling:

- [**Kaart tegel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): raster (PNG) en vector tegels ophalen voor de basis kaarten (wegen, grenzen, achtergrond).
- [**Tegel afbeelding kaart**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Haal tegels voor lucht foto-en satelliet afbeeldingen op.

> [!TIP]
> Veel Google Maps-toepassingen waarbij interactieve kaarten een paar jaar geleden worden overgeschakeld naar statische kaarten, zoals een methode voor het opslaan van kosten. In Azure Maps is het vaak veel rendabeler om het interactieve kaart besturings element in de Web-SDK te gebruiken wanneer het wordt berekend op basis van de belasting van de kaart. Het toewijzen van tegels in Azure Maps zijn groot en het is vaak slechts een paar nodig om dezelfde kaart weergave opnieuw te maken als een statische kaart en de kaart tegels worden automatisch in de cache opgeslagen door de browser. Als zodanig wordt het besturings element interactieve kaart vaak alleen een fractie van een trans actie gegenereerd bij het reproduceren van een statische kaart weergave. Bij het pannen en zoomen worden meer tegels geladen. er zijn echter opties in het kaart besturings element om dit gedrag als gewenst uit te scha kelen. Het besturings element interactieve map biedt ook veel meer visualisatie opties dan statische map-Services.

### <a name="marker-url-parameter-format-comparison"></a>Vergelijking van parameter notatie voor markerings-URL

**Voor: Google Maps**

In Google Maps-markeringen kan worden toegevoegd aan een statische kaart installatie kopie met behulp van de para meter `markers` in de URL. De para meter `markers` heeft een stijl en een lijst met locaties die moeten worden weer gegeven op de kaart met die stijl zoals hieronder wordt weer gegeven:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Aanvullende stijlen kunnen worden gebruikt door extra `markers` para meters toe te voegen aan de URL met een andere stijl en set locaties.

Markerings locaties worden opgegeven met de notatie "breedte graad, lengte graad".

Markerings stijlen in Google Maps worden toegevoegd met de indeling `optionName:value`, met meerdere stijlen, gescheiden door sluis tekens (\|), zoals deze ' optionName1: waarde1\|optionName2: waarde2 '. Houd er rekening mee dat de optie namen en waarden worden gescheiden door een dubbele punt (:). De volgende stijl optie namen kunnen worden gebruikt voor het opmaken van markeringen in Google Maps:

- `color`: de kleur van het pictogram standaard markering. Dit kan een 24-bits hex-kleur (`0xrrggbb`) of een van de volgende waarden zijn. `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`: een enkel hoofdletter alfanumeriek teken dat boven op het pictogram wordt weer gegeven.
- `size`-de grootte van de markering. Kan `tiny`, `mid`of `small`zijn.

Aangepaste pictogrammen kunnen ook worden gebruikt in Google Maps met de volgende stijl namen:

- `anchor`: Hiermee geeft u op hoe de pictogram afbeelding moet worden uitgelijnd op de coördinaat. Dit kan een pixel waarde (x, y) of een van de volgende waarden zijn. `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`of `bottomright`.
- `icon`: een URL die verwijst naar de pictogram afbeelding.

In Google Maps kan bijvoorbeeld een rode, middel grote markering worden toegevoegd aan de kaart op coördinaten (lengte graad:-110, Latitude: 45) met de volgende URL-para meter:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-markering](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Na: Azure Maps**

In Azure Maps markeringen kunnen ook worden toegevoegd aan een statische kaart installatie kopie door de para meter `pins` op te geven in de URL. Net als bij Google Maps kunnen een stijl en een lijst met locaties worden opgegeven in deze para meter en kan de para meter `pins` meerdere keren worden opgegeven om markeringen met verschillende stijlen te ondersteunen.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Aanvullende stijlen kunnen worden gebruikt door extra `pins` para meters toe te voegen aan de URL met een andere stijl en set locaties.

Wanneer het gaat om locaties vastmaken, moet Azure Maps de coördinaten hebben in de indeling lengte graad breedte, terwijl Google Maps de notatie ' breedte graad, lengte graad ' gebruikt. Houd er ook rekening mee dat er sprake is van een spatie, geen komma en breedte graad in Azure Maps.

De `iconType` waarde geeft het type pincode aan dat moet worden gemaakt en kan de volgende waarden hebben:

- `default`: het pictogram voor de standaard pincode.
- `none`: er wordt geen pictogram weer gegeven, alleen labels worden gerenderd.
- `custom`: Hiermee geeft u een aangepast pictogram moet worden gebruikt. Een URL die verwijst naar de pictogram afbeelding kan worden toegevoegd aan het einde van de para meter `pins` na de locatie gegevens van de pincode.
- `{udid}`: een unieke gegevens-ID (UDID) voor een pictogram dat is opgeslagen in het Azure Maps gegevens opslag platform.

De stijlen voor vastmaken in Azure Maps worden toegevoegd met de indeling `optionNameValue`, met meerdere stijlen, gescheiden door sluis tekens (\|), zoals deze `iconType|optionName1Value1|optionName2Value2`. Houd er rekening mee dat de optie namen en waarden niet gescheiden zijn. De volgende stijl optie namen kunnen worden gebruikt voor het opmaken van markeringen in Azure Maps:

- `al`: geeft de dekking (alpha) van de markering aan. Dit kan een getal tussen 0 en 1 zijn.
- `an`: Hiermee geeft u het anker van de pincode. X-en y-pixel waarden die zijn opgegeven in de notatie x y.
- `co`: de kleur van de pincode. Moet een 24-bits hex-kleur zijn: `000000` `FFFFFF`.
- `la`: geeft het anker van het label aan. X-en y-pixel waarden die zijn opgegeven in de notatie x y.
- `lc`: de kleur van het label. Moet een 24-, maar hexadecimale kleur zijn: `000000` `FFFFFF`.
- `ls`: de grootte van het label in pixels. Dit kan een getal groter dan 0 zijn.
- `ro`: een waarde in graden voor het draaien van het pictogram. Dit kan een getal zijn tussen-360 en 360.
- `sc`: een schaal waarde voor het speld pictogram. Dit kan een getal groter dan 0 zijn.

Label waarden worden opgegeven voor elke pincode locatie in plaats van een enkele label waarde die van toepassing is op alle markeringen in de lijst met locaties. De label waarde kan bestaan uit een teken reeks van meerdere tekens en moet worden verpakt met enkele citaten om ervoor te zorgen dat deze niet wordt aangeduid als een stijl of locatie waarde.

U kunt bijvoorbeeld in Azure Maps een rood (`FF0000`) standaard pictogram toevoegen met het label ' ruimte naald ' onder (15 50) het pictogram op coördinaten (lengte graad:-122,349300, breedte graad: 47,620180) kan worden uitgevoerd met de volgende URL-para meter:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps markering](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

In het volgende voor beeld worden drie pincodes toegevoegd met de label waarden ' 1 ', ' 2 ' en ' 3 ':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps meerdere markering](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Vergelijking van de pad-URL-para meter

**Voor: Google Maps**

In Google Maps-regels en veelhoeken kunnen aan een statische kaart installatie kopie worden toegevoegd met behulp van de para meter `path` in de URL. De para meter `path` heeft een stijl en een lijst met locaties die moeten worden weer gegeven op de kaart met die stijl zoals hieronder wordt weer gegeven:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Aanvullende stijlen kunnen worden gebruikt door extra `path` para meters toe te voegen aan de URL met een andere stijl en set locaties.

Pad-locaties in Google Maps worden opgegeven met de indeling `latitude1,longitude1|latitude2,longitude2|…`. Paden kunnen worden gecodeerd of adressen bevatten voor punten.

Paden in Google Maps worden toegevoegd met de indeling `optionName:value`, met meerdere stijlen, gescheiden door sluis tekens (\|), zoals deze `optionName1:value1|optionName2:value2`. Houd er rekening mee dat de optie namen en waarden worden gescheiden door een dubbele punt (:). De volgende stijl optie namen kunnen worden gebruikt voor het opmaken van paden in Google Maps:

- `color`: de kleur van het pad of het overzicht van de veelhoek. Dit kan een 24-bits hex-kleur (`0xrrggbb`), een 32-bits hex-kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn. zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `fillColor`: de kleur voor het vullen van het pad naar de locatie (veelhoek). Dit kan een 24-bits hex-kleur (`0xrrggbb`), een 32-bits hex-kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn. zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `geodesic`: geeft aan of het pad een lijn is die de kromte van de aarde volgt.
- `weight`: de dikte van de padregel in pixels.

In Google Maps kan bijvoorbeeld een rode lijn met een dekking van 50% en een dikte van 4 pixels worden toegevoegd aan de kaart tussen coördinaten (lengte graad:-110, breedte graad: 45 en lengte graad:-100, Latitude: 50) met de volgende URL-para meter:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-poly lijn](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Na: Azure Maps**

In Azure Maps lijnen en veelhoeken kunnen ook worden toegevoegd aan een statische kaart installatie kopie door de para meter `path` op te geven in de URL. Net als bij Google Maps kunnen een stijl en een lijst met locaties worden opgegeven in deze para meter en kan de para meter `path` meerdere keren worden opgegeven om meerdere cirkels, lijnen en veelhoeken met verschillende stijlen weer te geven.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Wanneer het gaat om paden, moeten Azure Maps de coördinaten de lengte graad breedte hebben, terwijl Google Maps de notatie ' breedte graad, lengte graad ' gebruikt. Houd er ook rekening mee dat er sprake is van een spatie, geen komma en breedte graad in Azure Maps. Azure Maps ondersteunt geen gecodeerde paden of adressen voor punten. Grotere gegevens sets kunnen worden geüpload als geojson-opvulling in de Azure Maps Data Storage-API zoals [hier](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)wordt beschreven.

Paden in Azure Maps worden toegevoegd met de indeling `optionNameValue`, met meerdere stijlen, gescheiden door sluis tekens (\|), zoals deze `optionName1Value1|optionName2Value2`. Houd er rekening mee dat de optie namen en waarden niet gescheiden zijn. De volgende stijl optie namen kunnen worden gebruikt voor het opmaken van paden in Azure Maps:

- `fa`: de opvul kleur dekking (alpha) die wordt gebruikt bij het renderen van veelhoeken. Dit kan een getal tussen 0 en 1 zijn.
- `fc`: de opvul kleur die wordt gebruikt om het gebied van een veelhoek weer te geven.
- `la`: de lijn kleur dekking (alpha) die wordt gebruikt bij het renderen van lijnen en het overzicht van veelhoeken. Dit kan een getal tussen 0 en 1 zijn.
- `lc`: de lijn kleur die wordt gebruikt om lijnen en het overzicht van veelhoeken weer te geven.
- `lw`: de breedte van de lijn in pixels.
- `ra`: Hiermee geeft u een straal van cirkels op in meters.

In Azure Maps kan bijvoorbeeld een rode lijn met een dekking van 50% en een dikte van 4 pixels worden toegevoegd aan de kaart tussen coördinaten (lengte graad:-110, breedte graad: 45 en lengte graad:-100, Latitude: 50) met de volgende URL-para meter:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps poly lijn](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Een afstands matrix berekenen

Azure Maps biedt een API voor het berekenen van de reis tijden en de afstand tussen een set locaties als een afstands matrix. De API voor de Azure Maps afstands matrix is vergelijkbaar met de API voor de afstands matrix in Google Maps;

- [**Route matrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): berekent op asynchrone wijze reis tijden en afstanden voor een reeks oorsprongen en bestemmingen. Maxi maal 700 cellen per aanvraag wordt ondersteund (het aantal oorsprong vermenigvuldigd met het aantal doelen). Voor beelden van mogelijke matrix dimensies zijn: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Een aanvraag naar de afstands matrix-API kan alleen worden gemaakt met behulp van een POST-aanvraag met de bron-en doel informatie in de hoofd tekst van de aanvraag. Daarnaast moeten voor Azure Maps alle oorsprongen en doelen coördinaten zijn. Adressen moeten eerst geogecodeerd zijn.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

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
| `region`                       | *N.v.t.* : dit is een functie die betrekking heeft op Geocode ring. Gebruik de para meter `countrySet` wanneer u de Azure Maps geocoderings-API gebruikt. |
| `traffic_model`                | *N.v.t.* : kan alleen opgeven of verkeers gegevens moeten worden gebruikt met de para meter `traffic`. |
| `transit_mode`                 | *N/A* -door Voer gebaseerde afstands matrices worden momenteel niet ondersteund.  |
| `transit_routing_preference`   | *N/A* -door Voer gebaseerde afstands matrices worden momenteel niet ondersteund.  |
| `units`                        | *N.v.t.* – Azure Maps maakt alleen gebruik van het metrieke systeem. |

> [!TIP]
> Alle geavanceerde routerings opties die beschikbaar zijn in de Azure Maps routerings-API (vracht routering, Engine specificaties, voor komen...) worden ondersteund in de Azure Maps-afstands matrix-API.

## <a name="get-a-time-zone"></a>Een tijd zone ophalen

Azure Maps biedt een API voor het ophalen van de tijd zone waarin een coördinaat zich bevindt. De API voor de Azure Maps tijd zone is vergelijkbaar met de tijd zone-API in Google Maps:

- [**Tijd zone per coördinaat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Geef een coördinaat op en haal de details op voor de tijd zone waarin deze valt.

De volgende tabel bevat kruis verwijzingen naar de API-para meters van Google Maps met de vergelijk bare API-para meters in Azure Maps.

| Google Maps API-para meter | Vergelijk bare Azure Maps API-para meter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Zie ook de [verificatie met Azure Maps](azure-maps-authentication.md) documentatie.       |
| `language`                  | `language`: Zie de documentatie voor [ondersteunde talen](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Daarnaast biedt het Azure Maps-platform ook een aantal extra tijd zone-Api's om te helpen bij conversies met tijdzone namen en-Id's:

- [**Tijd zone op id**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): retourneert actuele, historische en toekomstige tijd zone-informatie voor de opgegeven IANA-tijd zone-id.
- [**Time zone Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): retourneert een volledige lijst met tijd zone-id's van de IANA. Updates van de IANA-service worden binnen één dag in het systeem weer gegeven.
- [**Time zone Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): retourneert een volledige lijst met Windows-tijd zone-id's.
- [**Tijd zone IANA-versie**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): retourneert het huidige IANA-versie nummer dat wordt gebruikt door Azure Maps.
- [**Tijd zone vensters naar IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): retourneert een CORRESPONDERENDE IANA-id, gezien een geldige Windows-tijd zone-id. Er kunnen meerdere IANA-Id's voor één Windows-ID worden geretourneerd.

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps biedt client bibliotheken voor de volgende programmeer talen:

- Java script, type script, node. js – [documentation](how-to-use-services-module.md) \| [NPM-pakket](https://www.npmjs.com/package/azure-maps-rest)

Open-source-client bibliotheken voor andere programmeer talen:

- .NET Standard 2,0 – [github project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-pakket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Aanvullende bronnen

Hier volgen enkele aanvullende documentatie en bronnen voor de Azure Maps REST-services.

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