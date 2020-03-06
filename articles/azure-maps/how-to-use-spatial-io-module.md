---
title: De Azure Maps ruimtelijke IO-module gebruiken | Microsoft Azure kaarten
description: Meer informatie over het gebruik van de ruimtelijke IO-module die wordt meegeleverd met de Azure Maps Web-SDK. Deze module biedt krachtige functies waarmee ontwikkel aars gemakkelijk ruimtelijke gegevens kunnen integreren met de Azure Maps Web-SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c309473529666d369e8accd1617021249867fb19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371036"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>De Azure Maps ruimtelijke IO-module gebruiken

De Azure Maps Web-SDK biedt de **ruimtelijke IO-module**, die ruimtelijke gegevens integreert met de Azure Maps Web-SDK met behulp van Java script of type script. Met de krachtige functies in deze module kunnen ontwikkel aars het volgende doen:

- [Gegevens lezen en schrijven naar algemene ruimtelijke bestanden](spatial-io-read-write-spatial-data.md). Ondersteunde bestands indelingen zijn: KML, KMZ, GPX, GeoRSS, GML en CSV-bestanden met kolommen met ruimtelijke gegevens.
- [Maak verbinding met Open Geospatial Consortium-Services (OGC) en integreer met Azure Maps Web-SDK. Overlay (Web Mapping Services) en WMTS (Web Maps tile Services) als lagen op de kaart.](spatial-io-add-ogc-map-layer.md)
- [Query's uitvoeren op gegevens in een Web Feature Service (WFS)](spatial-io-connect-wfs-service.md).
- [Bedekkings complexe gegevens sets die stijl informatie bevatten en automatisch moeten worden weer gegeven](spatial-io-add-simple-data-layer.md).
- [Maak gebruik van snelle XML-en met scheidings tekens voor bestands lezers en Writer](spatial-io-core-operations.md).

In deze hand leiding leert u hoe u de ruimtelijke IO-module kunt integreren en gebruiken in een webtoepassing.

## <a name="prerequisites"></a>Vereisten

Voordat u de ruimtelijke IO-module kunt gebruiken, moet u [een Azure Maps account maken](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) en [de primaire abonnements sleutel voor uw account ophalen](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>De ruimtelijke IO-module installeren

U kunt de Azure Maps ruimtelijke IO-module laden met een van de twee opties:

* De wereld wijd gehoste Azure CDN voor de Azure Maps Spatial IO-module. Voor deze optie voegt u een verwijzing naar de Java script toe aan het `<head>`-element van het HTML-bestand.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* De bron code voor [Azure-Maps-Spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) kan lokaal worden geladen en vervolgens worden gehost met uw app. Dit pakket bevat ook type script definities. Voor deze optie gebruikt u de volgende opdracht om het pakket te installeren:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Voeg vervolgens in het element `<head>` van het HTML-document een verwijzing toe naar het Java script:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>De ruimtelijke IO-module gebruiken

1. Maak een nieuw HTML-bestand.

2. Laad de Azure Maps Web-SDK en Initialiseer het kaart besturings element. Raadpleeg de hand leiding voor het [beheer van Azure Maps-kaarten](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) voor meer informatie. Zodra u klaar bent met deze stap, ziet uw HTML-bestand er als volgt uit:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
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
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Laad de Azure Maps ruimtelijke IO-module. Gebruik voor deze oefening het CDN voor de Azure Maps ruimtelijke IO-module. Voeg de onderstaande referentie toe aan het `<head>`-element van uw HTML-bestand:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Initialiseer een `datasource`en voeg de gegevens bron toe aan de kaart. Initialiseer een `layer`en voeg de gegevens bron toe aan de kaartLaag. Vervolgens worden zowel de gegevens bron als de laag weer gegeven. Voordat u omlaag schuift om de volledige code in de volgende stap te bekijken, moet u nadenken over de beste plaatsen om de fragmenten van de gegevens bron en laag code te plaatsen. Als u de kaart programmatisch wilt bewerken, moet u wachten tot de kaart resource gereed is.

    ```javascript
    var datasource, layer;
    ```

    en

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. De HTML-code moet eruitzien als de volgende code. Dit voor beeld laat zien hoe u een XML-bestand kunt lezen vanuit een URL. Vervolgens kunt u de functie gegevens van het bestand op de kaart laden en weer geven. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
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
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Vergeet niet om `<Your Azure Maps Key>` te vervangen door uw primaire sleutel. Open uw HTML-bestand en er worden resultaten weer gegeven die vergelijkbaar zijn met de volgende afbeelding:

    <center>

    ![Voor beeld van ruimtelijke gegevens](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Volgende stappen

De functie die we hier hebben gedemonstreerd, is slechts een van de vele beschik bare functies in de ruimtelijke IO-module. Lees de onderstaande hand leidingen voor meer informatie over het gebruik van andere functies in de ruimtelijke IO-module:

> [!div class="nextstepaction"]
> [Een eenvoudige gegevenslaag toevoegen](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Een OGC-kaartLaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Kern bewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevens indeling](spatial-io-supported-data-format-details.md)

Raadpleeg de Azure Maps ruimtelijke IO-documentatie:

> [!div class="nextstepaction"]
> [Azure Maps ruimtelijk IO-pakket](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
