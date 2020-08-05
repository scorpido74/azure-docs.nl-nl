---
title: 'Zelfstudie: Een route naar een locatie zoeken | Microsoft Azure Maps'
description: In deze zelfstudie wordt beschreven hoe u een route naar een locatie (nuttige plaats) op een kaart kunt weergeven met behulp van de routeringsservice van Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: d6a526e23be47c9e05de0c9064bdb4095a49d132
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287418"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Zelfstudie: Zoeken naar een nuttige plaats met Azure Maps

Deze zelfstudie laat zien hoe u uw Azure Maps-account en de Route Service SDK gebruikt om de route naar uw nuttige plaats te vinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Adrescoördinaten instellen
> * Query uitvoeren op Route Service voor route naar nuttige plaats

## <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, volgt u de instructies in [Een account maken](quick-demo-map-app.md#create-an-azure-maps-account) om een abonnement met S1-prijsniveau te maken. Volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om uw primaire sleutel voor uw account op te halen. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

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

3. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Vervang de tekenreeks `<Your Azure Maps Key>` door de primaire sleutel die u hebt gekopieerd uit uw Maps-account.

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

1. Voeg de volgende JavaScript-code toe nadat de kaart is geïnitialiseerd.

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
    
    In de gebeurtenis-handler `ready` voor kaarten wordt een gegevensbron gemaakt waarin de routelijn en de begin- en eindpunten worden opgeslagen. Er wordt een lijnlaag gemaakt en aan de gegevensbron gekoppeld waarmee wordt gedefinieerd hoe de routelijn wordt weergegeven. De routelijn wordt weergegeven in een mooie blauwe kleur. Deze heeft een breedte van vijf pixels, ronde lijnkoppelingen en uiteinden. Als u de laag toevoegt aan de kaart wordt een tweede parameter met de waarde `'labels'` doorgegeven. Hiermee wordt bepaald dat deze laag onder de kaartlabels wordt weergegeven. Zo kunt u er zeker van zijn dat de routelijnen de straatlabels niet bedekken. Er wordt een symboollaag gemaakt en aan de gegevensbron gekoppeld. In deze laag wordt aangegeven hoe de begin- en eindpunten worden weergegeven. In dit geval zijn er expressies toegevoegd om de afbeelding van het pictogram en de tekstlabelgegevens op te halen uit de eigenschappen van elk puntobject. 
    
2. Voor deze zelfstudie stelt u Microsoft in als beginpunt en een benzinestation in Seattle als eindpunt. Voeg de volgende code toe aan de gebeurtenis-handler `ready` voor kaarten.

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

    Met deze code worden twee [GeoJSON-puntobjecten](https://en.wikipedia.org/wiki/GeoJSON) gemaakt die voor de begin- en eindpunten van de route staan en worden de punten toegevoegd aan de gegevensbron. Aan elk punt wordt een eigenschap `title` en `icon` toegevoegd. In het laatste blok wordt met behulp van de eigenschap [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart de cameraweergave ingesteld met de breedtegraad en de lengtegraad van het begin- en eindpunt.

3. Sla het bestand **MapRoute.html** op en vernieuw de browser. De kaart is nu gecentreerd op Seattle en u ziet de blauwe speld die het beginpunt aangeeft en de ronde blauwe speld die het eindpunt aangeeft.

   ![Begin- en eindpunt van routes op de kaart weergeven](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Routebeschrijving ophalen

In deze sectie wordt beschreven hoe u de API voor Azure Maps-routeservices gebruikt. De API voor routeservices zoekt de route van een bepaald beginpunt naar een eindpunt. Deze service beschikt over API's om de *snelste*, *kortste*, *zuinigste* of *leukste* route tussen twee locaties te plannen. Ook kunnen gebruikers met deze service in de toekomst routes plannen met behulp van de uitgebreide historische verkeersdatabase van Azure. Gebruikers kunnen de voorspelling van de routeduur voor een gekozen dag en tijd bekijken. Zie [Routebeschrijvingen ophalen](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) voor meer informatie. Alle volgende functies moeten worden toegevoegd **in de eventListener ready voor de kaart** zodat deze worden geladen wanneer toegang kan worden verkregen tot de kaartresources.

1. Voeg in de functie GetMap het volgende toe aan de JavaScript-code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   De `SubscriptionKeyCredential` maakt een `SubscriptionKeyCredentialPolicy` voor het verifiëren van HTTP-aanvragen voor Azure Maps met de abonnementssleutel. De `atlas.service.MapsURL.newPipeline()` neemt het beleid `SubscriptionKeyCredential` op en maakt een [pijplijn](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)instantie. De `routeURL` staat voor een URL voor [route](https://docs.microsoft.com/rest/api/maps/route)bewerkingen van Azure Maps.

2. Nadat u de referenties en de URL hebt ingesteld, voegt u de volgende JavaScript-code toe om de route van het begin- tot het eindpunt te maken. De `routeURL` vraagt de Azure Maps-routeservice om de routebeschrijving te berekenen. Met behulp van de methode `geojson.getFeatures()` wordt vervolgens uit het antwoord een verzameling GeoJSON-kenmerken geëxtraheerd en aan de gegevensbron toegevoegd.

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

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * Adrescoördinaten instellen
> * Query uitvoeren op routeservice voor routebeschrijving naar nuttige plaats

> [!div class="nextstepaction"]
> [Volledige broncode weergeven](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Livevoorbeeld weergeven](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

De volgende zelfstudie laat zien hoe u een routequery maakt met beperkingen zoals de manier van reizen of het type lading, en meerdere routes op dezelfde kaart weergeeft.

> [!div class="nextstepaction"]
> [Routes vinden voor verschillende manieren van reizen](./tutorial-prioritized-routes.md)
