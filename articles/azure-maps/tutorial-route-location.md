---
title: 'Zelfstudie: Route naar een locatie zoeken | Microsoft Azure Maps'
description: In deze zelfstudie ziet u hoe u route naar een locatie (interessantpunt) op een kaart weergeven met behulp van Microsoft Azure Maps Routing Service.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333762"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Zelfstudie: Routeren naar een interessant punt met Azure Maps

Deze zelfstudie laat zien hoe u uw Azure Maps-account en de Route Service SDK gebruikt om de route naar uw nuttige plaats te vinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Adrescoördinaten instellen
> * Query uitvoeren op Route Service voor route naar nuttige plaats

## <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, volgt u instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps), hebt u een abonnement met S1-prijscategorie nodig. Volg de stappen in [de primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te krijgen. Zie Verificatie beheren in Azure [Maps voor](how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Een nieuwe kaart maken

Gebruik de volgende stappen voor het maken van een statische HTML-pagina, ingesloten met de Map Control-API.

1. Maak een nieuw bestand op uw lokale computer en noem dit **MapRoute.html**.
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

    De `atlas.Map` biedt het besturingselement voor een visuele en interactieve webkaart en is een onderdeel van de Azure Map Control API.

4. Sla het bestand op en open het in uw browser. U hebt nu een basiskaart die u verder kunt ontwikkelen.

   ![Basiskaart weergeven](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definiëren hoe de route wordt weergegeven

In deze zelfstudie wordt een eenvoudige route weergegeven met behulp van een symboolpictogram voor het begin en het einde van de route, en een lijn voor het routepad.

1. Nadat u de kaart hebt geïnitialiseren, voegt u de volgende JavaScript-code toe.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    In de `ready` gebeurtenishandler kaarten wordt een gegevensbron gemaakt om de routelijn en de begin- en eindpunten op te slaan. Er wordt een lijnlaag gemaakt en aan de gegevensbron gekoppeld waarmee wordt gedefinieerd hoe de routelijn wordt weergegeven. De route lijn zal worden weergegeven als een mooie schaduw van blauw. Het heeft een breedte van vijf pixels, afgeronde lijn joins, en caps. Als u de laag toevoegt aan de kaart wordt een tweede parameter met de waarde `'labels'` doorgegeven. Hiermee wordt bepaald dat deze laag onder de kaartlabels wordt weergegeven. Zo kunt u er zeker van zijn dat de routelijnen de straatlabels niet bedekken. Er wordt een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de begin- en eindpunten worden weergegeven. In dit geval zijn expressies toegevoegd om de informatie van de pictogramafbeelding en het tekstlabel op te halen uit eigenschappen op elk puntobject. 
    
2. Voor deze zelfstudie stelt u Microsoft in als beginpunt en een benzinestation in Seattle als eindpunt. Voeg in `ready` de gebeurtenishandler kaarten de volgende code toe.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Met deze code worden twee [GeoJSON-puntobjecten](https://en.wikipedia.org/wiki/GeoJSON) gemaakt om de begin- en eindpunten van de route weer te geven en worden de punten aan de gegevensbron toegevoegd. Aan elk punt wordt een eigenschap `title` en `icon` toegevoegd. Het laatste blok stelt de cameraweergave in met behulp van de breedte- en lengtegraad van de begin- en eindpunten, met behulp van de eigenschap [SetCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

3. Sla het bestand **MapRoute.html** op en vernieuw de browser. Nu is de kaart gecentreerd over Seattle, en je zien dat de blauwe pin het beginpunt markeert en de ronde blauwe pin die het eindpunt markeert.

   ![Routes begin- en eindpunt op de kaart weergeven](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Routebeschrijving ophalen

In deze sectie ziet u hoe u de API voor routeservice-API van Azure Maps gebruiken. De API voor routeservice vindt de route van een bepaald beginpunt naar een eindpunt. Binnen deze service zijn er API's om *snelste,* *kortste,* *eco-* of *spannende* routes tussen twee locaties te plannen. Met deze service kunnen gebruikers in de toekomst ook routes plannen met behulp van de uitgebreide historische verkeersdatabase van Azure. Gebruikers kunnen de voorspelling van de routeduur op elke gekozen dag en tijd zien. Zie [Routebeschrijvingen ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) voor meer informatie. Alle volgende functionaliteiten moeten worden toegevoegd **in de map ready eventListener** om ervoor te zorgen dat ze worden geladen nadat de kaartbronnen klaar zijn om te worden geopend.

1. Voeg in de functie GetMap het volgende toe aan de JavaScript-code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Hiermee `SubscriptionKeyCredential` wordt `SubscriptionKeyCredentialPolicy` een gemaakt om HTTP-aanvragen voor Azure Maps te verifiëren met de abonnementssleutel. De `atlas.service.MapsURL.newPipeline()` takes `SubscriptionKeyCredential` in het beleid en maakt een [pipeline-instantie.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Het `routeURL` vertegenwoordigt een URL naar Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) bewerkingen.

2. Nadat u referenties en de URL hebt ingesteld, voegt u de volgende JavaScript-code toe om de route van begin tot eind te construeren. De `routeURL` aanvragen van de routeservice Azure Maps om routebeschrijvingen te berekenen. Een GeoJSON-functieverzameling uit het antwoord `geojson.getFeatures()` wordt vervolgens geëxtraheerd met behulp van de methode en toegevoegd aan de gegevensbron.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Sla het bestand **MapRoute.html** op en vernieuw de webbrowser. Bij een succesvolle verbinding met de API's van Maps ziet de kaart er ongeveer als volgt uit.

    ![Azure Map Control en Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Adrescoördinaten instellen
> * Query uitvoeren op routeservice voor routebeschrijving naar nuttige plaats

> [!div class="nextstepaction"]
> [Volledige broncode weergeven](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Bekijk live voorbeeld](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

De volgende zelfstudie laat zien hoe u een routequery maakt met beperkingen zoals de manier van reizen of het type lading, en meerdere routes op dezelfde kaart weergeeft.

> [!div class="nextstepaction"]
> [Routes vinden voor verschillende manieren van reizen](./tutorial-prioritized-routes.md)
