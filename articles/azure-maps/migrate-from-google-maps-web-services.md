---
title: 'Zelfstudie: Webservices migreren vanuit Google Maps | Microsoft Azure Maps'
description: Webservices migreren van Google Maps naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371462"
---
# <a name="migrate-web-service-from-google-maps"></a>Webservice migreren vanuit Google Maps

Zowel Azure als Google Maps bieden toegang tot ruimtelijke API's via REST-webservices. De API-interfaces van deze platforms voeren vergelijkbare functionaliteiten uit. Maar ze gebruiken elk verschillende naamgevingsconventies en antwoordobjecten.

De tabel toont de Azure Maps-service-API's, die een vergelijkbare functionaliteit hebben als de vermelde Google Maps-service-API's.

| Google Maps-service-API | Azure Maps-service-API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Routebeschrijving              | [Route](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Afstandsmatrix         | [Routematrix](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geocodering               | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Places Zoeken           | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Automatisch aanvullen plaatsen      | [Zoeken](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Snap aan weg            | Zie [Routes en routebeschrijvingen](#calculate-routes-and-directions) berekenen.            |
| Snelheidslimieten            | Zie [Een coördinatensectie omkeren.](#reverse-geocode-a-coordinate)                  |
| Statische kaart              | [Renderen](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Tijdzone               | [Tijdzone](https://docs.microsoft.com/rest/api/maps/timezone)                              |

De volgende service-API's zijn momenteel niet beschikbaar in Azure Maps:

- Hoogte
- Geolocatie
- Plaatsen details en foto's - Telefoonnummers en website-URL zijn beschikbaar in de Azure Maps-zoek-API.
- URL's van kaarten
- Dichtstbijzijnde wegen - Dit is haalbaar met behulp van de Web SDK zoals [hier](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)weergegeven, maar momenteel niet beschikbaar als een service.
- Statische straatweergave

Azure Maps heeft verschillende extra REST-webservices die van belang kunnen zijn:

- [Ruimtelijke bewerkingen](https://docs.microsoft.com/rest/api/maps/spatial): Offload complexe ruimtelijke berekeningen en bewerkingen, zoals geofencing, naar een dienst.
- [Verkeer](https://docs.microsoft.com/rest/api/maps/traffic): Toegang tot realtime verkeersstroom- en incidentgegevens.

## <a name="geocoding-addresses"></a>Geocoderingsadressen

Geocodering is het proces van het omzetten van een adres in een coördinaat. Bijvoorbeeld, "1 Microsoft manier, Redmond, WA" converteert naar lengte: -122.1298, breedtegraad: 47.64005. Vervolgens kunnen coördinaten worden gebruikt voor verschillende soorten doeleinden, zoals het plaatsen van een markering op een kaart.

Azure Maps biedt verschillende methoden voor geocoderingsadressen:

- [**Geocodering in het vrije formulier:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)geef één adrestekenreeks op en verwerk de aanvraag onmiddellijk. "1 Microsoft way, Redmond, WA" is een voorbeeld van een enkele adres string. Deze API wordt aanbevolen als u afzonderlijke adressen snel moet geocoderen.
- [**Gestructureerde adresgeocodering**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Geef de delen van één adres op, zoals de straatnaam, plaats, land en postcode, en verwerk de aanvraag onmiddellijk. Deze API wordt aanbevolen als u afzonderlijke adressen snel moet geocoderen en de gegevens al zijn geparseerd in de afzonderlijke adresdelen.
- [**Geocodering voor batchadres:**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)maak een aanvraag met maximaal 10.000 adressen en laat deze gedurende een bepaalde periode verwerken. Alle adressen worden parallel geocoded op de server en wanneer voltooid kan de volledige resultaatset worden gedownload. Dit wordt aanbevolen voor het geocoderen van grote gegevenssets.
- [**Fuzzy search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Deze API combineert adresgeocodering met point of interest search. Deze API neemt een touw in vrije vorm aan. Deze tekenreeks kan een adres, plaats, oriëntatiepunt, bezienswaardigheid of interessecategorie zijn. Deze API verwerkt de aanvraag in de buurt van realtime. Deze API wordt aanbevolen voor toepassingen waarbij gebruikers zoeken naar adressen of bezienswaardigheden in hetzelfde tekstvak.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Maak een aanvraag met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of bezienswaardigheden en laat ze gedurende een bepaalde periode verwerken. Alle gegevens worden parallel op de server verwerkt en na voltooiing kan de volledige resultaatset worden gedownload.

In de volgende tabel wordt de API-parameters van Google Maps vergeleken met de vergelijkbare API-parameters in Azure Maps.

| Parameter Google Maps API | Vergelijkbare Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` en `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- stad / stad<br/>`municipalitySubdivision`– buurt, sub / super stad<br/>`countrySubdivision`- staat of provincie<br/>`countrySecondarySubdivision`- provincie<br/>`countryTertiarySubdivision`- district<br/>`countryCode`- landcode met twee letters |
| `key`                       | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Een voorbeeld van het gebruik van de zoekservice is [hier](how-to-search-for-address.md)gedocumenteerd. Bekijk de [aanbevolen procedures voor zoeken.](how-to-use-best-practices-for-search.md)

> [!TIP]
> De geocodering van het vrije formulier en fuzzy search API's kunnen in de autocomplete modus worden gebruikt door toe te voegen `&typeahead=true` aan de aanvraag-URL. Dit zal de server vertellen dat de invoertekst waarschijnlijk gedeeltelijk is, en het zoeken zal in voorspellende wijze gaan.

## <a name="reverse-geocode-a-coordinate"></a>Een coördinaat omkeren

Reverse geocoding is het proces van het omzetten van geografische coördinaten naar een benaderend adres. Coördinaten met "lengte: -122.1298, breedtegraad: 47.64005" converteren naar "1 Microsoft way, Redmond, WA".

Azure Maps biedt verschillende omgekeerde geocoderingsmethoden:

- [**Adres omgekeerde geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Geef één geografische coördinaat op om het geschatte adres te krijgen dat overeenkomt met deze coördinaat. Verwerkt de aanvraag in de buurt van real-time.
- [**Cross street reverse geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Geef één geografische coördinaat op om informatie over de straat in de buurt te krijgen en de aanvraag onmiddellijk te verwerken. U bijvoorbeeld de volgende dwarsstraten 1st Ave en Main St. ontvangen.
- [**Batch adres omgekeerde geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Maak een aanvraag met maximaal 10.000 coördinaten en laat ze gedurende een bepaalde periode verwerken. Alle gegevens worden parallel op de server verwerkt. Wanneer het verzoek is voltooid, u de volledige set resultaten downloaden.

In deze tabel wordt de API-parameters van Google Maps vergeleken met de vergelijkbare API-parameters in Azure Maps.

| Parameter Google Maps API   | Vergelijkbare Azure Maps API-parameter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *N.v.t.*     |
| `result_type`               | `entityType`    |

Bekijk [aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

De Azure Maps reverse geocoding API heeft een aantal extra functies, die niet beschikbaar zijn in Google Maps. Deze functies kunnen handig zijn om te integreren met uw toepassing, terwijl u uw app migreert:

- Gegevens over snelheidslimiet ophalen
- Informatie ophalen over het gebruik van de weg: lokale weg, arteriële, beperkte toegang, oprit, enzovoort
- Haal de kant van de straat op waar een coördinaat zich bevindt

## <a name="search-for-points-of-interest"></a>Zoeken naar nuttige plaatsen

Gegevens over bezienswaardigheden kunnen worden doorzocht in Google Maps met behulp van de Places Search API. Deze API biedt drie verschillende manieren om te zoeken naar bezienswaardigheden:

- **Plaats vinden in tekst:** Zoekt naar een interessepunt op basis van de naam, het adres of het telefoonnummer.
- **Zoeken in de buurt:** zoekt naar bezienswaardigheden die zich binnen een bepaalde afstand van een locatie bevinden.
- **Tekst zoeken:** Hiermee wordt gezocht naar plaatsen met behulp van een tekst in een vrije vorm, die punt van interesse en locatie-informatie bevat. Bijvoorbeeld "pizza in New York" of "restaurants in de buurt van main st".

Azure Maps biedt verschillende zoek-API's voor bezienswaardigheden:

- [**POI zoeken**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Zoeken naar bezienswaardigheden op naam. Bijvoorbeeld "Starbucks".
- [**POI-categoriezoekopdracht**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Zoek naar interesses per categorie. Bijvoorbeeld "restaurant".
- [**Zoeken in de buurt**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Zoekt naar bezienswaardigheden die zich binnen een bepaalde afstand van een locatie bevinden.
- [**Fuzzy search**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Deze API combineert adresgeocodering met point of interest search. Deze API wordt ingevoerd in een tekenreeks in vrije vorm die een adres, plaats, oriëntatiepunt, bezienswaardigheid of interessecategorie kan zijn. Het verwerkt de aanvraag in de buurt van real-time. Deze API wordt aanbevolen voor toepassingen waarbij gebruikers zoeken naar adressen of bezienswaardigheden in hetzelfde tekstvak.
- [**Zoeken in geometrie**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Zoek naar punten van interesses die zich binnen een bepaalde geometrie bevinden. Zoek bijvoorbeeld een interessant punt binnen een veelhoek.
- [**Zoek langs route**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Zoek naar punten van interesses die zich langs een bepaald routepad bevinden.
- [**Fuzzy batch search**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Maak een aanvraag met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of bezienswaardigheden. De aanvraag gedurende een bepaalde periode verwerkt. Alle gegevens worden parallel op de server verwerkt. Wanneer de aanvraag de verwerking voltooit, u de volledige set resultaten downloaden.

Momenteel heeft Azure Maps geen vergelijkbare API als de Text Search API in Google Maps.

> [!TIP]
> De POI-zoekopdracht, POI-categoriezoeken en vage zoek-API's `&typeahead=true` kunnen in de autocompletemodus worden gebruikt door de URL van de aanvraag toe te voegen. Dit zal de server vertellen dat de invoertekst waarschijnlijk gedeeltelijk is. De API voert de zoekopdracht uit in de voorspellende modus.

Bekijk de [aanbevolen procedures voor zoekdocumentatie.](how-to-use-best-practices-for-search.md)

### <a name="find-place-from-text"></a>Plaats zoeken uit tekst

Gebruik de [POI-zoekopdracht azure](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) maps en [fuzzy search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) om te zoeken naar bezienswaardigheden op naam of adres.

De tabel verwijst door naar de API-parameters van Google Maps met de vergelijkbare Azure Maps API-parameters.

| Parameter Google Maps API | Vergelijkbare Azure Maps API-parameter |
|---------------------------|-------------------------------------|
| `fields`                  | *N.v.t.*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N.v.t.*                               |
| `key`                     | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `locationbias`            | `lat`, `lon` en`radius`<br/>`topLeft` en `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Zoeken in de buurt

Gebruik de [zoek-API in](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) de buurt om bezienswaardigheden in de buurt op te halen in Azure Maps.

De tabel toont de API-parameters van Google Maps met de vergelijkbare Azure Maps API-parameters.

| Parameter Google Maps API | Vergelijkbare Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` en `brandSet`        |
| `language`                  | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `location`                  | `lat` en `lon`                     |
| `maxprice`                  | *N.v.t.*                               |
| `minprice`                  | *N.v.t.*                               |
| `name`                      | `categorySet` en `brandSet`        |
| `opennow`                   | *N.v.t.*                               |
| `pagetoken`                 | `ofs` en `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N.v.t.*                               |
| `type`                      | `categorySet –`Bekijk de documentatie [voor ondersteunde zoekcategorieën.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Routes en routebeschrijvingen berekenen

Bereken routes en routebeschrijvingen met Azure Maps. Azure Maps heeft veel van dezelfde functionaliteiten als de Routeringsservice van Google Maps, zoals:

- Aankomst- en vertrektijden.
- Real-time en voorspellende verkeersroutes.
- Verschillende vormen van vervoer. Zoals rijden, wandelen, fietsen.

> [!NOTE]
> Azure Maps vereist dat alle waypoints worden coördinaat. Adressen moeten eerst geogecodeerd zijn.

De routeringsservice azure maps biedt de volgende API's voor het berekenen van routes:

- [**Route berekenen**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Bereken een route en laat de aanvraag onmiddellijk verwerken. Deze API ondersteunt zowel GET- als POST-aanvragen. POST-aanvragen worden aanbevolen bij het opgeven van een groot aantal waypoints of bij het gebruik van veel routeopties om ervoor te zorgen dat de URL-aanvraag niet te lang wordt en problemen veroorzaakt. De ROUTErichting POST in Azure Maps heeft een optie die duizenden [ondersteunende punten](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) in zich opneemt en deze gebruikt om een logisch routepad tussen deze punten opnieuw te maken (klik op weg). 
- [**Batchroute**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Maak een aanvraag met maximaal 1.000 routeaanvragen en laat deze gedurende een bepaalde periode verwerken. Alle gegevens worden parallel op de server verwerkt en na voltooiing kan de volledige resultaatset worden gedownload.
- [**Mobiliteitsdiensten**](https://docs.microsoft.com/rest/api/maps/mobility): Bereken routes en routebeschrijvingen met het openbaar vervoer.

De tabel verwijst door naar de API-parameters van Google Maps met de vergelijkbare API-parameters in Azure Maps.

| Parameter Google Maps API    | Vergelijkbare Azure Maps API-parameter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– coördinaten in de indeling`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* - Deze functie is geocoding gerelateerd. Gebruik de parameter *countrySet* wanneer u de geocoderings-API voor Azure Maps gebruikt.  |
| `traffic_model`               | *N/A* - Kan alleen opgeven als verkeersgegevens moeten worden gebruikt met de *verkeersparameter.* |
| `transit_mode`                | Bekijk [documentatie over mobiliteitsdiensten](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Bekijk [documentatie over mobiliteitsdiensten](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps gebruikt alleen het metrische systeem.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Standaard retourneert de API voor Azure Maps-route alleen een overzicht. Hiermee worden de afstand en tijden en de coördinaten voor het routepad geretourneerd. Gebruik `instructionsType` de parameter om de afslag-voor-beurt-instructies op te halen. En gebruik `routeRepresentation` de parameter om het overzichts- en routepad uit te filteren.

Azure Maps routing API heeft extra functies die niet beschikbaar zijn in Google Maps. Wanneer u uw app migreert, u overwegen deze functies te gebruiken, maar deze kunnen nuttig zijn.

- Ondersteuning voor routetype: kortste, snelste, trillingsen en zuinigste.
- Ondersteuning voor extra reismodi: bus, motor, taxi, vrachtwagen en bestelwagen.
- Ondersteuning voor 150 waypoints.
- Bereken meerdere reistijden in één aanvraag; historisch verkeer, live verkeer, geen verkeer.
- Vermijd extra wegtypes: carpoolwegen, onverharde wegen, reeds gebruikte wegen.
- Geef aangepaste gebieden op om te vermijden.
- Beperk de hoogte, die de route kan stijgen.
- Route op basis van motorspecificaties. Bereken routes voor verbrandings- of elektrische voertuigen op basis van motorspecificaties en de resterende brandstof of lading.
- Ondersteuning van routeparameters voor bedrijfsvoertuigen. Zoals afmetingen van het voertuig, gewicht, aantal axels en vrachttype.
- Geef de maximale snelheid van het voertuig op.

Daarnaast ondersteunt de routeservice in Azure Maps [het berekenen van routeerbare bereiken.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Het berekenen van routeerbare bereiken wordt ook wel isochronen genoemd. Het houdt in dat een veelhoek wordt overgenomen die een gebied bestrijkt dat vanaf een oorsprongspunt in elke richting kan worden afgelegd. Dit alles onder een bepaalde hoeveelheid tijd of hoeveelheid brandstof of lading.

Bekijk de [aanbevolen procedures voor routeringsdocumentatie.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Een kaartafbeelding ophalen

Azure Maps biedt een API voor het renderen van de statische kaartafbeeldingen met gegevens die zijn bedekt. De [API voor het renderen van kaartafbeeldingen](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) in Azure Maps is vergelijkbaar met de statische kaart-API in Google Maps.

> [!NOTE]
> Azure Maps vereist dat het centrum, alle markeringen en de padlocaties worden coördinaat in de indeling 'lengtegraad, breedtegraad'. Terwijl Google Maps het "latitude,longitude"-formaat gebruikt. Adressen moeten eerst geocoded zijn.

De tabel verwijst door naar de API-parameters van Google Maps met de vergelijkbare API-parameters in Azure Maps.

| Parameter Google Maps API | Vergelijkbare Azure Maps API-parameter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– opgegeven als onderdeel van het URL-pad. Momenteel alleen PNG ondersteund. |
| `key`                       | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `maptype`                   | `layer`en `style` – Zie [Documentatie ondersteunde kaartstijlen.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* - Dit is een geocoding gerelateerde functie. Gebruik `countrySet` de parameter wanneer u de geocoderings-API van Azure Maps gebruikt.  |
| `scale`                     | *N.v.t.*                              |
| `size`                      | `width`en `height` – kan tot 8192x8192 groot zijn. |
| `style`                     | *N.v.t.*                              |
| `visible`                   | *N.v.t.*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> In het tegelsysteem Azure Maps zijn tegels twee keer zo groot als kaarttegels die worden gebruikt in Google Maps. Als zodanig wordt de zoomwaarde in Azure Maps één zoomniveau dichter in Azure Maps weergegeven dan in Google Maps. Als u dit verschil wilt compenseren, moet u het zoomniveau in de aanvragen die u migreert, laten vervallen.

Zie de handleiding [voor het maken van de kaartafbeelding voor](how-to-render-custom-data.md)meer informatie .

De renderservice Azure Maps biedt niet alleen de mogelijkheid om een statische kaartafbeelding te genereren, maar biedt ook de mogelijkheid om direct toegang te krijgen tot kaarttegels in raster- en vectorindeling:

- [**Kaarttegel**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Raster -tegels (PNG) en vectortegels ophalen voor de basiskaarten (wegen, grenzen, achtergrond).
- [**Kaartbeelden tegel:**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Ophalen lucht-en satellietbeelden tegels.

> [!TIP]
> Veel Google Maps-toepassingen waar een paar jaar geleden overstapte van interactieve kaartervaringen naar statische kaartafbeeldingen. Dit werd gedaan als een kostenbesparende methode. In Azure Maps is het meestal rendabeler om het interactieve kaartbesturingselement in de Web SDK te gebruiken. De kosten voor interactieve kaartbeheer zijn gebaseerd op het aantal tegelbelastingen. Kaarttegels in Azure Maps zijn groot. Vaak duurt het slechts een paar tegels om dezelfde kaartweergave opnieuw te maken als een statische kaart. Kaarttegels worden automatisch in de cache opgeslagen door de browser. Als zodanig genereert het interactieve kaartbesturingselement vaak een fractie van een transactie bij het reproduceren van een statische kaartweergave. Pannen en zoomen zal meer tegels laden; Er zijn echter opties in het kaartbesturingselement om dit gedrag uit te schakelen. Het interactieve kaartbesturingselement biedt ook veel meer visualisatieopties dan de statische kaartservices.

### <a name="marker-url-parameter-format-comparison"></a>Vergelijking van parameternotatie van markeringsURL

**Voor: Google Maps**

Markeringen toevoegen met `markers` de parameter in de URL. De `markers` parameter neemt in een stijl en een lijst van locaties die moeten worden weergegeven op de kaart met die stijl zoals hieronder weergegeven:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Als u extra stijlen `markers` wilt toevoegen, gebruikt u de parameters aan de URL met een andere stijl en een andere set locaties.

Geef markeringslocaties op met de indeling 'breedtegraad, lengtegraad'.

Voeg markeringsstijlen `optionName:value` toe aan de indeling,\|waarbij meerdere stijlen worden gescheiden\|door pipe () tekens zoals deze optie "optionName1:value1 optionName2:value2". Let op: de optienamen en -waarden worden gescheiden met een dubbele punt (:). Gebruik de volgende namen van stijloptie om markeringen te stylen in Google Maps:

- `color`– De kleur van het standaardmarkeringspictogram. Kan een 24-bits hex-kleur ()`0xrrggbb`of een van de volgende waarden zijn; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Een alfanumeriek teken in hoofdletters dat boven aan het pictogram moet worden weergegeven.
- `size`- De grootte van de markering. Kan `tiny`worden `mid`, `small`, of .

Gebruik de volgende namen voor stijlopties voor aangepaste pictogrammen in Google Maps:

- `anchor`– Hiermee geeft u op hoe u de pictogramafbeelding uitlijnt op de coördinaat. Kan een pixelwaarde (x,y) of een van de volgende waarden zijn; `top`, `bottom` `left`, `right` `center`, `topleft` `topright`, `bottomleft`, `bottomright`, , of .
- `icon`– Een URL die naar de afbeelding van het pictogram wijst.

Laten we bijvoorbeeld een rode, middelgrote markering toevoegen aan de kaart bij lengte: -110, breedtegraad: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps-markering](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Na: Azure Maps**

Voeg markeringen toe aan een statische `pins` kaartafbeelding door de parameter in de URL op te geven. Geef net als Google Maps een stijl en een lijst met locaties in de parameter op. De `pins` parameter kan meerdere keren worden opgegeven om markeringen met verschillende stijlen te ondersteunen.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Als u extra stijlen `pins` wilt gebruiken, voegt u extra parameters toe aan de URL met een andere stijl en een reeks locaties.

In Azure Maps moet de pinlocatie in de indeling 'lengtebreedte' staan. Google Maps maakt gebruik van "latitude,longitude"-indeling. Een spatie, geen komma, scheidt lengte en breedtegraad in de Azure Maps-indeling.

Hiermee `iconType` geeft u het type pin aan dat moet worden gemaakt. Deze kan de volgende waarden hebben:

- `default`– Het standaardpictogram voor pin.
- `none`– Er wordt geen pictogram weergegeven, alleen labels worden weergegeven.
- `custom`– Hiermee geeft u een aangepast pictogram op dat moet worden gebruikt. Een URL die naar de pictogramafbeelding wijst, `pins` kan aan het einde van de parameter worden toegevoegd na de locatiegegevens van de pin.
- `{udid}`– Een unieke gegevens-id (UDID) voor een pictogram dat is opgeslagen in het Azure Maps Data Storage-platform.

Voeg pinstijlen `optionNameValue` toe aan de indeling. Scheid meerdere stijlen met\|de pipe ( ) tekens. Bijvoorbeeld: `iconType|optionName1Value1|optionName2Value2`. De optienamen en -waarden worden niet gescheiden. Gebruik de volgende stijloptienamen om markeringen te stylen:

- `al`– Hiermee geeft u de dekking (alfa) van de markering op. Kies een getal tussen 0 en 1.
- `an`– Hiermee geeft u het pinanker op. Geef X- en y-pixelwaarden op in de 'x y'-indeling.
- `co`– De kleur van de pin. Geef een 24-bits hex-kleur op: `000000` naar `FFFFFF`.
- `la`– Hiermee geeft u het labelanker op. Geef X- en y-pixelwaarden op in de 'x y'-indeling.
- `lc`– De kleur van het label. Geef een 24-bits hex-kleur op: `000000` naar `FFFFFF`.
- `ls`– De grootte van het label in pixels. Kies een getal groter dan 0.
- `ro`– Een waarde in graden om het pictogram te draaien. Kies een getal tussen -360 en 360.
- `sc`– Een schaalwaarde voor het pinpictogram. Kies een getal groter dan 0.

Geef labelwaarden op voor elke pinlocatie. Deze aanpak is efficiënter dan het toepassen van één labelwaarde op alle markeringen in de lijst met locaties. De labelwaarde kan een tekenreeks van meerdere tekens zijn. Wikkel de tekenreeks met enkele aanhalingstekens om ervoor te zorgen dat deze niet wordt verward als een stijl- of locatiewaarde.

Laten we een rood`FF0000`( ) standaardpictogram toevoegen, met het label "Space Needle", hieronder geplaatst (15 50). Het pictogram is op lengte: -122.349300, breedtegraad: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps-markering](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Voeg drie pinnen toe met de labelwaarden '1', '2' en '3':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Meerdere markeringen voor Azure Maps](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Vergelijking van de parameterformaat van padURL

**Voor: Google Maps**

Voeg lijnen en veelhoek toe `path` aan een statische kaartafbeelding met behulp van de parameter in de URL. De `path` parameter neemt in een stijl en een lijst van locaties die op de kaart moeten worden weergegeven, zoals hieronder wordt weergegeven:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Gebruik extra stijlen `path` door extra parameters toe te voegen aan de URL met een andere stijl en een andere set locaties.

Padlocaties worden opgegeven `latitude1,longitude1|latitude2,longitude2|…` met de indeling. Paden kunnen worden gecodeerd of adressen voor punten bevatten.

Voeg padstijlen `optionName:value` toe met de indeling,\|scheid meerdere stijlen door de pipe () tekens. En, aparte optie namen en waarden met een dubbele punt (:). Zoals dit: `optionName1:value1|optionName2:value2`. De volgende stijloptienamen kunnen worden gebruikt om paden in Google Maps te stylen:

- `color`– De kleur van het pad of de veelhoekomtrek. Kan een 24-bits hex`0xrrggbb`kleur ( ), een 32-bits hex kleur (`0xrrggbbbaa`) of een van de volgende waarden: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `fillColor`– De kleur waarmee u het padgebied moet vullen (veelhoek). Kan een 24-bits hex`0xrrggbb`kleur ( ), een 32-bits hex kleur (`0xrrggbbbaa`) of een van de volgende waarden: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
- `geodesic`– Geeft aan of het pad een lijn moet zijn die de kromming van de aarde volgt.
- `weight`– De dikte van de padlijn in pixels.

Voeg een rode lijndekking en pixeldikte toe aan de kaart tussen de coördinaten, in de PARAMETER URL. Voor het onderstaande voorbeeld heeft de lijn een dekking van 50% en een dikte van vier pixels. De coördinaten zijn lengte: -110, breedtegraad: 45 en lengte: -100, breedtegraad: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps-polylijn](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Na: Azure Maps**

Voeg lijnen en veelhoeken toe aan een `path` statische kaartafbeelding door de parameter in de URL op te geven. Geef net als Google Maps een stijl en een lijst met locaties in deze parameter op. Geef `path` de parameter meerdere keren op om meerdere cirkels, lijnen en veelhoeken met verschillende stijlen weer te geven.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Als het gaat om padlocaties, vereist Azure Maps dat de coördinaten in de indeling 'lengtebreedte' zijn. Google Maps maakt gebruik van "latitude,longitude"-indeling. Een spatie, geen komma, scheidt lengte en breedtegraad in de Azure Maps-indeling. Azure Maps biedt geen ondersteuning voor gecodeerde paden of adressen voor punten. Upload grotere gegevenssets als een GeoJSON-bestand naar de Azure Maps Data Storage API zoals [hier](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)is gedocumenteerd.

Padstijlen toevoegen `optionNameValue` aan de indeling. Scheid meerdere stijlen\|per pijp ( `optionName1Value1|optionName2Value2`) tekens, zoals deze . De optienamen en -waarden worden niet gescheiden. Gebruik de volgende stijloptienamen om paden in Azure Maps te stylen:

- `fa`- De opvulkleurdekking (alfa) die wordt gebruikt bij het renderen van veelhoeken. Kies een getal tussen 0 en 1.
- `fc`- De vulkleur die wordt gebruikt om het gebied van een veelhoek weer te geven.
- `la`– De lijnkleurdekking (alfa) die wordt gebruikt bij het renderen van lijnen en de omtrek van veelhoeken. Kies een getal tussen 0 en 1.
- `lc`– De lijnkleur die wordt gebruikt om lijnen en de omtrek van veelhoeken weer te geven.
- `lw`– De breedte van de lijn in pixels.
- `ra`– Hiermee geeft u een cirkelstraal in meters op.

Voeg een rode lijndekking en pixeldikte toe tussen de coördinaten in de PARAMETER URL. Voor het onderstaande voorbeeld heeft de lijn 50% dekking en een dikte van vier pixels. De coördinaten hebben de volgende waarden: lengte: -110, breedtegraad: 45 en lengte: -100, breedtegraad: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps-polylijn](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Een afstandsmatrix berekenen

Azure Maps biedt de API voor afstandsmatrix. Gebruik deze API om de reistijden en de afstanden tussen een set locaties te berekenen, met een afstandsmatrix. Het is vergelijkbaar met de afstand matrix API in Google Maps.

- [**Routematrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): Asynchroon berekent de reistijden en afstanden voor een reeks oorsprongen en bestemmingen. Ondersteunt maximaal 700 cellen per aanvraag. Dat is het aantal oorsprong vermenigvuldigd met het aantal bestemmingen. Met die beperking in het achterhoofd zijn voorbeelden van mogelijke matrixafmetingen: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Een verzoek om de api voor afstandsmatrix kan alleen worden gedaan met behulp van een POST-verzoek met de oorsprong som- en bestemmingsgegevens in de hoofdtekst van het verzoek. Bovendien vereist Azure Maps dat alle oorsprong en bestemmingen worden coördinaat. Adressen moeten eerst geocoded zijn.

In deze tabel wordt de API-parameters van Google Maps vergeleken met de vergelijkbare Azure Maps API-parameters.

| Parameter Google Maps API      | Vergelijkbare Azure Maps API-parameter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– geef in de post-aanvraaginstantie aan als GeoJSON. |
| `key`                          | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– geef in de post-aanvraaginstantie aan als GeoJSON.  |
| `region`                       | *N/A* - Deze functie is geocoding gerelateerd. Gebruik `countrySet` de parameter wanneer u de geocoderings-API van Azure Maps gebruikt. |
| `traffic_model`                | *N/A* - Kan alleen opgeven als verkeersgegevens moeten worden gebruikt met de `traffic` parameter. |
| `transit_mode`                 | *N/A* - Transit-gebaseerde afstandmatrices worden momenteel niet ondersteund.  |
| `transit_routing_preference`   | *N/A* - Transit-gebaseerde afstandmatrices worden momenteel niet ondersteund.  |
| `units`                        | *N/A* – Azure Maps gebruikt alleen het metrische systeem. |

> [!TIP]
> Alle geavanceerde routeringsopties die beschikbaar zijn in de Azure Maps-routerings-API worden ondersteund in de Azure Maps-matrixAPI voor afstandsmatrix. Geavanceerde routeringsopties zijn: truckrouting, motorspecificaties, enzovoort.

Bekijk de [aanbevolen procedures voor routeringsdocumentatie.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Een tijdzone krijgen

Azure Maps biedt een API voor het ophalen van de tijdzone van een coördinaat. De AZURE Maps-tijdzone-API is vergelijkbaar met de tijdzone-API in Google Maps:

- [**Tijdzone op coördinaat**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Geef een coördinaat op en ontvang de tijdzonegegevens van de coördinaat.

In deze tabel wordt de API-parameters van Google Maps vergeleken met de vergelijkbare API-parameters in Azure Maps.

| Parameter Google Maps API | Vergelijkbare Azure Maps API-parameter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Zie ook de [documentatie Verificatie met Azure Maps.](azure-maps-authentication.md)       |
| `language`                  | `language`– Bekijk de documentatie [over ondersteunde talen.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Naast deze API biedt Azure Maps een aantal tijdzone-API's. Deze API's zetten de tijd om op basis van de namen of de iD's van de tijdzone:

- [**Tijdzone op id:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)geeft als resultaat huidige, historische en toekomstige tijdzone-informatie voor de opgegeven IANA-tijdzone-id.
- [**Time zone Enum IANA:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)geeft als resultaat een volledige lijst met IANA-tijdzone-id's. Updates van de IANA-service worden binnen één dag in het systeem weergegeven.
- [**Time zone Enum Windows:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)geeft als resultaat een volledige lijst met Windows Time Zone-id's.
- [**IANA-versie**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)voor tijdzone: geeft als resultaat het huidige IANA-versienummer dat wordt gebruikt door Azure Maps.
- [**Tijdzone Windows naar IANA:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)geeft als resultaat een overeenkomstige IANA-id, met een geldige Windows Time Zone-id. Meerdere IANA-id's kunnen worden geretourneerd voor één Windows-id.

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps biedt clientbibliotheken voor de volgende programmeertalen:

- JavaScript- [documentation](how-to-use-services-module.md) \| [NPM package](https://www.npmjs.com/package/azure-maps-rest)

Deze open-source clientbibliotheken zijn voor andere programmeertalen:

- .NET Standard 2.0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet pakket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Aanvullende bronnen

Hieronder volgen aanvullende documentatie en bronnen voor de Azure Maps REST-services.

- [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md)
- [Zoeken naar een adres](how-to-search-for-address.md)
- [Aanbevolen procedures voor routering](how-to-use-best-practices-for-routing.md)
- [Referentiedocumentatie voor Azure Maps REST Service API](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps REST-services.

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de zoekservice](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van de routeringsservice](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [De servicesmodule (Web SDK) gebruiken](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
