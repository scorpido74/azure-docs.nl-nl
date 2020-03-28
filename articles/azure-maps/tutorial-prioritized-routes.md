---
title: 'Zelfstudie: Meerdere routes zoeken op reiswijze | Microsoft Azure Maps'
description: In deze zelfstudie leert u hoe u routes voor verschillende manieren van reizen vinden met Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333794"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Zelfstudie: Routes zoeken voor verschillende vervoerswijzen met Azure Maps

In deze zelfstudie ziet u hoe u uw Azure Maps-account en de routeservice gebruiken. De routeservice kan de route naar uw interessepunt vinden, geprioriteerd door uw manier van reizen. U twee verschillende routes op uw kaart weergeven, één voor auto's en één voor vrachtwagens. De routeringsdienst houdt rekening met beperkingen vanwege de hoogte en het gewicht van het voertuig of als het voertuig gevaarlijke lading vervoert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Verkeerstromen op de kaart visualiseren
> * Routequery's maken die de manier van reizen declareren
> * Meerdere routes op de kaart weergeven

## <a name="prerequisites"></a>Vereisten
Volg de instructies in Een account maken voordat u verdergaat met [het maken van een account](quick-demo-map-app.md#create-an-account-with-azure-maps) en selecteer de prijslaag S1. Volg de stappen in [de primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te krijgen. Zie Verificatie beheren in Azure [Maps voor](how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

## <a name="create-a-new-map"></a>Een nieuwe kaart maken

Gebruik de volgende stappen voor het maken van een statische HTML-pagina, ingesloten met de Map Control-API.

1. Maak een nieuw bestand op uw lokale computer en noem dit **MapTruckRoute.html**.
2. Voeg de volgende HTML-onderdelen toe aan het bestand:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    U ziet dat de HTML-header de CSS- en JavaScript-bronbestanden bevat, gehost door de Azure Map Control-bibliotheek. Let op de gebeurtenis `onload` in het hoofdtekstgedeelte van de pagina. Deze zorgt ervoor dat de functie `GetMap` wordt aangeroepen nadat het hoofdtekstgedeelte van de pagina is geladen. Deze functie bevat de inline JavaScript-code voor toegang tot de API's van Azure Maps.

3. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Vervang de `<Your Azure Maps Key>` tekenreeks door de primaire sleutel die u hebt gekopieerd uit uw Maps-account.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    De `atlas.Map` klasse biedt de controle voor een visuele en interactieve webkaart en is een onderdeel van de Azure Map Control API.

4. Sla het bestand op en open het in uw browser. U hebt nu een basiskaart die u verder kunt ontwikkelen.

   ![Basiskaart weergeven](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Verkeerstromen visualiseren

1. Voeg de weergave van de verkeersstroom toe aan de kaart. De `ready` kaarten gebeurtenis wacht tot de kaarten bronnen zijn geladen en klaar om veilig te communiceren met het.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    In de `ready` map event handler, de verkeersstroom `relative`instelling op de kaart is ingesteld op , dat is de snelheid van de weg ten opzichte van de vrije stroom. U kunt deze ook instellen op `absolute` snelheid van de weg of `relative-delay` de relatieve snelheid weergeven, waar deze verschilt van een vrije stroom.

2. Sla het bestand **MapTruckRoute.html** op en vernieuw de pagina in uw browser. Als u interactie hebt met de kaart en inzoomt op Los Angeles, moet u de straten bekijken met de huidige verkeersgegevens.

   ![Verkeer op een kaart weergeven](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiëren hoe de route wordt weergegeven

In deze zelfstudie worden twee routes berekend en weergegeven op de kaart. Eén route die toegankelijk is voor auto's en de andere die toegankelijk is voor vrachtwagens. Wanneer gerenderd tonen we een symboolpictogram voor het begin en einde van de route en verschillende gekleurde lijnen voor elk routepad.

1. Nadat u de kaart hebt geïnitialiseren, voegt u de volgende JavaScript-code toe in de gebeurtenishandler kaarten. `ready`

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    In de `ready` gebeurtenishandler kaarten wordt een gegevensbron gemaakt om de routelijnen en de begin- en eindpunten op te slaan. Er wordt een lijnlaag gemaakt en aan de gegevensbron gekoppeld waarmee wordt gedefinieerd hoe de routelijn wordt weergegeven. Er wordt gebruikgemaakt van expressies om de lijndikte en kleur op te halen uit de eigenschappen van de routelijnfunctie. Als u de laag toevoegt aan de kaart wordt een tweede parameter met de waarde `'labels'` doorgegeven. Hiermee wordt bepaald dat deze laag onder de kaartlabels wordt weergegeven. Dit zorgt ervoor dat de routelijn de weglabels niet dekt. Er wordt een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de begin- en eindpunten worden weergegeven. In dit geval zijn expressies toegevoegd om de informatie van de pictogramafbeelding en het tekstlabel op te halen uit eigenschappen op elk puntobject. 
    
2. Voor deze zelfstudie, moet u een fictief bedrijf in Seattle, genaamd Fabrikam, instellen als beginpunt, en een kantoor van Microsoft als eindpunt (bestemming). Voeg in `ready` de gebeurtenishandler kaarten de volgende code toe.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Deze code maakt twee [GeoJSON-objecten](https://en.wikipedia.org/wiki/GeoJSON) die de begin- en eindpunten van de route vertegenwoordigen. Aan elk punt wordt een eigenschap `title` en `icon` toegevoegd.

3. Voeg nu de volgende JavaScript-code toe om de spelden voor de begin- en eindpunten toe te voegen aan de kaart:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    De begin- en eindpunten worden toegevoegd aan de gegevensbron. Het begrenzingsvak voor de begin- en eindpunten wordt berekend met behulp van de functie `atlas.data.BoundingBox.fromData`. Dit selectiekader wordt gebruikt om de kaartcamera's `map.setCamera` weergave over de hele route met behulp van de functie in te stellen. Er wordt opvulling toegevoegd om de grootte van de pixels in de symboolpictogrammen te compenseren.

4. Sla het bestand op en vernieuw de browser. De spelden worden weergegeven op de kaart. Nu is de kaart gecentreerd over Seattle. U de ronde blauwe pin zien die het beginpunt markeert en de blauwe speld die het eindpunt markeert.

   ![Kaart met begin- en eindpunt weergeven](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Routes weergeven gerangschikt naar de manier van reizen

In deze sectie ziet u hoe u de API voor routeservice Kaarten gebruiken. De route-API wordt gebruikt om meerdere routes te vinden van een bepaald beginpunt naar het eindpunt, op basis van uw vervoerswijze. De routeservice biedt API's om *snelste,* *kortste,* *eco-* of *spannende* routes te plannen. De API's plannen niet alleen routes tussen twee locaties, maar houden ook rekening met de huidige verkeersomstandigheden. 

Met de route-API kunnen gebruikers routes in de toekomst plannen met behulp van de uitgebreide historische verkeersdatabase van Azure. De API kan de routeduur voor een bepaalde dag en tijd voorspellen. Zie [GetRoute Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Routebeschrijvingen ophalen) voor meer informatie. 

Alle volgende codeblokken moeten worden toegevoegd **in de gebeurtenis kaartbelastingListener** om ervoor te zorgen dat ze worden geladen nadat de kaart volledig is geladen.

1. Voeg in de functie GetMap het volgende toe aan Javascript-code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Hiermee `SubscriptionKeyCredential` wordt `SubscriptionKeyCredentialPolicy` een gemaakt om HTTP-aanvragen voor Azure Maps te verifiëren met de abonnementssleutel. De `atlas.service.MapsURL.newPipeline()` takes `SubscriptionKeyCredential` in het beleid en maakt een [pipeline-instantie.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Het `routeURL` vertegenwoordigt een URL naar Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) bewerkingen.

2. Nadat u referenties en de URL hebt ingesteld, voegt u de volgende JavaScript-code toe om een route van begin tot eindpunt te maken voor een vrachtwagen die door USHazmatClass2 geklasseerde lading vervoert en de resultaten weerte geven.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    In dit codefragment hierboven wordt de routeringsservice van Azure Maps opgevraagd via de methode [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) De routeregel wordt vervolgens geëxtraheerd uit de GeoJSON-functieverzameling uit de respons die met behulp van de `geojson.getFeatures()` methode wordt geëxtraheerd. De routeregel wordt vervolgens toegevoegd aan de gegevensbron. Een index van 0 zorgt ervoor dat deze wordt weergegeven vóór andere regels in de gegevensbron. Dit wordt gedaan omdat de berekening van de vrachtwagenroute vaak langzamer is dan een routeberekening van een auto. Als de routelijn van de truck na de route van de auto aan de gegevensbron wordt toegevoegd, wordt deze erboven weergegeven. Twee eigenschappen worden toegevoegd aan de routelijn van de truck, een lijnkleur die een mooie tint blauw is en een lijnbreedte van negen pixels.

3. Voeg de volgende JavaScript-code toe om een route voor een auto te maken en de resultaten weer te geven.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    In dit codefragment hierboven wordt de routeringsservice van Azure Maps opgevraagd via de methode [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) De routeregel wordt vervolgens geëxtraheerd uit de GeoJSON-functieverzameling uit de respons die met behulp van de `geojson.getFeatures()` methode wordt geëxtraheerd. De routeregel wordt vervolgens toegevoegd aan de gegevensbron. Aan de routelijn van de auto worden twee eigenschappen toegevoegd, een lijnkleur die een tint paars is en een lijnbreedte van vijf pixels.  

4. Sla het bestand **MapTruckRoute.html** op en vernieuw de browser om het resultaat te bekijken. Bij een succesvolle verbinding met de-API's van Maps ziet de kaart er ongeveer als volgt uit.

    ![Routes rangschikken met Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    De truck route is dik blauw, en de auto route is dun paars. De auto route gaat over Lake Washington via I-90, die gaat door tunnels onder woonwijken. Omdat de tunnels dicht bij woonwijken liggen, is de lading van gevaarlijk afval beperkt. De vrachtwagenroute, die een USHazmatClass2 vrachttype specificeert, wordt geleid om een verschillende weg te gebruiken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Verkeerstromen op de kaart visualiseren
> * Routequery's maken die de manier van reizen declareren
> * Meerdere routes op de kaart weergeven

> [!div class="nextstepaction"]
> [Volledige broncode weergeven](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Bekijk live voorbeeld](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

In de volgende zelfstudie ziet u het proces voor het maken van een eenvoudige winkellocator met behulp van Azure Maps.

> [!div class="nextstepaction"]
> [Een winkellocator maken met behulp van Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)