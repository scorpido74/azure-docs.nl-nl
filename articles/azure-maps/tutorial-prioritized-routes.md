---
title: 'Zelf studie: meerdere routes zoeken op reis wijze | Microsoft Azure kaarten'
description: In deze zelf studie leert u hoe u routes kunt vinden voor verschillende reis methoden met behulp van Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 73cc2ff49653c91d635d52b79a92d1974bfd895b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989651"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Zelf studie: routes voor verschillende manieren van reizen zoeken met behulp van Azure Maps

Deze zelf studie laat zien hoe u uw Azure Maps-account en de route service kunt gebruiken. De route service kan de route naar uw interesse punt vinden, met een prioriteit van uw reis wijze. U kunt twee verschillende routes weer geven op uw kaart, één voor auto's en één voor vracht wagens. De Routing-service houdt rekening met de beperkingen vanwege de hoogte en het gewicht van het Voer tuig, of als het Voer tuig gevaarlijke lading verzorgt. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Verkeerstromen op de kaart visualiseren
> * Routequery's maken die de manier van reizen declareren
> * Meerdere routes op de kaart weergeven

## <a name="prerequisites"></a>Vereisten
Voordat u doorgaat, volgt u de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) en selecteert u de prijs categorie S1. Volg de stappen in de [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account op te halen. Zie [verificatie beheren in azure Maps](how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

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

3. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Vervang de teken reeks `<Your Azure Maps Key>` door de primaire sleutel die u hebt gekopieerd uit uw Maps-account.

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

    De klasse `atlas.Map` biedt het besturings element voor een visuele en interactieve Webkaart en maakt deel uit van de Azure Map Control-API.

4. Sla het bestand op en open het in uw browser. U hebt nu een basiskaart die u verder kunt ontwikkelen.

   ![Basiskaart weergeven](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Verkeerstromen visualiseren

1. Voeg de weergave van de verkeersstroom toe aan de kaart. De Maps `ready` gebeurtenis wacht totdat de resources van de toewijzing zijn geladen en klaar zijn om ermee te communiceren.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    In de gebeurtenis-handler voor de kaart `ready` de verkeers stroom op de kaart is ingesteld op `relative`. Dit is de snelheid van de weg ten opzichte van vrije stroom. U kunt deze ook instellen op `absolute` snelheid van de weg of `relative-delay` de relatieve snelheid weergeven, waar deze verschilt van een vrije stroom.

2. Sla het bestand **MapTruckRoute.html** op en vernieuw de pagina in uw browser. Als u met de kaart communiceert en inzoomt op Los Angeles, ziet u de straten met de huidige verkeers gegevens.

   ![Verkeer op een kaart weer geven](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiëren hoe de route wordt weergegeven

In deze zelfstudie worden twee routes berekend en weergegeven op de kaart. Eén route die toegankelijk is voor auto's en de andere die toegankelijk is voor vrachtwagens. Bij het renderen wordt een symbool pictogram weer gegeven voor het begin en het einde van de route, en verschillende gekleurde lijnen voor elk traject.

1. Nadat u de kaart hebt geïnitialiseerd, voegt u de volgende Java script-code toe in de gebeurtenis-handler Maps `ready`.

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
    
    In de gebeurtenis-handler Maps `ready` wordt een gegevens bron gemaakt voor het opslaan van de route lijnen en de begin-en eind punten. Er wordt een lijnlaag gemaakt en aan de gegevensbron gekoppeld waarmee wordt gedefinieerd hoe de routelijn wordt weergegeven. Er wordt gebruikgemaakt van expressies om de lijndikte en kleur op te halen uit de eigenschappen van de routelijnfunctie. Als u de laag toevoegt aan de kaart wordt een tweede parameter met de waarde `'labels'` doorgegeven. Hiermee wordt bepaald dat deze laag onder de kaartlabels wordt weergegeven. Zo kunt u er zeker van zijn dat de routelijnen de straatlabels niet bedekken. Er wordt een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de begin-en eind punten worden weer gegeven. In dit geval zijn er expressies toegevoegd voor het ophalen van de pictogram afbeelding en tekst label informatie uit eigenschappen van elk object Point. 
    
2. Voor deze zelfstudie, moet u een fictief bedrijf in Seattle, genaamd Fabrikam, instellen als beginpunt, en een kantoor van Microsoft als eindpunt (bestemming). Voeg de volgende code toe in de gebeurtenis-handler Maps `ready`.

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

    De begin- en eindpunten worden toegevoegd aan de gegevensbron. Het begrenzingsvak voor de begin- en eindpunten wordt berekend met behulp van de functie `atlas.data.BoundingBox.fromData`. Dit begrenzingsvak wordt gebruikt om de weer gave van de kaart camera's voor de volledige route in te stellen met behulp van de functie `map.setCamera`. Er wordt opvulling toegevoegd om de grootte van de pixels in de symboolpictogrammen te compenseren.

4. Sla het bestand op en vernieuw de browser. De spelden worden weergegeven op de kaart. De kaart is nu gecentreerd op Seattle. U ziet de ronde blauwe pincode die het begin punt markeert en de blauwe pincode markeert het eind punt.

   ![Kaart met begin- en eindpunt weergeven](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Routes weergeven gerangschikt naar de manier van reizen

In deze sectie wordt beschreven hoe u de Maps route Service-API gebruikt om meerdere routes van een bepaald begin punt naar het eind punt te zoeken op basis van uw transport methode. De routeservice biedt API's om de *snelste*, *kortste*, *zuinigste* of *leukste* route tussen twee locaties te plannen, waarbij rekening wordt gehouden met de huidige verkeersomstandigheden. Ook kunnen gebruikers in de toekomst routes plannen met behulp van de uitgebreide historische verkeersdatabase van Azure en de duur van de route voor elke dag en tijd voorspellen. Zie [GetRoute Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Routebeschrijvingen ophalen) voor meer informatie. Alle volgende codeblokken moeten worden toegevoegd **binnen de map.loadEventListener** om ervoor te zorgen dat de blokken worden geladen nadat de kaart volledig is geladen.

1. Voeg in de functie GetMap het volgende toe aan java script-code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   De `SubscriptionKeyCredential` maakt een `SubscriptionKeyCredentialPolicy` voor het verifiëren van HTTP-aanvragen om te Azure Maps met de abonnements sleutel. De `atlas.service.MapsURL.newPipeline()` neemt in het `SubscriptionKeyCredential` beleid en maakt een [pijplijn](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instantie. De `routeURL` vertegenwoordigt een URL voor het Azure Maps van [route](https://docs.microsoft.com/rest/api/maps/route) bewerkingen.

2. Nadat u referenties en de URL hebt ingesteld, voegt u de volgende Java script-code toe om een route van het begin naar het eind punt te maken voor een truck met USHazmatClass2e lading en worden de resultaten weer gegeven.

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

    Dit code fragment hierboven voert een query uit op de Azure Maps Routing-service via de methode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . De route lijn wordt vervolgens geëxtraheerd uit de verzameling geojson-functies uit het antwoord dat is geëxtraheerd met behulp van de `geojson.getFeatures()` methode. De route lijn wordt vervolgens toegevoegd aan de gegevens bron. Een index van 0 zorgt ervoor dat deze wordt weer gegeven vóór andere regels in de gegevens bron. Dit gebeurt omdat de berekende vrachtwagenroute vaak langzamer uitvalt dan een autoroute, en als de vrachtwagenroute na de autoroute aan de gegevensbron wordt toegevoegd, wordt de vrachtwagenroute bovenop de autoroute weergegeven. Er worden twee eigenschappen toegevoegd aan de route lijn van de vracht wagen, een streek kleur die een fraaie tint van blauw is en een streek breedte van negen pixels.

3. Voeg de volgende Java script-code toe om een route voor een auto te maken en de resultaten weer te geven.

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

    Dit code fragment hierboven voert een query uit op de Azure Maps Routing-service via de methode [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . De route lijn wordt vervolgens geëxtraheerd uit de verzameling geojson-functies uit het antwoord dat is geëxtraheerd met behulp van de `geojson.getFeatures()` methode. De route lijn wordt vervolgens toegevoegd aan de gegevens bron. Er worden twee eigenschappen toegevoegd aan de route lijn van de auto, een streek kleur die een tint van paars is en een streek breedte van vijf pixels.  

4. Sla het bestand **MapTruckRoute.html** op en vernieuw de browser om het resultaat te bekijken. Bij een succesvolle verbinding met de-API's van Maps ziet de kaart er ongeveer als volgt uit.

    ![Routes rangschikken met Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    De route voor vrachtwagens is blauw en dik. De route voor auto's is paars en dun. De route voor auto's gaat over Lake Washington via de I-90 door tunnels onder woongebieden en is verboden voor het vervoer van gevaarlijke afvalstoffen. De route voor vrachtwagens, met een lading met de Amerikaanse gevarenklasse USHazmatClass2, loopt dan ook via een andere snelweg.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Verkeerstromen op de kaart visualiseren
> * Routequery's maken die de manier van reizen declareren
> * Meerdere routes op de kaart weergeven

> [!div class="nextstepaction"]
> [Volledige bron code weer geven](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Live voor beeld weer geven](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

In de volgende zelfstudie ziet u het proces voor het maken van een eenvoudige winkellocator met behulp van Azure Maps.

> [!div class="nextstepaction"]
> [Een winkellocator maken met behulp van Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Op gegevens gebaseerde stijl expressies gebruiken](data-driven-style-expressions-web-sdk.md)