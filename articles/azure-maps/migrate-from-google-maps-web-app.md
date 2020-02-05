---
title: 'Zelf studie: een web-app migreren vanuit Google Maps | Microsoft Azure kaarten'
description: Een web-app migreren van Google Maps naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989109"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Een web-app migreren vanuit Google Maps

De meeste web-apps, die gebruikmaken van Google Maps, maken gebruik van de Google Maps v3 java script SDK. De Azure Maps Web-SDK is de geschikte op Azure gebaseerde SDK voor het migreren naar. Met de Azure Maps Web-SDK kunt u interactieve kaarten aanpassen met uw eigen inhoud en beelden. U kunt uw app uitvoeren op internet-of mobiele toepassingen. Dit besturingselement maakt gebruik van WebGL, zodat u grote gegevenssets kunt weergeven met hoge prestaties. Ontwikkel met deze SDK met behulp van Java script of type script.

Als u een bestaande webtoepassing wilt migreren, controleert u of er een open-source kaart beheer bibliotheek wordt gebruikt. Voor beelden van een open-source kaart beheer bibliotheek zijn: cesium, bijsluiter en openlaag. Als dat niet het geval is en u niet de Azure Maps Web-SDK wilt gebruiken, is een andere optie voor het migreren van uw toepassing het gebruik van het open-source kaart besturings element te blijven gebruiken en verbinding te maken met de Azure Maps-tegel Services ([wegtegels](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [satelliet tegels](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Hieronder vindt u meer informatie over het gebruik van Azure Maps in een veelgebruikte open-source kaart beheer bibliotheken.

- Cesium: een 3D-kaart besturings element voor het web. [Documentatie](https://cesiumjs.org/) voor [code voorbeelden](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \|
- Bijsluiter: Lightweight 2D map Control voor het web. [Documentatie](https://leafletjs.com/) voor [code voorbeelden](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \|
- Openlaags: een 2D-kaart besturings element voor het web dat projecties ondersteunt. [Documentatie](https://openlayers.org/) voor [code voorbeelden](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \|

## <a name="key-features-support"></a>Ondersteuning van belang rijke functies

De volgende tabel bevat een overzicht van de belangrijkste API-functies in de Google Maps v3 java script SDK en de ondersteunde API-functie in de Azure Maps Web-SDK.

| Google Maps-functie     | Ondersteuning voor Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Markeringen                 | ✓                          |
| Markerings clusters       | ✓                          |
| Polylijnen & veelhoeken    | ✓                          |
| Gegevens lagen             | ✓                          |
| Wegdek bedekkingen         | ✓                          |
| Heatmap               | ✓                          |
| Tegel lagen             | ✓                          |
| KML-laag               | ✓                          |
| Tekenprogramma's           | ✓                          |
| Geocodeer-service        | ✓                          |
| Instructies service      | ✓                          |
| Service voor afstands matrix | ✓                          |
| Verhogings service       | Gepland                    |

## <a name="notable-differences-in-the-web-sdks"></a>Belang rijke verschillen in de Web-Sdk's

Hier volgen enkele van de belangrijkste verschillen tussen de Google Maps en Azure Maps Web-Sdk's, waarmee u rekening moet houden met:

- Naast het leveren van een gehost eind punt voor toegang tot de websdk van Azure Maps, is er ook een NPM-pakket beschikbaar voor het insluiten van de Web-SDK in apps, indien gewenst. Raadpleeg deze [documentatie](how-to-use-map-control.md)voor meer informatie. Dit pakket bevat ook type script definities.
- Nadat u een exemplaar van de kaart klasse hebt gemaakt in Azure Maps, moet uw code wachten tot de toewijzingen `ready` of `load` gebeurtenis worden gestart voordat de kaart wordt geactiveerd. Deze volg orde zorgt ervoor dat alle kaart bronnen zijn geladen en gereed zijn om te worden geopend.
- Beide platforms gebruiken een soortgelijk tegel systeem voor de basis kaarten, maar de tegels in Google Maps zijn 256 pixels in dimensie terwijl de tegels in Azure Maps 512 pixels in dimensie zijn. Om dezelfde kaart weergave te verkrijgen in Azure Maps als Google Maps, moet een zoom niveau dat in Google Maps wordt gebruikt, worden afgetrokken door één in Azure Maps.
- Coördinaten in Google Maps worden aangeduid als "breedte graad, lengte graad", terwijl Azure Maps "lengte graad, breedte graad" gebruikt. De Azure Maps-indeling wordt afgestemd op de standaard `[x, y]`, gevolgd door de meeste GIS-platforms.
- Shapes in de Web-SDK van Azure Maps zijn gebaseerd op het geojson-schema. Hulp klassen worden weer gegeven via de [naam ruimte *Atlas. data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Er is ook de [*Atlas.* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)Klasse van vorm. Deze klasse kan worden gebruikt om geojson-objecten af te ronden en ze gemakkelijk te kunnen bijwerken en onderhouden op een manier die kan worden verbonden met gegevens.
- Coördinaten in Azure Maps worden gedefinieerd als positie-objecten. Een coördinaat wordt opgegeven als een numerieke matrix in de indeling `[longitude, latitude]`, of wordt opgegeven met behulp van de nieuwe Atlas. data. Position (lengte graad, breedte graad).
    > [!TIP]
    > De klasse position heeft een statische hulp methode voor het importeren van coördinaten met de notatie "breedte graad, lengte graad". De methode [Atlas. data. position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) kan vaak worden vervangen door de methode `new google.maps.LatLng` in Google Maps-code.
- In plaats van opmaak gegevens op te geven voor elke vorm die wordt toegevoegd aan de kaart, Azure Maps opmaak profielen gescheiden van de gegevens. Gegevens worden opgeslagen in gegevens bronnen is verbonden met het renderen van lagen. Azure Maps code maakt gebruik van gegevens bronnen om de gegevens weer te geven. Deze aanpak biedt een verbeterd voor deel van prestaties. Daarnaast ondersteunen veel lagen gegevensgestuurde stijlen, waar bedrijfs logica kan worden toegevoegd aan laag stijl opties. Deze ondersteuning wijzigt hoe afzonderlijke vormen worden weer gegeven in een laag op basis van de eigenschappen die in de vorm zijn gedefinieerd.

## <a name="web-sdk-side-by-side-examples"></a>Web SDK-voor beelden naast elkaar

De volgende verzameling bevat code voorbeelden voor elk platform, elk met veelvoorkomende gebruiks voorbeelden. Het is bedoeld om u te helpen bij het migreren van uw webtoepassing vanuit Google Maps v3 java script SDK naar de Azure Maps Web-SDK. Code voorbeelden die betrekking hebben op webtoepassingen zijn opgenomen in Java script. Azure Maps biedt echter ook type script definities als een extra optie via een [NPM-module](how-to-use-map-control.md).

### <a name="load-a-map"></a>Een kaart laden

Het laden van een kaart volgt dezelfde reeks stappen in beide Sdk's:

- Voeg een verwijzing naar de kaart-SDK toe.
- Voeg een `div`-tag toe aan de hoofd tekst van de pagina, die als tijdelijke aanduiding voor de kaart fungeert.
- Maak een Java script-functie die wordt aangeroepen wanneer de pagina is geladen.
- Maak een instantie van de betreffende kaart klasse.

**Enkele belang rijke verschillen**

- Voor Google Maps moet een account sleutel worden opgegeven in de script verwijzing van de API. Verificatie referenties voor Azure Maps zijn opgegeven als opties van de kaart klasse. Deze referentie kan een abonnements sleutel of Azure Active Directory informatie zijn.
- Google Maps maakt gebruik van een call back-functie in de script verwijzing van de API, die wordt gebruikt om een initialisatie functie aan te roepen om de kaart te laden. Met Azure Maps moet u de gebeurtenis OnLoad van de pagina gebruiken.
- Bij het verwijzen naar het `div`-element waarin de kaart wordt weer gegeven, is voor de `Map` klasse in Azure Maps alleen de `id` waarde vereist terwijl Google Maps een `HTMLElement`-object vereist.
- Coördinaten in Azure Maps worden gedefinieerd als Position-objecten, die kunnen worden opgegeven als een eenvoudige nummer matrix in de notatie `[longitude, latitude]`.
- Het zoom niveau in Azure Maps is één niveau lager dan het zoom niveau in Google Maps. Dit verschil is het gevolg van het verschil in de grootte van het tegel systeem van de twee platformen.
- Azure Maps voegt geen navigatie besturings elementen aan het kaart doek toe. Daarom heeft een kaart standaard geen knoppen voor inzoomen en kaart stijlen. Er zijn echter besturings elementen voor het toevoegen van een kaart stijl kiezer, Zoom knoppen, kompas of rotatie besturings element en een besturings element pitch.
- Er wordt een gebeurtenis-handler toegevoegd in Azure Maps om de `ready` gebeurtenis van het exemplaar van de kaart te controleren. Deze gebeurtenis wordt gestart wanneer de toewijzing van de WebGL-context en alle benodigde resources is voltooid. Voeg een wille keurige code toe die u wilt uitvoeren, nadat de kaart is geladen, naar deze gebeurtenis-handler.

In de onderstaande voor beelden ziet u hoe u een basis kaart kunt laden die wordt gecentreerd over New York op coördinaten (lengte graad:-73,985, Latitude: 40,747) en op Zoom niveau 12 in Google Maps.

**Voor: Google Maps**

De volgende code is een voor beeld van het weer geven van een Google-kaart die is gecentreerd en ingezoomd op een locatie.

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

Als deze code wordt uitgevoerd in een browser, wordt een kaart weer gegeven met de volgende afbeelding:

<center>

![eenvoudige Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Na: Azure Maps**

De volgende code laat zien hoe u een kaart met dezelfde weer gave in Azure Maps kunt laden, samen met een besturings element voor kaart stijl en Zoom knoppen.

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

Als deze code wordt uitgevoerd in een browser, wordt een kaart weer gegeven met de volgende afbeelding:

<center>

![eenvoudige Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

[Hier](how-to-use-map-control.md)vindt u gedetailleerde documentatie over het instellen en gebruiken van het Azure Maps map-besturings element in een web-app.

> [!NOTE]
> In tegens telling tot Google Maps heeft Azure Maps geen start centrum nodig en wordt er een zoom niveau opgegeven voor het laden van de kaart. Als deze informatie niet wordt weer gegeven bij het laden van de kaart, probeert de kaart te bepalen in welke plaats de gebruiker zich bevindt en wordt de kaart daar gecentreerd en ingezoomd.

**Aanvullende bronnen:**

- Azure Maps biedt ook navigatie besturings elementen voor het draaien en verkopen van de kaart weergave zoals [hier](map-add-controls.md)wordt beschreven.

### <a name="localizing-the-map"></a>De kaart lokaliseren

Als uw doel groep is verspreid over meerdere landen of andere talen spreekt, is lokalisatie belang rijk.

**Voor: Google Maps**

Voor het lokaliseren van Google Maps worden de para meters taal en regio toegevoegd aan

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Hier volgt een voor beeld van Google Maps waarbij de taal is ingesteld op "fr-FR".

<center>

lokalisatie van Google Maps ![](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Na: Azure Maps**

Azure Maps biedt twee verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de wereld wijde *Atlas* -naam ruimte, wat resulteert in alle instanties van de kaart besturing in uw app die standaard worden ingesteld op deze instellingen. Hiermee stelt u de taal in op Frans ("fr-FR") en de regionale weer gave op ' auto ':

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

De tweede optie is om deze informatie door te geven aan de kaart opties bij het laden van de kaart zoals:

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
> Met Azure Maps kunt u meerdere toewijzings exemplaren op dezelfde pagina met andere taal-en regio-instellingen laden. Daarnaast is het ook mogelijk deze instellingen in de kaart bij te werken nadat deze is geladen. [Hier](supported-languages.md)vindt u een gedetailleerde lijst met ondersteunde talen in azure Maps.

Hier volgt een voor beeld van Azure Maps waarbij de taal is ingesteld op "FR" en de gebruikers regio ingesteld op "fr-FR".

<center>

![Azure Maps lokalisatie](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>De kaart weergave instellen

Dynamische kaarten in Azure en Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties. Hiertoe roept u de juiste functies aan in Java script. In de voor beelden ziet u hoe u de kaart kunt weer geven satelliet lucht afbeelding, de kaart centreren over een locatie en het zoom niveau wijzigt in 15 in Google Maps. De volgende locatie coördinaten worden gebruikt: lengte graad:-111,0225 en Latitude: 35,0272.

> [!NOTE]
> Google Maps maakt gebruik van tegels van 256 pixels in dimensies terwijl Azure Maps een grotere 512-pixels-tegel gebruikt. Dit beperkt het aantal netwerk aanvragen dat nodig is voor Azure Maps om hetzelfde kaart gebied als Google Maps te laden. Als gevolg van de manier waarop tegel piramides werken in kaart besturings elementen, de grotere tegels in Azure Maps betekent dat u hetzelfde zicht bare gebied als een kaart in Google Maps wilt gebruiken, moet u het zoom niveau dat in Google Maps wordt gebruikt, aftrekken bij het gebruik van Azure Maps.

**Voor: Google Maps**

Het kaart besturings element Google Maps kan via een programma worden verplaatst met behulp van de `setOptions` methode. Met deze methode kunt u het middel punt van de kaart en een zoom niveau opgeven.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps set-weer gave](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Na: Azure Maps**

In Azure Maps kan de kaart positie programmatisch worden gewijzigd met behulp van de methode `setCamera` van de kaart en de kaart stijl kan worden gewijzigd met de methode `setStyle`. De coördinaten in Azure Maps hebben de indeling lengte graad, breedte graad en de waarde voor het zoom niveau wordt afgetrokken door één.

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

weer gave ![Azure Maps instellen](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Aanvullende bronnen:**

- [Een kaart stijl kiezen](choose-map-style.md)
- [Ondersteunde kaart stijlen](supported-map-styles.md)

### <a name="adding-a-marker"></a>Een markering toevoegen

In Azure Maps zijn er meerdere manieren waarop punt gegevens op de kaart kunnen worden weer gegeven.

- **HTML-markeringen** : Hiermee worden punten weer gegeven met traditionele DOM-elementen. HTML-markeringen ondersteunen slepen.
- **Symbol-laag** – geeft punten weer met een pictogram en/of tekst in de WebGL-context.
- **Bubble Layer** : geeft punten weer als cirkels op de kaart. De stralen van de cirkels kunnen worden geschaald op basis van de eigenschappen in de gegevens.

Zowel symbool-als bellen lagen worden weer gegeven in de WebGL-context. Beide lagen kunnen grote sets van punten op de kaart weer geven. Voor deze lagen moeten gegevens worden opgeslagen in een gegevens bron. Gegevens bronnen en rendering lagen moeten worden toegevoegd aan de kaart nadat de `ready` gebeurtenis is geactiveerd. HTML-markeringen worden op de pagina weer gegeven als DOM-elementen en gebruiken geen gegevens bron. Hoe meer DOM-elementen een pagina heeft, hoe langzamer de pagina wordt. Als er meer dan honderd punten op een kaart worden weer gegeven, is het raadzaam om een van de rendering-lagen te gebruiken.

In de volgende voor beelden wordt een markering toegevoegd aan de kaart op (lengte graad:-0,2, Latitude: 51,5) met nummer 10 als label.

**Voor: Google Maps**

Met Google Maps worden markeringen aan de kaart toegevoegd met behulp van de klasse `google.maps.Marker` en door de kaart als een van de opties op te geven.

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

**Na: Azure Maps HTML-markeringen gebruiken**

In Azure Maps kunnen HTML-markeringen worden gebruikt om een punt op de kaart weer te geven. HTML-markeringen worden aanbevolen voor eenvoudigweg apps die slechts een klein aantal punten op de kaart hoeven weer te geven. Als u een HTML-markering wilt gebruiken, maakt u een instantie van de klasse `atlas.HtmlMarker`, stelt u de opties tekst en positie in en voegt u de markering toe aan de kaart met behulp van de methode `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML-markering](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Na: Azure Maps met behulp van een symbool-laag**

Wanneer u een symbool-laag gebruikt, moeten de gegevens worden toegevoegd aan een gegevens bron en de gegevens bron die aan de laag is gekoppeld. Daarnaast moeten de gegevens bron en laag worden toegevoegd aan de kaart nadat de `ready` gebeurtenis is geactiveerd. Als u een unieke tekst waarde wilt weer geven boven een symbool, moeten de tekst gegevens worden opgeslagen als een eigenschap van het gegevens punt en moet er naar deze eigenschap worden verwezen in de `textField` optie van de laag. Deze aanpak is iets meer werk dan het gebruik van HTML-markeringen, maar het biedt prestatie voordelen.

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

![Azure Maps Symbol-laag](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Aanvullende bronnen:**

- [Een gegevens bron maken](create-data-source-web-sdk.md)
- [Een symbool laag toevoegen](map-add-pin.md)
- [Een Bubble laag toevoegen](map-add-bubble-layer.md)
- [Gegevens van cluster punt](clustering-point-data-web-sdk.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Opties voor symbool laag pictogram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [De tekst optie Symbol Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasse HTML-markering](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opties voor HTML-markering](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

Aangepaste installatie kopieën kunnen worden gebruikt om punten op een kaart weer te geven. De volgende kaart afbeelding gebruikt een aangepaste installatie kopie om een punt op de kaart weer te geven. Het punt wordt weer gegeven op de breedte graad: 51,5, lengte graad:-0,2. Het anker verschuift de positie van de markering, zodat het punt van het pictogram punaise wordt uitgelijnd met de juiste positie op de kaart.

<center>

afbeelding van gele punaise ![](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_punaise. png</center>

**Voor: Google Maps**

In Google Maps wordt een aangepaste markering gemaakt door een `Icon`-object op te geven dat de `url` aan de afbeelding bevat, een `anchor` punt om het punt van de punaise afbeelding uit te lijnen met de coördinaat op de kaart. De anker waarde in Google Maps is relatief ten opzichte van de linkerbovenhoek van de afbeelding.

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

aangepaste markering voor ![Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Na: Azure Maps HTML-markeringen gebruiken**

Als u een HTML-markering wilt aanpassen in Azure Maps een HTML-`string` of `HTMLElement` kan worden door gegeven aan de `htmlContent` optie van de markering. In Azure Maps wordt een `anchor` optie gebruikt om de relatieve positie van de markering op te geven ten opzichte van de positie coördinaat met behulp van een van de negen gedefinieerde referentie punten. De gedefinieerde verwijzings punten zijn: ' Center ', ' top ', ' bottom ', ' left ', ' right ', ' top-left ', ' top-right ', ' bottom-left ', ' bottom-right '. De inhoud wordt standaard verankerd aan het midden van de HTML-inhoud. Om het gemakkelijker te maken code te migreren vanuit Google Maps, stelt u de `anchor` in op ' linksboven ' en gebruikt u vervolgens de optie `pixelOffset` met dezelfde offset die wordt gebruikt in Google Maps. De verschuivingen in Azure Maps worden verplaatst in de tegenovergestelde richting van Google Maps, dus Vermenigvuldig ze met min één.

> [!TIP]
> Voeg `pointer-events:none` toe als een stijl in de HTML-inhoud om het standaard gedrag voor slepen in micro soft Edge uit te scha kelen, waardoor een ongewenst pictogram wordt weer gegeven.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps aangepaste HTML-markering](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Na: Azure Maps met behulp van een symbool-laag**

Symbool lagen in Azure Maps ook aangepaste installatie kopieën ondersteunen, maar de installatie kopie moet eerst worden geladen in de kaart bronnen en een unieke ID hebben toegewezen. De Symbol-laag kan vervolgens naar deze ID verwijzen. Het symbool kan worden afgestemd op het juiste punt op de afbeelding met behulp van het pictogram `offset` optie. In Azure Maps wordt een `anchor` optie gebruikt om de relatieve positie van het symbool ten opzichte van de positie coördinaat op te geven met behulp van een van de negen gedefinieerde referentie punten. De gedefinieerde positie coördinaten zijn: ' Center ', ' top ', ' bottom ', ' left ', ' right ', ' top-left ', ' top-right ', ' bottom-left ', ' bottom-right '. De inhoud wordt standaard verankerd aan het midden van de HTML-inhoud. Om het gemakkelijker te maken code te migreren vanuit Google Maps, stelt u de `anchor` in op ' linksboven ' en gebruikt u vervolgens de optie `offset` met dezelfde offset die wordt gebruikt in Google Maps. De verschuivingen in Azure Maps worden verplaatst in de tegenovergestelde richting van Google Maps, dus Vermenigvuldig ze met min één.

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

![Azure Maps aangepaste pictogram laag](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Als u een geavanceerde aangepaste rendering van punten wilt maken, gebruikt u meerdere rendering-lagen tegelijk. Stel bijvoorbeeld dat u meerdere markerings punten wilt hebben die hetzelfde pictogram hebben op verschillende gekleurde cirkels. In plaats van een aantal installatie kopieën voor elke kleur-overlay te maken, voegt u een symbool laag toe boven op een tekenlaag en laat u de markerings punten verwijzen naar dezelfde gegevens bron. Deze benadering is efficiënter dan het maken en onderhouden van een aantal verschillende installatie kopieën.

**Aanvullende bronnen:**

- [Een gegevens bron maken](create-data-source-web-sdk.md)
- [Een symbool laag toevoegen](map-add-pin.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Opties voor symbool laag pictogram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [De tekst optie Symbol Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasse HTML-markering](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opties voor HTML-markering](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Een poly lijn toevoegen

Polylines worden gebruikt om een lijn of pad op de kaart weer te geven. In de volgende voor beelden ziet u hoe u een stippel lijn op de kaart maakt.

**Voor: Google Maps**

In Google Maps gebruikt de klasse poly lijn een set opties. Er wordt een matrix van coördinaten door gegeven in de `path` optie van de poly lijn.

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

![Google Maps-poly lijn](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Na: Azure Maps**

In Azure Maps worden polylinen `LineString` of `MultiLineString` objecten genoemd. Deze objecten kunnen worden toegevoegd aan een gegevens bron en worden weer gegeven met behulp van een laag.

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

![Azure Maps poly lijn](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Aanvullende bronnen:**

- [Regels toevoegen aan de kaart](map-add-line-layer.md)
- [Opties voor lijn lagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Veelhoeken worden gebruikt om een gebied op de kaart weer te geven. Azure Maps-en Google-kaarten bieden vergelijk bare ondersteuning voor veelhoeken. In de volgende voor beelden ziet u hoe u een veelhoek maakt die een drie hoek vormt op basis van de middelste coördinaat van de kaart.

**Voor: Google Maps**

In Google Maps neemt de klasse veelhoek in een reeks opties. Er wordt een matrix van coördinaten door gegeven in de `paths` optie van de polygoon.

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

![Google Maps-polygoon](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Na: Azure Maps**

In Azure Maps kunnen `Polygon` en `MultiPolygon` objecten worden toegevoegd aan een gegevens bron en met behulp van lagen worden weer gegeven op de kaart. Het gebied van een veelhoek kan worden weer gegeven in een polygoon laag. Het overzicht van een veelhoek kan worden gerenderd met behulp van een line-laag.

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

![Azure Maps veelhoek](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Aanvullende bronnen:**

- [Een veelhoek toevoegen aan de kaart](map-add-shape.md)
- [Een cirkel aan de kaart toevoegen](map-add-shape.md#add-a-circle-to-the-map)
- [Opties voor polygoon lagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opties voor lijn lagen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Een informatie venster weer geven

Aanvullende informatie voor een entiteit kan worden weer gegeven op de kaart als een `google.maps.InfoWindow` klasse in Google Maps. In Azure Maps kan deze functionaliteit worden bereikt met behulp van de `atlas.Popup`-klasse. In de volgende voor beelden wordt een markering aan de kaart toegevoegd en wanneer erop wordt geklikt, wordt er een info venster/pop-up weer gegeven.

**Voor: Google Maps**

Met Google Maps wordt een info venster gemaakt met behulp van de `google.maps.InfoWindow`-constructor.

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

![Google Maps-pop-up](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Na: Azure Maps**

In Azure Maps kunt u een pop-upvenster gebruiken om aanvullende informatie weer te geven voor een locatie. Een HTML-`string` of `HTMLElement`-object kan worden door gegeven aan de `content` optie van de pop-up. Pop-ups kunnen desgewenst onafhankelijk van een wille keurige vorm worden weer gegeven. Pop-ups vereisen daarom een `position` waarde die moet worden opgegeven. Als u een pop-upvenster wilt weer geven, roept u de `open`-methode aan en geeft u de `map` op waarin de pop-up wordt weer gegeven.

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

![Azure Maps popup](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Als u hetzelfde wilt doen met een symbool-, bellen-, lijn-of veelhoek-laag, geeft u de laag eenvoudigweg door aan de gebeurtenis code Maps in plaats van een markering.

**Aanvullende bronnen:**

- [Een pop-upvenster toevoegen](map-add-popup.md)
- [Pop-up met media-inhoud](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Popups op vormen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Opnieuw gebruiken van pop-up met meerdere pincodes](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Pop-upklasse](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Pop-upopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Een geojson-bestand importeren

Google Maps ondersteunt het laden en dynamisch koppelen van geojson-gegevens via de `google.maps.Data` klasse. De functionaliteit van deze klasse lijnt veel meer uit met de gegevensgestuurde stijl van Azure Maps. Een belang rijk verschil is dat u met Google Maps een call back-functie opgeeft. De bedrijfs logica voor het afhandelen van elke functie die wordt verwerkt afzonderlijk in de UI-thread. In Azure Maps lagen ondersteunen het opgeven van gegevensgestuurde expressies als opmaak opties. Deze expressies worden verwerkt op het moment dat er een afzonderlijke thread wordt gegenereerd. Deze aanpak verhoogt de weergave prestaties. Dit voor deel is opgemerkt wanneer grotere gegevens sets snel worden gerenderd.

In de volgende voor beelden wordt een geojson-feed van alle aard bevingen in de afgelopen zeven dagen uit de USGS geladen. Ze worden weer gegeven als geschaalde cirkels op de kaart. De kleur en schaal van elke cirkel zijn gebaseerd op de grootte van elke aard beving, die wordt opgeslagen in de eigenschap `"mag"` van elke functie in de gegevensset. Als de grootte groter is dan of gelijk is aan vijf, wordt de cirkel rood. Als het groter is dan of gelijk is aan drie, maar minder dan vijf, wordt de cirkel oranje. Als het minder is dan drie, wordt de cirkel groen. De straal van elke cirkel is de exponentiële waarde van de grootte vermenigvuldigd met 0,1.

**Voor: Google Maps**

In Google Maps kan één call back-functie worden opgegeven in de `map.data.setStyle` methode. Deze methode wordt gebruikt om bedrijfs logica toe te passen op elke functie die is geladen vanuit de geojson-feed via de `map.data.loadGeoJson` methode.

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

geojson-](media/migrate-google-maps-web-app/google-maps-geojson.png)</center> van Google Maps ![

**Na: Azure Maps**

Geojson is het basis gegevens type in Azure Maps en kan eenvoudig worden geïmporteerd in een gegevens bron met behulp van de methode `datasource.importFromUrl`. Een Bubble laag biedt functionaliteit voor het renderen van geschaalde cirkels op basis van de eigenschappen van de functies in een gegevens bron. In plaats van een call back-functie wordt de bedrijfs logica geconverteerd naar een expressie en door gegeven aan de stijl opties. Expressies bepalen hoe de bedrijfs logica werkt. Expressies kunnen worden door gegeven aan een andere thread en worden geëvalueerd op basis van de functie gegevens. Er kunnen meerdere gegevens bronnen en lagen worden toegevoegd aan Azure Maps, elk met verschillende bedrijfs logica. Met deze functie kunnen meerdere gegevens sets op verschillende manieren worden weer gegeven op de kaart.

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

![Azure Maps geojson](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Aanvullende bronnen:**

- [Een symbool laag toevoegen](map-add-pin.md)
- [Een Bubble laag toevoegen](map-add-bubble-layer.md)
- [Gegevens van cluster punt](clustering-point-data-web-sdk.md)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Markerings clusters

Wanneer een groot aantal gegevens punten op de kaart wordt gevisualiseerd, overlappen punten elkaar, de kaart lijkt overzichtelijker en wordt deze moeilijk te zien en te gebruiken. Het clusteren van punt gegevens kan worden gebruikt om deze gebruikers ervaring te verbeteren en ook de prestaties te verbeteren. Clustering Point-gegevens zijn het proces van het samen voegen van punt gegevens die zich in de buurt van elkaar bevinden en als één geclusterd gegevens punt vertegenwoordigen. Wanneer de gebruiker inzoomt op de kaart, worden de clusters in hun afzonderlijke gegevens punten gesplitst.

In de volgende voor beelden laadt code een geojson-feed van de aardte gegevens van de afgelopen week en voegt deze toe aan de kaart. Clusters worden weer gegeven als geschaalde en gekleurde cirkels, afhankelijk van het aantal punten dat ze bevatten.

> [!NOTE]
> Er worden verschillende algoritmen gebruikt voor het markeren van het cluster. Google en Azure Maps gebruiken enigszins verschillende algoritmen. Als zodanig kunnen de punten distributie in de clusters variëren.

**Voor: Google Maps**

In Google Maps-markeringen kan worden geclusterd door te laden in de MarkerClusterer-bibliotheek. Cluster pictogrammen zijn beperkt tot installatie kopieën, die de getallen één tot en met vijf als naam hebben en ze worden gehost in dezelfde map.

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

![Google Maps clustering](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Na: Azure Maps**

In Azure Maps worden gegevens toegevoegd en beheerd door een gegevens bron. Lagen maken verbinding met gegevens bronnen en genereren de gegevens hierin. De klasse `DataSource` in Azure Maps biedt verschillende opties voor clustering.

- `cluster`: geeft de gegevens bron aan cluster Point-gegevens.
- `clusterRadius`: de RADIUS in pixels tot cluster punten samen.
- `clusterMaxZoom`-het maximale zoom niveau waarin clusteren worden uitgevoerd. Als u inzoomt op meer dan dit niveau, worden alle punten weer gegeven als symbolen.
- `clusterProperties`-Hiermee worden aangepaste eigenschappen gedefinieerd die worden berekend met behulp van expressies voor alle punten in elk cluster en worden toegevoegd aan de eigenschappen van elk cluster punt.

Als Clustering is ingeschakeld, worden geclusterde en niet-geclusterde gegevens punten door de gegevens bron naar lagen verzonden voor rendering. Met de gegevens bron kunnen honderd duizenden gegevens punten worden geclusterd. Een geclusterd gegevens punt heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Hiermee wordt aangegeven of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | string  | Een unieke ID voor het cluster die kan worden gebruikt met de methoden data source `getClusterExpansionZoom`, `getClusterChildren`en `getClusterLeaves`. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | string  | Een teken reeks die de `point_count` waarde verkort als deze lang is. (4.000 wordt bijvoorbeeld 4.000)  |

De klasse `DataSource` heeft de volgende Help-functie voor het verkrijgen van toegang tot aanvullende informatie over een cluster met behulp van de `cluster_id`.

| Methode | Retour type | Beschrijving |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Hiermee worden de onderliggende items van het opgegeven cluster op het volgende zoom niveau opgehaald. Deze onderliggende elementen kunnen bestaan uit een combi natie van vormen en subclusteren. De subclusteren zijn functies met eigenschappen die overeenkomen met ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | &lt;nummer van Promise&gt; | Hiermee wordt een zoom niveau berekend waarmee het cluster wordt uitgebreid of gesplitst. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;matrix&lt;functie&lt;geometrie, een&gt; \| vorm&gt;&gt; | Haalt alle punten in een cluster op. Stel de `limit` in om een subset van de punten te retour neren en gebruik de `offset` om door de punten te bladeren. |

Bij het renderen van geclusterde gegevens op de kaart is het vaak eenvoudig om twee of meer lagen te gebruiken. In het volgende voor beeld worden drie lagen gebruikt. Een tekenlaag voor teken schalen van gekleurde cirkels op basis van de grootte van de clusters. Een symbool laag om de cluster grootte als tekst weer te geven. En maakt gebruik van een tweede symbool laag voor het weer geven van de niet-geclusterde punten. Er zijn veel andere manieren om geclusterde gegevens weer te geven. Zie de documentatie voor [cluster Point-gegevens](clustering-point-data-web-sdk.md) voor meer informatie.

Geojson-gegevens kunnen rechtstreeks worden geïmporteerd in Azure Maps met behulp van de `importDataFromUrl` functie van de `DataSource`-klasse.

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

![Azure Maps clustering](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Aanvullende bronnen:**

- [Een symbool laag toevoegen](map-add-pin.md)
- [Een Bubble laag toevoegen](map-add-bubble-layer.md)
- [Gegevens van cluster punt](clustering-point-data-web-sdk.md)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Een heatmap toevoegen

Heatmap, ook wel punt-dichtheids kaarten genoemd, is een type gegevens visualisatie. Ze worden gebruikt voor de densiteit van gegevens met behulp van een reeks kleuren. Ze worden vaak gebruikt om de gegevens ' HOTS Pots ' op een kaart weer te geven. Hitte kaarten zijn een uitstekende manier om grote punt gegevens sets weer te geven.

In de volgende voor beelden wordt een geojson-feed van alle aard bevingen in de afgelopen maand van de USGS geladen en weer gegeven als een gewogen heatmap waarbij de eigenschap `"mag"` als gewicht wordt gebruikt.

**Voor: Google Maps**

Als u in Google Maps een heatmap wilt maken, moet de visualisatie bibliotheek worden geladen door `&libraries=visualization` toe te voegen aan de URL van het API-script. De laag van de heatmap in Google Maps ondersteunt geen geojsongegevens rechtstreeks. De gegevens moeten eerst worden gedownload en geconverteerd naar een matrix van gewogen gegevens punten.

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

![Google Maps-heatmap](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Na: Azure Maps**

In Azure Maps laadt u de geojson-gegevens in een gegevens bron en verbindt u de gegevens bron met een heatmap. De eigenschap die wordt gebruikt voor het gewicht kan worden door gegeven aan de `weight` optie met behulp van een expressie. Geojson-gegevens kunnen rechtstreeks worden geïmporteerd in Azure Maps met behulp van de `importDataFromUrl` functie van de `DataSource`-klasse.

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

![Azure Maps heatmap](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Aanvullende bronnen:**

- [Een hitte kaart laag toevoegen](map-add-heat-map-layer.md)
- [Klasse van laag voor heatmap](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opties voor de laag van de heatmap](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Een tegel laag bedekken

Tegel lagen worden ook wel afbeeldings-overlays genoemd in Google Maps. Met de tegel lagen kunt u grote afbeeldingen bedekken die zijn opgesplitst in kleinere naast elkaar liggende afbeeldingen die zijn afgestemd op het kaarten tegel systeem. Dit wordt vaak gebruikt om grote afbeeldingen of grote gegevens sets te bedekken.

De volgende voor beelden bedekken een weer gave laag van Iowa Environment Mesonet van Iowa State University.

**Voor: Google Maps**

In Google Maps kunnen tegel lagen worden gemaakt met behulp van de klasse `google.maps.ImageMapType`.

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

![Google Maps-tegel laag](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Na: Azure Maps**

In Azure Maps kan een tegel laag net als elke andere laag worden toegevoegd aan de kaart. Een opgemaakt URL met een x-, y-, zoom-tijdelijke aanduiding; `{x}`, `{y}``{z}` respectievelijk wordt gebruikt om de laag voor toegang tot de tegels te geven. Azure Maps-tegel lagen ondersteunen ook `{quadkey}`, `{bbox-epsg-3857}`en `{subdomain}` tijdelijke aanduidingen.

> [!TIP]
> In Azure Maps lagen kunnen eenvoudig worden weer gegeven onder andere lagen, waaronder basis kaart lagen. Vaak is het wenselijk om tegel lagen onder de kaart labels weer te geven, zodat ze gemakkelijk te lezen zijn. De `map.layers.add`-methode heeft een tweede para meter die de id is van de laag waarin de nieuwe laag moet worden ingevoegd. Als u een tegel laag wilt invoegen onder de kaart labels, kunt u de volgende code gebruiken: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps-laag van de tegel](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Tegel aanvragen kunnen worden vastgelegd met de optie `transformRequest` van de kaart. Op deze manier kunt u indien gewenst kopteksten aan de aanvraag aanpassen of toevoegen.

**Aanvullende bronnen:**

- [Tegel lagen toevoegen](map-add-tile-layer.md)
- [Klasse van laag van Tegel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opties voor de laag van de tegel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Verkeer weergeven

Verkeers gegevens kunnen zowel Azure-als Google-kaarten overlappen.

**Voor: Google Maps**

In Google Maps kunnen verkeers gegevens op de kaart worden overlay met behulp van de laag van het verkeer.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps-verkeer](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Na: Azure Maps**

Azure Maps biedt verschillende opties voor het weer geven van verkeer. Verkeers incidenten, zoals het sluiten van wegsluitingen en ongel ukken, kunnen als pictogrammen op de kaart worden weer gegeven. Verkeers stromen, wegen met kleur code kunnen op de kaart worden geplaatst en de kleuren kunnen worden aangepast aan de grenzen van de geposte snelheid, ten opzichte van de normale verwachte vertraging of absolute vertraging. Incidenteer gegevens in Azure Maps elke minuut en stroomt gegevens elke twee minuten.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps verkeer](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Als u op een van de verkeers pictogrammen in Azure Maps klikt, wordt extra informatie weer gegeven in een pop-upvenster.

<center>

![Azure Maps verkeers incident](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Aanvullende bronnen:**

- [Verkeer op de kaart weer geven](map-show-traffic.md)
- [Opties voor Traffic overlay](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Een wegdek bedekking toevoegen

Zowel Azure-als Google-kaarten bieden ondersteuning voor het bedekken van geoverwijzings afbeeldingen op de kaart, zodat ze kunnen worden verplaatst en geschaald wanneer u de kaart wilt pannen en zoomen. In Google Maps worden deze bedekkingen aangeduid als ' laag overlays ' in Azure Maps ze worden aangeduid als afbeeldings lagen. Deze zijn ideaal voor het bouwen van vloer plannen, het bedekken van oude kaarten of installatie kopieën van een drone.

**Voor: Google Maps**

Wanneer u een basis bedekking in Google Maps maakt, moet u de URL naar de afbeelding die u wilt bedekken, opgeven en een selectie kader om de afbeelding aan de kaart te koppelen. In dit voor beeld wordt een kaart afbeelding van [Newark New Jersey van 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) op de kaart bedekt.

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

Als deze code wordt uitgevoerd in een browser, wordt een kaart weer gegeven met de volgende afbeelding:

<center>

overlay voor afbeelding van Google Maps ![](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Na: Azure Maps**

In Azure Maps kunnen installatie kopieën met geoverwijzingen worden overlapt met behulp van de `atlas.layer.ImageLayer`-klasse. Deze klasse vereist een URL naar een afbeelding en een set coördinaten voor de vier hoeken van de afbeelding. De installatie kopie moet worden gehost op hetzelfde domein of als CORs is ingeschakeld.

> [!TIP]
> Als u alleen North-, Zuid-, Oost-, West-en rotatie gegevens hebt en geen coördinaten voor elke hoek van de afbeelding, kunt u de statische [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) methode gebruiken.

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

![Azure Maps installatie kopie-overlay](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Aanvullende bronnen:**

- [Een afbeelding bedekken](map-add-image-layer.md)
- [Klasse van afbeeldings laag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Aanvullende code voorbeelden

Hier volgen enkele aanvullende code voorbeelden met betrekking tot de migratie van Google Maps:

- [Teken hulpprogramma's](map-add-drawing-toolbar.md)
- [Toewijzing beperken tot twee vinger inpannen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Zoom niveau van schuif wiel beperken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Een besturings element voor volledig opnieuw instellen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Onderzoeksservices**

- [De Azure Maps Services-module gebruiken](how-to-use-services-module.md)
- [Zoeken naar bezienswaardigheden](map-search-location.md)
- [Informatie ophalen uit een coördinaat (omgekeerde Geocode)](map-get-information-from-coordinate.md)
- [Route beschrijving van A naar B weer geven](map-route.md)
- [Automatische suggesties zoeken met de JQuery-gebruikers interface](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps v3 to Azure Maps Web SDK-klassen toewijzing

De volgende bijlage bevat een kruis verwijzing van de meestgebruikte klassen in Google Maps v3 en de Azure Maps Web SDK-equivalentie.

### <a name="core-classes"></a>Kern klassen

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Diagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Achtergrond](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. data. position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Kanaal](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Overlay-klassen

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. data. veelhoek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. data. Lines Tring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Zie [een cirkel toevoegen aan de kaart](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Service klassen

De Azure Maps Web-SDK bevat een module van [Services, die afzonderlijk kan worden geladen. Deze module verpakt de Azure Maps REST-services met een web-API en kan worden gebruikt in Java script-, type script-en node. js-toepassingen.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotheken

Bibliotheken voegen extra functionaliteit toe aan de kaart. Veel van deze bevinden zich in de core-SDK van Azure Maps. Hier volgen enkele equivalente klassen die u kunt gebruiken in plaats van deze Google Maps-bibliotheken

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Teken bibliotheek       | [Module voor tekenprogramma's](set-drawing-options.md) |
| Geometrie bibliotheek      | [Atlas. math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Visualisatie bibliotheek | [Hitte kaart laag](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Web-SDK.

> [!div class="nextstepaction"]
> [Map Control gebruiken](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [De Services-module gebruiken](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [De module teken hulpprogramma's gebruiken](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)

