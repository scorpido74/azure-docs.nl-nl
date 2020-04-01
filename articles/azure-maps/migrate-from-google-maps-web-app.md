---
title: 'Zelfstudie: Een web-app migreren vanuit Google Maps | Microsoft Azure Maps'
description: Een web-app migreren van Google Maps naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77914075"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Een web-app migreren vanuit Google Maps

De meeste webapps, die Google Maps gebruiken, gebruiken de Google Maps V3 JavaScript SDK. De Azure Maps Web SDK is de geschikte Azure-gebaseerde SDK om naar te migreren. Met de Azure Maps Web SDK u interactieve kaarten aanpassen met uw eigen inhoud en afbeeldingen. U uw app uitvoeren op zowel web- als mobiele toepassingen. Dit besturingselement maakt gebruik van WebGL, zodat u grote gegevenssets kunt weergeven met hoge prestaties. Ontwikkel met deze SDK met JavaScript of TypeScript.

Als u een bestaande webtoepassing migreert, controleert u of deze een open-source mapbesturingselementbibliotheek gebruikt. Voorbeelden van open-source map control library zijn: Cesium, Leaflet en OpenLayers. U uw toepassing nog steeds migreren, zelfs als deze gebruikmaakt van een open-source kaartbeheerbibliotheek en u de Azure Maps Web SDK niet wilt gebruiken. Sluit in dat geval uw toepassing aan op de tegelservices van Azure Maps [(satelliettegels voor](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)[wegtegels).](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| De volgende punten details over het gebruik van Azure Maps in een aantal veelgebruikte open-source map controlebibliotheken.

- Cesium - Een 3D-kaartcontrole voor het web. [Documentatie van het](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [codevoorbeeld](https://cesiumjs.org/)
- Folder - Lichtgewicht 2D-kaartbediening voor het web. [Documentatie van het](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [codevoorbeeld](https://leafletjs.com/)
- OpenLayers - Een 2D-kaartbesturingselement voor het web dat projecties ondersteunt. [Documentatie van het](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [codevoorbeeld](https://openlayers.org/)

## <a name="key-features-support"></a>Ondersteuning voor belangrijke functies

De tabel bevat belangrijke API-functies in de Google Maps V3 JavaScript SDK en de ondersteunde API-functie in de Azure Maps Web SDK.

| Google Maps-functie     | Azure Maps Web SDK-ondersteuning |
|-------------------------|:--------------------------:|
| Markeringen                 | ✓                          |
| Clustering van markering       | ✓                          |
| Polylines & Polygonen    | ✓                          |
| Gegevenslagen             | ✓                          |
| Grondoverlays         | ✓                          |
| Warmtekaarten               | ✓                          |
| Tegellagen             | ✓                          |
| KML-laag               | ✓                          |
| Tekenprogramma's           | ✓                          |
| Geocoderservice        | ✓                          |
| Routebeschrijvingsdienst      | ✓                          |
| Afstandsmatrixservice | ✓                          |
| Hoogteservice       | Gepland                    |

## <a name="notable-differences-in-the-web-sdks"></a>Opmerkelijke verschillen in het web SDKs

Hieronder volgen enkele belangrijke verschillen tussen de Google Maps- en Azure Maps-web-SDK's, om rekening mee te houden:

- Naast het bieden van een gehost eindpunt voor toegang tot de Azure Maps Web SDK, is er een NPM-pakket beschikbaar. Sluit het Web SDK-pakket in in apps. Zie deze [documentatie](how-to-use-map-control.md)voor meer informatie. Dit pakket bevat ook TypeScript-definities.
- U moet eerst een instantie van de klasse Kaart maken in Azure Maps. Wacht tot `ready` de `load` kaarten of gebeurtenis worden afvuurd voordat de kaart programmatisch wordt interactie. Deze volgorde zorgt ervoor dat alle kaartbronnen zijn geladen en klaar zijn om toegankelijk te zijn.
- Beide platforms maken gebruik van een soortgelijk tegelsysteem voor de basiskaarten. De tegels in Google Maps hebben een afmeting van 256 pixels. De tegels in Azure Maps zijn echter 512 pixels in dimensie. Als u dezelfde kaartweergave wilt krijgen in Azure Maps als Google Maps, trekt u het zoomniveau van Google Maps af op nummer één in Azure Maps.
- Coördinaten in Google Maps worden aangeduid als "breedtegraad, lengtegraad", terwijl Azure Maps gebruik maakt van "lengtegraad, breedtegraad". De Azure Maps-indeling is `[x, y]`afgestemd op de standaard, die wordt gevolgd door de meeste GIS-platforms.
- Shapes in de Azure Maps Web SDK zijn gebaseerd op het GeoJSON-schema. Helperklassen worden weergegeven via de [naamruimte *atlas.data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Er is ook de [*atlas. Vormklasse.*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) Gebruik deze klasse om GeoJSON-objecten te verpakken, om het eenvoudig te maken om de gegevens op een houdbare manier bij te werken en te onderhouden.
- Coördinaten in Azure Maps worden gedefinieerd als Positieobjecten. Een coördinaat wordt `[longitude,latitude]`opgegeven als een getalarray in de notatie . Of het is opgegeven met behulp van nieuwe atlas.data.Position (lengtegraad, breedtegraad).
    > [!TIP]
    > De klasse Positie heeft een statische helpermethode voor het importeren van coördinaten die in de indeling 'breedtebreedte, lengtegraad' zijn. De [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) methode kan vaak `new google.maps.LatLng` worden vervangen door de methode in Google Maps code.
- In plaats van stylinginformatie op te geven voor elke vorm die aan de kaart wordt toegevoegd, scheidt Azure Maps stijlen van de gegevens. Gegevens worden opgeslagen in een gegevensbron en zijn verbonden met renderinglagen. Azure Maps-code gebruikt gegevensbronnen om de gegevens weer te geven. Deze aanpak biedt een verbeterd prestatievoordeel. Bovendien ondersteunen veel lagen gegevensgestuurde styling waarbij bedrijfslogica kan worden toegevoegd aan opties voor laagstijl. Deze ondersteuning verandert de manier waarop afzonderlijke vormen worden weergegeven binnen een laag op basis van eigenschappen die in de vorm zijn gedefinieerd.

## <a name="web-sdk-side-by-side-examples"></a>Voorbeelden van Web SDK naast elkaar

Deze verzameling bevat codevoorbeelden voor elk platform en elk voorbeeld heeft betrekking op een gemeenschappelijke use case. Het is bedoeld om u te helpen uw webtoepassing te migreren van Google Maps V3 JavaScript SDK naar de Azure Maps Web SDK. Codevoorbeelden met betrekking tot webtoepassingen worden geleverd in JavaScript. Azure Maps biedt echter ook TypeScript-definities als extra optie via een [NPM-module.](how-to-use-map-control.md)

### <a name="load-a-map"></a>Een kaart laden

Beide SDK's hebben dezelfde stappen om een kaart te laden:

- Voeg een verwijzing toe naar de Map SDK.
- Voeg `div` een tag toe aan de hoofdtekst van de pagina, die fungeert als tijdelijke aanduiding voor de kaart.
- Maak een JavaScript-functie die wordt aangeroepen wanneer de pagina is geladen.
- Maak een instantie van de betreffende kaartklasse.

**Enkele belangrijke verschillen**

- Google maps vereist dat een accountsleutel moet worden opgegeven in de scriptverwijzing van de API. Verificatiereferenties voor Azure Maps zijn opgegeven als opties van de kaartklasse. Deze referentie kan een abonnementssleutel of Azure Active Directory-informatie zijn.
- Google Maps accepteert een callback-functie in de scriptverwijzing van de API, die wordt gebruikt om een initialisatiefunctie aan te roepen om de kaart te laden. Met Azure Maps moet de onload-gebeurtenis van de pagina worden gebruikt.
- Wanneer u verwijst `div` naar het element waarin de `Map` kaart wordt weergegeven, `id` vereist de klasse `HTMLElement` in Azure Maps alleen de waarde terwijl Google Maps een object vereist.
- Coördinaten in Azure Maps worden gedefinieerd als Positieobjecten, die kunnen `[longitude, latitude]`worden opgegeven als een eenvoudige getalarray in de indeling .
- Het zoomniveau in Azure Maps is één niveau lager dan het zoomniveau in Google Maps. Deze discrepantie komt doordat het verschil in de grootte van het betegelingssysteem van de twee platforms.
- Azure Maps voegt geen navigatiebesturingselementen toe aan het kaartcanvas. Standaard heeft een kaart dus geen zoomknoppen en kaartstijlknoppen. Er zijn echter controleopties voor het toevoegen van een kaartstijlkiezer, zoomknoppen, kompas- of rotatiebediening en een toonhoogtebesturingselement.
- Er wordt een gebeurtenishandler toegevoegd `ready` in Azure Maps om de gebeurtenis van de kaartinstantie te controleren. Deze gebeurtenis wordt gestart wanneer de kaart klaar is met het laden van de WebGL-context en alle benodigde resources. Voeg alle code die u wilt uitvoeren nadat de kaart het laden heeft voltooid, toe aan deze gebeurtenishandler.

De onderstaande basisvoorbeelden gebruiken Google Maps om een kaart te laden die gecentreerd is over New York op coördinaten. De lengtegraad: -73.985, breedtegraad: 40.747, en de kaart is op zoomniveau van 12.

**Voor: Google Maps**

Een Google Map weergeven die gecentreerd en ingezoomd is op een locatie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als u deze code in een browser uitvoert, wordt een kaart weergegeven die lijkt op de volgende afbeelding:

<center>

![Eenvoudige Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Na: Azure Maps**

Laad een kaart met dezelfde weergave in Azure Maps, samen met een kaartstijlbesturingselement en zoomknoppen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als u deze code in een browser uitvoert, wordt een kaart weergegeven die lijkt op de volgende afbeelding:

<center>

![Eenvoudige Azure-kaarten](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Vind gedetailleerde documentatie over het instellen en gebruiken van het Azure Maps-kaartbesturingselement in een web-app door [hier](how-to-use-map-control.md)te klikken .

> [!NOTE]
> In tegenstelling tot Google Maps vereist Azure Maps geen eerste centrum en een zoomniveau om de kaart te laden. Als deze informatie niet wordt verstrekt bij het laden van de kaart, proberen Azure-kaarten de plaats van de gebruiker te bepalen. Het zal het centrum en zoom de kaart daar.

**Aanvullende bronnen:**

- Azure Maps biedt ook navigatiebesturingselementen voor het roteren en pitchen van de kaartweergave, zoals [hier](map-add-controls.md)is gedocumenteerd.

### <a name="localizing-the-map"></a>De kaart lokaliseren

Als je doelgroep verspreid is over meerdere landen of verschillende talen spreekt, is lokalisatie belangrijk.

**Voor: Google Maps**

Als u Google Maps wilt lokaliseren, voegt u taal- en regioparameters toe.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Hier is een voorbeeld van Google Maps met de taal ingesteld op "fr-FR".

<center>

![Lokalisatie van Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Na: Azure Maps**

Azure Maps biedt twee verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de globale *atlas* naamruimte. Dit zal resulteren in alle kaartbeheerinstanties in uw app die in gebreke blijven bij deze instellingen. In de volgende plaats de taal frans ("fr-FR") en de regionale weergave op "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

De tweede optie is om deze informatie door te geven aan de kaartopties bij het laden van de kaart. Zoals dit:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Met Azure Maps is het mogelijk om meerdere kaartinstanties op dezelfde pagina te laden met verschillende taal- en regio-instellingen. Het is ook mogelijk om deze instellingen in de kaart bij te werken nadat deze is geladen. 

Zoek een gedetailleerde lijst met [ondersteunde talen](supported-languages.md) in Azure Maps.

Hier is een voorbeeld van Azure Maps met de taal ingesteld op "fr" en de gebruikersregio ingesteld op "fr-FR".

<center>

![Lokalisatie van Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>De kaartweergave instellen

Dynamische kaarten in zowel Azure als Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties. Roep hiervoor de juiste functies in JavaScript aan. De voorbeelden laten zien hoe u de kaart satellietbeelden van de kaart weergeven, de kaart over een locatie centreren en het zoomniveau wijzigen in 15 in Google Maps. De volgende locatiecoördinaten worden gebruikt: lengte: -111.0225 en breedtegraad: 35.0272.

> [!NOTE]
> Google Maps gebruikt tegels met afmetingen van 256 pixels, terwijl Azure Maps een grotere tegel van 512 pixels gebruikt. Azure Maps vereist dus minder netwerkaanvragen om hetzelfde kaartgebied te laden als Google Maps. Vanwege de manier waarop tegelpiramides werken in kaartbesturingselementen, moet u het zoomniveau dat in Google Maps wordt gebruikt, aftrekken door het nummer één bij het gebruik van Azure Maps. Deze rekenbewerking zorgt ervoor dat grotere tegels in Azure Maps hetzelfde kaartgebied weergeven als in Google Maps,

**Voor: Google Maps**

Verplaats het kaartbesturingselement `setOptions` van Google Maps met de methode. Met deze methode u het midden van de kaart en een zoomniveau opgeven.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps-setweergave](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Na: Azure Maps**

Wijzig in Azure Maps de `setCamera` kaartpositie met de methode `setStyle` en wijzigt de kaartstijl met de methode. De coördinaten in Azure Maps zijn in de indeling 'lengtegraad, breedtegraad' en de zoomwaarde wordt met één afgetrokken.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Weergave azure Maps-set](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Aanvullende bronnen:**

- [Een kaartstijl kiezen](choose-map-style.md)
- [Ondersteunde kaartstijlen](supported-map-styles.md)

### <a name="adding-a-marker"></a>Een markering toevoegen

In Azure Maps zijn er meerdere manieren waarop puntgegevens op de kaart kunnen worden weergegeven:

- **HTML-markeringen** – Hiermee worden punten weergegeven met traditionele DOM-elementen. HTML-markeringen ondersteunen slepen.
- **Symboollaag** : hiermee worden punten weergegeven met een pictogram of tekst in de WebGL-context.
- **Bubble Layer** – Hiermee worden punten als cirkels op de kaart weergegeven. De stralen van de cirkels kunnen worden geschaald op basis van eigenschappen in de gegevens.

Symboollagen en bellenlagen weergeven binnen de WebGL-context. Beide lagen kunnen grote sets punten op de kaart weergeven. Deze lagen vereisen dat gegevens worden opgeslagen in een gegevensbron. Gegevensbronnen en renderinglagen moeten aan de `ready` kaart worden toegevoegd nadat de gebeurtenis is afgevuurd. HTML-markeringen worden weergegeven als DOM-elementen op de pagina en ze gebruiken geen gegevensbron. Hoe meer DOM-elementen een pagina heeft, hoe langzamer de pagina wordt. Als het renderen van meer dan een paar honderd punten op een kaart, is het raadzaam om een van de rendering lagen in plaats daarvan te gebruiken.

Laten we een markering toevoegen aan de kaart met het nummer 10 bedekt als een label. Gebruik lengte: -0,2 en breedtegraad: 51,5.

**Voor: Google Maps**

Voeg met Google Maps markeringen toe `google.maps.Marker` aan de kaart met de klasse en geef de kaart op als een van de opties.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps-markering](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Na: Azure Maps met HTML-markeringen**

Gebruik in Azure Maps HTML-markeringen om een punt op de kaart weer te geven. HTML-markeringen worden aanbevolen voor apps die slechts een klein aantal punten op de kaart hoeven weer te geven. Als u een HTML-markering wilt `atlas.HtmlMarker` gebruiken, maakt u een instantie van de klasse. Stel de opties voor tekst en positie in `map.markers.add` en voeg de markering met de methode toe aan de kaart.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![HTML-markering Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Na: Azure Maps met een symboollaag**

Voeg de gegevens voor een symboollaag toe aan een gegevensbron. Voeg de gegevensbron aan de laag toe. Bovendien moeten de gegevensbron en -laag aan `ready` de kaart worden toegevoegd nadat de gebeurtenis is geactiveerd. Als u een unieke tekstwaarde boven een symbool wilt weergeven, moet de tekstinformatie worden opgeslagen als eigenschap van het gegevenspunt. De eigenschap moet worden `textField` verwezen in de optie van de laag. Deze aanpak is een beetje meer werk dan het gebruik van HTML-markers, maar het betere prestaties.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Symboollaag Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Aanvullende bronnen:**

- [Een gegevensbron maken](create-data-source-web-sdk.md)
- [Een symboollaag toevoegen](map-add-pin.md)
- [Een laag Bellen toevoegen](map-add-bubble-layer.md)
- [Gegevens van clusterpunt](clustering-point-data-web-sdk.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Opties voor pictogramsymboollagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Tekstoptie Symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-markeringsklasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-markeringsopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

U Aangepaste afbeeldingen gebruiken om punten op een kaart weer te geven. De kaart hieronder maakt gebruik van een aangepaste afbeelding om een punt op de kaart weer te geven. Het punt wordt weergegeven op breedtegraad: 51,5 en lengte: -0,2. Het anker compenseert de positie van de markering, zodat het punt van het punaisepictogram wordt uitgelijnd met de juiste positie op de kaart.

<center>

![gele punaise afbeelding](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_punaise.png</center>

**Voor: Google Maps**

Maak een aangepaste markering `Icon` door een `url` object op te geven dat de afbeelding bevat. Geef `anchor` een punt op om het punt van de punaiseafbeelding uit te lijnen met de coördinaat op de kaart. De ankerwaarde in Google Maps is relatief ten opzichte van de linkerbovenhoek van de afbeelding.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Aangepaste google maps-markering](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Na: Azure Maps met HTML-markeringen**

Als u een HTML-markering `string` `HTMLElement` wilt `htmlContent` aanpassen, geeft u een HTML of de optie van de markering door. Gebruik `anchor` de optie om de relatieve positie van de markering op te geven ten opzichte van de positiecoördinaat. Wijs een van de negen `anchor` gedefinieerde referentiepunten toe aan de optie. Die gedefinieerde punten zijn: "midden", "boven", "onder", "links", "rechts", "linksboven", "rechtsboven", "linksonder", "rechtsonder". De inhoud is standaard verankerd in het onderste midden van de html-inhoud. Als u het gemakkelijker wilt maken om `anchor` code van Google Maps te `pixelOffset` migreren, stelt u de optie in op 'linksboven' en gebruikt u de optie met dezelfde verschuiving die wordt gebruikt in Google Maps. De verschuivingen in Azure Maps worden in de tegenovergestelde richting van de verschuivingen in Google Maps verplaatst. Dus, vermenigvuldig de compensaties met min één.

> [!TIP]
> Voeg `pointer-events:none` als stijl toe aan de html-inhoud om het standaard sleepgedrag in Microsoft Edge uit te schakelen, waardoor een ongewenst pictogram wordt weergegeven.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Aangepaste HTML-markering azure maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Na: Azure Maps met een symboollaag**

Symboollagen in Azure Maps ondersteunen ook aangepaste afbeeldingen. Laad de afbeelding eerst naar de kaartbronnen en wijs deze toe met een unieke ID. Verwijs naar de afbeelding in de symboollaag. Gebruik `offset` de optie om de afbeelding uit te lijnen op het juiste punt op de kaart. Gebruik `anchor` de optie om de relatieve positie van het symbool op te geven ten opzichte van de positiecoördinaten. Gebruik een van de negen gedefinieerde referentiepunten. Deze punten zijn: "midden", "boven", "onder", "links", "rechts", "linksboven", "rechtsboven", "linksonder", "rechtsonder". De inhoud is standaard verankerd in het onderste midden van de html-inhoud. Als u het gemakkelijker wilt maken om `anchor` code van Google Maps te `offset` migreren, stelt u de optie in op 'linksboven' en gebruikt u de optie met dezelfde verschuiving die wordt gebruikt in Google Maps. De verschuivingen in Azure Maps worden in de tegenovergestelde richting van de verschuivingen in Google Maps verplaatst. Dus, vermenigvuldig de compensaties met min één.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Aangepaste pictogramsymboollaag azure maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Als u geavanceerde aangepaste punten wilt renderen, gebruikt u meerdere renderinglagen samen. Stel dat u meerdere punaises wilt hebben met hetzelfde pictogram op verschillende gekleurde cirkels. In plaats van een aantal afbeeldingen te maken voor elke kleuroverlay, voegt u een symboollaag toe bovenop een bellenlaag. Laat de punaises naar dezelfde gegevensbron verwijzen. Deze aanpak zal efficiënter zijn dan het creëren en onderhouden van een heleboel verschillende beelden.

**Aanvullende bronnen:**

- [Een gegevensbron maken](create-data-source-web-sdk.md)
- [Een symboollaag toevoegen](map-add-pin.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Opties voor pictogramsymboollagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Tekstoptie Symboollaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML-markeringsklasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML-markeringsopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Een polylijn toevoegen

Gebruik polylijnen om een lijn of pad op de kaart weer te geven. Laten we een gestippelde polylijn op de kaart maken.

**Voor: Google Maps**

De klasse Polyline accepteert een reeks opties. Geef een array van `path` coördinaten in de optie van de polyline.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps-polylijn](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Na: Azure Maps**

Polylijnen worden `LineString` `MultiLineString` aangeroepen of objecten genoemd. Deze objecten kunnen worden toegevoegd aan een gegevensbron en worden weergegeven met behulp van een lijnlaag. Voeg `LineString` toe aan een gegevensbron en `LineLayer` voeg de gegevensbron toe aan een gegevensbron om deze weer te geven.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps-polylijn](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Aanvullende bronnen:**

- [Lijnen toevoegen aan de kaart](map-add-line-layer.md)
- [Opties voor lijnlaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Azure Maps en Google Maps bieden vergelijkbare ondersteuning voor veelhoeken. Polygonen worden gebruikt om een gebied op de kaart weer te geven. In de volgende voorbeelden ziet u hoe u een veelhoek maakt die een driehoek vormt op basis van de middelste coördinaat van de kaart.

**Voor: Google Maps**

De klasse Polygon accepteert een reeks opties. Geef een array met `paths` coördinaten door aan de optie van de veelhoek.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps-veelhoek](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Na: Azure Maps**

Een `Polygon` of `MultiPolygon` een object toevoegen aan een gegevensbron. Maak het object op de kaart weer met lagen. Het gebied van een veelhoek weergeven met behulp van een veelhoeklaag. En, render de omtrek van een veelhoek met behulp van een lijnlaag.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Polygoon Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Aanvullende bronnen:**

- [Een veelhoek toevoegen aan de kaart](map-add-shape.md)
- [Een cirkel toevoegen aan de kaart](map-add-shape.md#add-a-circle-to-the-map)
- [Veelhoeklaagopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opties voor lijnlaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Een infovenster weergeven

Aanvullende informatie voor een entiteit kan op `google.maps.InfoWindow` de kaart worden weergegeven als een klasse in Google Maps. In Azure Maps kan deze functionaliteit `atlas.Popup` worden bereikt met behulp van de klasse. In de volgende voorbeelden wordt een markering aan de kaart toegevoegd. Wanneer op de markering wordt geklikt, wordt een infovenster of een pop-up weergegeven.

**Voor: Google Maps**

Maak een infovenster met `google.maps.InfoWindow` de constructor.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps pop-up](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Na: Azure Maps**

Laten we pop-up gebruiken om aanvullende informatie over de locatie weer te geven. Geef een `string` `HTMLElement` HTML of `content` object door aan de optie van de pop-up. Als u wilt, kunnen pop-ups onafhankelijk van elke vorm worden weergegeven. Pop-ups vereisen `position` dus een waarde die moet worden opgegeven. Geef `position` de waarde op. Als u een pop-up wilt weergeven, roept u de `open` methode aan en geeft u de `map` weergave door waarin de pop-up moet worden weergegeven.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Pop-up azure maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> U hetzelfde doen met een symbool-, bellen-, lijn- of veelhoeklaag door de gekozen laag door te geven aan de gebeurteniscode kaarten in plaats van een markering.

**Aanvullende bronnen:**

- [Een pop-up toevoegen](map-add-popup.md)
- [Pop-up met media-inhoud](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Pop-ups op shapes](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Pop-up opnieuw gebruiken met meerdere pins](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Pop-upklasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Pop-upopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Een GeoJSON-bestand importeren

Google Maps ondersteunt het laden en dynamisch `google.maps.Data` stylen van GeoJSON-gegevens via de klas. De functionaliteit van deze klasse sluit veel meer aan bij de datagestuurde styling van Azure Maps. Maar er is een belangrijk verschil. Met Google Maps geeft u een terugbelfunctie op. De bedrijfslogica voor het stylen van elke functie die afzonderlijk in de ui-thread is verwerkt. Maar in Azure Maps ondersteunen lagen het opgeven van gegevensgestuurde expressies als stylingopties. Deze expressies worden op rendertijd verwerkt op een afzonderlijke thread. De Azure Maps-aanpak verbetert de renderprestaties. Dit voordeel wordt opgemerkt wanneer grotere gegevenssets snel moeten worden weergegeven.

De volgende voorbeelden laden een GeoJSON-feed van alle aardbevingen in de afgelopen zeven dagen van de USGS. Aardbevingen gegevens wordt weergegeven als geschaalde cirkels op de kaart. De kleur en schaal van elke cirkel is gebaseerd op de `"mag"` omvang van elke aardbeving, die is opgeslagen in de eigenschap van elke functie in de gegevensset. Als de magnitude groter is dan of gelijk is aan vijf, zal de cirkel rood zijn. Als het groter of gelijk is aan drie, maar minder dan vijf, zal de cirkel oranje zijn. Als het minder dan drie is, zal de cirkel groen zijn. De straal van elke cirkel zal de exponentiële van de omvang vermenigvuldigd met 0,1.

**Voor: Google Maps**

Geef één terugbelfunctie `map.data.setStyle` op in de methode. Pas in de callback-functie bedrijfslogica toe op elke functie. Laad de GeoJSON-feed met de `map.data.loadGeoJson` methode.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Na: Azure Maps**

GeoJSON is het basisgegevenstype in Azure Maps. Importeer deze in een `datasource.importFromUrl` gegevensbron met behulp van de methode. Gebruik een bellenlaag. De bellaag biedt functionaliteit voor het renderen van geschaalde cirkels, op basis van de eigenschappen van de functies in een gegevensbron. In plaats van een callback-functie te hebben, wordt de bedrijfslogica omgezet in een expressie en doorgegeven aan de stijlopties. Expressies definiëren hoe de bedrijfslogica werkt. Expressies kunnen worden doorgegeven aan een andere thread en worden geëvalueerd aan de hand van de functiegegevens. Er kunnen meerdere gegevensbronnen en lagen worden toegevoegd aan Azure Maps, elk met verschillende bedrijfslogica. Met deze functie kunnen meerdere gegevenssets op verschillende manieren op de kaart worden weergegeven.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Aanvullende bronnen:**

- [Een symboollaag toevoegen](map-add-pin.md)
- [Een laag Bellen toevoegen](map-add-bubble-layer.md)
- [Gegevens van clusterpunt](clustering-point-data-web-sdk.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering van markering

Bij het visualiseren van veel gegevenspunten op de kaart kunnen punten elkaar overlappen. Overlapping maakt de kaart ziet er rommelig, en de kaart wordt moeilijk te lezen en te gebruiken. Clustering point data is het proces van het combineren van gegevenspunten die dicht bij elkaar liggen en deze op de kaart weergeven als één geclusterd gegevenspunt. Als de gebruiker inzoomt op de kaart, de clusters uit elkaar te halen in hun individuele gegevenspunten. Clustergegevenspunten om de gebruikerservaring te verbeteren en de prestaties in kaart te brengen.

In de volgende voorbeelden laadt de code een GeoJSON-feed van aardbevingsgegevens van de afgelopen week en voegt deze toe aan de kaart. Clusters worden weergegeven als geschaalde en gekleurde cirkels. De schaal en kleur van de cirkels is afhankelijk van het aantal punten dat ze bevatten.

> [!NOTE]
> Google Maps en Azure Maps gebruiken iets andere clusteralgoritmen. Als zodanig varieert soms de puntverdeling in de clusters.

**Voor: Google Maps**

Gebruik de markerclusterbibliotheek om markeringen te clusteren. Clusterpictogrammen zijn beperkt tot afbeeldingen, die de nummers één tot en met vijf als hun naam hebben. Ze worden gehost in dezelfde directory.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps-clustering](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Na: Azure Maps**

Gegevens toevoegen en beheren in een gegevensbron. Verbind gegevensbronnen en lagen en maak de gegevens weer. De `DataSource` klasse in Azure Maps biedt verschillende clusteropties.

- `cluster`– Hiermee wordt de gegevensbron aan de clusterpuntgegevens opgetaid.
- `clusterRadius`- De straal in pixels om punten samen te clusteren.
- `clusterMaxZoom`- Het maximale zoomniveau waarin clustering plaatsvindt. Als u meer inzoomt dan dit niveau, worden alle punten weergegeven als symbolen.
- `clusterProperties`- Definieert aangepaste eigenschappen die worden berekend met behulp van expressies op alle punten binnen elk cluster en toegevoegd aan de eigenschappen van elk clusterpunt.

Wanneer clustering is ingeschakeld, verzendt de gegevensbron geclusterde en niet-geclusterde gegevenspunten naar lagen voor rendering. De gegevensbron is in staat om honderdduizenden gegevenspunten te clusteren. Een geclusterd gegevenspunt heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Geeft aan of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | tekenreeks  | Een unieke id voor het cluster dat `getClusterExpansionZoom`kan `getClusterChildren`worden `getClusterLeaves` gebruikt met de DataSource en methoden. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | tekenreeks  | Een tekenreeks die de `point_count` waarde verkort als deze lang is. (4.000 wordt bijvoorbeeld 4K)  |

De `DataSource` klasse heeft de volgende helperfunctie voor toegang `cluster_id`tot aanvullende informatie over een cluster met behulp van de .

| Methode | Retourtype | Beschrijving |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;&lt;Array&lt;Feature Geometry, elke&gt; \| vorm&gt;&gt; | Hiermee haalt u de kinderen van het gegeven cluster op het volgende zoomniveau op. Deze kinderen kunnen een combinatie zijn van vormen en subclusters. De subclusters zijn functies met eigenschappen die overeenkomen met ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Beloftenummer&lt;&gt; | Berekent een zoomniveau waarop het cluster begint uit te breiden of uit elkaar te vallen. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;&lt;Array&lt;Feature Geometry, elke&gt; \| vorm&gt;&gt; | Hiermee worden alle punten in een cluster opgehaald. Stel `limit` de set in om een subset `offset` van de punten terug te geven en gebruik de pagina naar door de punten. |

Bij het renderen van geclusterde gegevens op de kaart u vaak het beste twee of meer lagen gebruiken. In het volgende voorbeeld worden drie lagen gebruikt. Een bellenlaag voor het tekenen van geschaalde gekleurde cirkels op basis van de grootte van de clusters. Een symboollaag om de clustergrootte als tekst weer te geven. En het maakt gebruik van een tweede symboollaag voor het renderen van de niet-geclusterde punten. Er zijn veel andere manieren om geclusterde gegevens weer te geven. Zie de gegevensdocumentatie van het [clusterpunt voor](clustering-point-data-web-sdk.md) meer informatie.

Importeer geoJSON-gegevens `importDataFromUrl` rechtstreeks met `DataSource` de functie in de klasse in de azure maps-kaart.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps-clustering](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Aanvullende bronnen:**

- [Een symboollaag toevoegen](map-add-pin.md)
- [Een laag Bellen toevoegen](map-add-bubble-layer.md)
- [Gegevens van clusterpunt](clustering-point-data-web-sdk.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Een warmtekaart toevoegen

Heat maps, ook wel puntdichtheidskaarten genoemd, zijn een type gegevensvisualisatie. Ze worden gebruikt om de dichtheid van gegevens weer te geven met behulp van een reeks kleuren. En ze worden vaak gebruikt om de gegevens "hot spots" op een kaart weer te geven. Heat maps zijn een geweldige manier om grote punt datasets weer te geven.

De volgende voorbeelden laden een GeoJSON-feed van alle aardbevingen in de afgelopen maand, van de USGS, en het maakt ze als een gewogen warmtekaart. De `"mag"` eigenschap wordt gebruikt als het gewicht.

**Voor: Google Maps**

Als u een heatmap wilt maken, `&libraries=visualization` laadt u de bibliotheek 'visualisatie' door toe te voegen aan de URL van het API-script. De heatmaplaag in Google Maps ondersteunt geoJSON-gegevens niet rechtstreeks. Download eerst de gegevens en zet deze om in een reeks gewogen gegevenspunten:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps-heatkaart](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Na: Azure Maps**

Laad de GeoJSON-gegevens in een gegevensbron en verbind de gegevensbron met een warmtekaartlaag. De eigenschap die wordt gebruikt voor het `weight` gewicht kan worden doorgegeven aan de optie met behulp van een expressie. Importeer geoJSON-gegevens rechtstreeks naar `importDataFromUrl` Azure `DataSource` Maps met de functie in de klasse.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps-heatmap](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Aanvullende bronnen:**

- [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)
- [Laagklasse Heatmap](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opties voor warmtekaartlaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Een tegellaag bedekken

Tegellagen in Azure Maps worden afbeeldingsoverlays genoemd in Google Maps. Met tegellagen u grote afbeeldingen bedekken die zijn opgesplitst in kleinere betegelde afbeeldingen, die overeenkomen met het betegelingssysteem kaarten. Deze benadering wordt vaak gebruikt om grote afbeeldingen of grote gegevenssets te bedekken.

De volgende voorbeelden overlay een weer radar tegel laag van Iowa Environmental Mesonet van Iowa State University.

**Voor: Google Maps**

In Google Maps kunnen tegellagen worden `google.maps.ImageMapType` gemaakt met behulp van de klasse.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Tegellaag van Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Na: Azure Maps**

Voeg een tegellaag op dezelfde manier toe aan de kaart als elke andere laag. Gebruik een opgemaakte URL met tijdelijke aanduidingen x, y en zoom; `{x}`, `{y}` `{z}` om de laag te vertellen waar toegang te krijgen tot de tegels. Azure Maps-tegellagen `{quadkey}` `{bbox-epsg-3857}`ondersteunen `{subdomain}` ook tijdelijke aanduidingen en tijdelijke aanduidingen.

> [!TIP]
> In Azure Maps kunnen lagen eenvoudig worden weergegeven onder andere lagen, inclusief basiskaartlagen. Vaak is het wenselijk om tegellagen onder de kaartlabels weer te geven, zodat ze gemakkelijk te lezen zijn. De `map.layers.add` methode neemt in een tweede parameter die de id van de laag waarin de nieuwe laag hieronder in te voegen. Als u een tegellaag onder de kaartlabels wilt invoegen, gebruikt u deze code:`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Tegellaag Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Tegelaanvragen kunnen worden `transformRequest` vastgelegd met de optie van de kaart. Hierdoor u kopteksten wijzigen of toevoegen aan het verzoek indien gewenst.

**Aanvullende bronnen:**

- [Tegellagen toevoegen](map-add-tile-layer.md)
- [Tegellaag, klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opties voor tegellagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Verkeer weergeven

Verkeersgegevens kunnen zowel Azure als Google maps worden bedekt.

**Voor: Google Maps**

Overlay verkeersgegevens op de kaart met behulp van de verkeerslaag.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps-verkeer](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Na: Azure Maps**

Azure Maps biedt verschillende opties voor het weergeven van verkeer. Geef verkeersincidenten, zoals wegafsluitingen en ongevallen, weer als pictogrammen op de kaart. Overlay verkeersstroom en kleur gecodeerde wegen op de kaart. De kleuren kunnen worden gewijzigd op basis van de geplaatste snelheidslimiet, ten opzichte van de normale verwachte vertraging, of absolute vertraging. Incidentgegevens in Azure Maps worden elke minuut bijgewerkt en er worden elke twee minuten gegevens bijgewerkt.

De gewenste waarden `setTraffic` toewijzen voor opties.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps-verkeer](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Als u op een van de verkeerspictogrammen in Azure Maps klikt, wordt aanvullende informatie weergegeven in een pop-up.

<center>

![Azure Maps-verkeersincident](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Aanvullende bronnen:**

- [Verkeer weergeven op de kaart](map-show-traffic.md)
- [Opties voor overlay verkeer](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Een grondoverlay toevoegen

Zowel Azure als Google Maps ondersteunen het overleggen van georeferenced afbeeldingen op de kaart. Georeferenced afbeeldingen bewegen en schalen terwijl u de kaart pannen en zoomen. In Google Maps worden gegeoreferenced afbeeldingen grondoverlays genoemd, terwijl ze in Azure Maps afbeeldingslagen worden genoemd. Ze zijn geweldig voor het bouwen van plattegronden, het overlopen van oude kaarten, of beelden van een drone.

**Voor: Google Maps**

Geef de URL op naar de afbeelding die u wilt bedekken en een selectiekader om de afbeelding op de kaart te binden. In dit voorbeeld wordt een kaartafbeelding van [Newark New Jersey uit 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) op de kaart gezet.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Als u deze code in een browser uitvoert, wordt een kaart weergegeven die lijkt op de volgende afbeelding:

<center>

![Google Maps-afbeeldingsoverlay](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Na: Azure Maps**

Gebruik `atlas.layer.ImageLayer` de klasse om georeferenced afbeeldingen te bedekken. Deze klasse vereist een URL naar een afbeelding en een set coördinaten voor de vier hoeken van de afbeelding. De afbeelding moet op hetzelfde domein worden gehost of cor's hebben ingeschakeld.

> [!TIP]
> Als u alleen informatie over noord-, zuid-, oost-, west- en rotatie hebt en u [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) geen coördinaten hebt voor elke hoek van de afbeelding, u de statische methode gebruiken.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Afbeeldingsoverlay voor Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Aanvullende bronnen:**

- [Een overlay aan een afbeelding toevoegen](map-add-image-layer.md)
- [Afbeeldingslaag, klasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>KML toevoegen aan de kaart

Zowel Azure als Google maps kunnen KML-, KMZ- en GeoRSS-gegevens op de kaart importeren en renderen. Azure Maps ondersteunt ook GPX-, GML-, ruimtelijke CSV-bestanden, GeoJSON, Bekende Tekst (WKT), Web Mapping Services (WMS), Web Mapping Tile Services (WMTS) en Web Feature Services (WFS). Azure Maps leest de bestanden lokaal in het geheugen en kan in de meeste gevallen veel grotere KML-bestanden verwerken. 

**Voor: Google Maps**


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Als u deze code in een browser uitvoert, wordt een kaart weergegeven die lijkt op de volgende afbeelding:

<center>

![Google Maps-afbeeldingsoverlay](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Na: Azure Maps**

In Azure Maps is GeoJSON het belangrijkste gegevensformaat dat in de webSDK wordt gebruikt, aanvullende ruimtelijke gegevensformaten kunnen eenvoudig worden geïntegreerd in het gebruik van de [ruimtelijke IO-module.](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/) Deze module heeft functies voor zowel het lezen als het schrijven van ruimtelijke gegevens en bevat ook een eenvoudige gegevenslaag die eenvoudig gegevens kan renderen uit een van deze ruimtelijke gegevensformaten. Als u de gegevens in een ruimtelijk gegevensbestand wilt lezen, geeft u `atlas.io.read` eenvoudig een URL of ruwe gegevens als tekenreeks of blob door in de functie. Dit retourneert alle ontleedgegevens uit het bestand die vervolgens aan de kaart kunnen worden toegevoegd. KML is een beetje complexer dan de meeste ruimtelijke data-formaat als het bevat veel meer styling informatie. De `SpatialDataLayer` klasse ondersteunt het renderen van de meerderheid van deze stijlen, maar pictogrammen afbeeldingen moeten worden geladen in de kaart voor het laden van de functie gegevens, en de grond overlays moeten worden toegevoegd als lagen aan de kaart afzonderlijk. Bij het laden van gegevens via een URL moet deze worden gehost op een eindpunt met CORs of moet een proxyservice als optie worden doorgegeven aan de leesfunctie. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Afbeeldingsoverlay voor Azure Maps](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Aanvullende bronnen:**

- [atlas.io.read- functie](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Aanvullende codevoorbeelden

Hieronder volgen enkele aanvullende codevoorbeelden met betrekking tot google maps-migratie:

- [Tekenprogramma's](map-add-drawing-toolbar.md)
- [Kaart beperken tot pannen met twee vingers](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Schuifwielzoom beperken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Een besturingselement op volledig scherm maken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Diensten:**

- [De Azure Maps-servicesmodule gebruiken](how-to-use-services-module.md)
- [Zoeken naar bezienswaardigheden](map-search-location.md)
- [Informatie ophalen van een coördinaat (omgekeerde geocode)](map-get-information-from-coordinate.md)
- [Routebeschrijving van A naar B](map-route.md)
- [Zoek Automatisch voorstellen met JQuery-gebruikersinterface](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 naar Azure Maps Web SDK-klassetoewijzing

De volgende bijlage bevat een kruisverwijzing van de veelgebruikte klassen in Google Maps V3 en de gelijkwaardige Azure Maps Web SDK.

### <a name="core-classes"></a>Kernklassen

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Kaart](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. Pop-upopties](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Positie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. Cameraopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opties voor camerabounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ServiceOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Stijlopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opties voor gebruikersinteractie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Overlayklassen

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. Htmlmarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbollayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Pictogramopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Tekstopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. Polygoonopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Zie [Een cirkel toevoegen aan de kaart](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. Tegellaag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Serviceklassen

De Azure Maps Web SDK bevat een servicesmodule, die afzonderlijk kan worden geladen. Met deze module worden de Azure Maps REST-services omwikkeld met een web-API en kan deze worden gebruikt in JavaScript-, TypeScript- en Node.js-toepassingen.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. Opties voor searchaddress](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchaddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchaddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOpties](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ZoekfuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opties voor SearchInsideGeometry](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opties voor zoeken in de buurt](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ZoekOPTIES](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. Opties voor routeroutedirections berekenen](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotheken

Bibliotheken voegen extra functionaliteit toe aan de kaart. Veel van deze bibliotheken bevinden zich in de kern SDK van Azure Maps. Hier zijn enkele gelijkwaardige klassen om te gebruiken in plaats van deze Google Maps-bibliotheken

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Tekenbibliotheek       | [Module voor tekenprogramma's](set-drawing-options.md) |
| Geometriebibliotheek      | [atlas.wiskunde](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Visualisatiebibliotheek | [Warmtekaartlaag](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Het kaartbesturingselement gebruiken](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [De servicesmodule gebruiken](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [De module tekengereedschappen gebruiken](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)

