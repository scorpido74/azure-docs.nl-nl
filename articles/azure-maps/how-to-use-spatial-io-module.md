---
title: De ruimtelijke IO-module Azure Maps gebruiken | Microsoft Azure Maps
description: Meer informatie over het gebruik van de Spatial IO-module van de Azure Maps Web SDK. Deze module biedt robuuste functies om het voor ontwikkelaars gemakkelijk te maken om ruimtelijke gegevens te integreren met de Azure Maps-websdk.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcfeebc1fcb96cfdf6ea802293eb4027f339815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335216"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>De Azure Maps Spatial IO-module gebruiken

De Azure Maps Web SDK biedt de **Spatial IO-module**, die ruimtelijke gegevens integreert met de Azure Maps web SDK met behulp van JavaScript of TypeScript. De robuuste functies in deze module stellen ontwikkelaars in staat om:

- [Lees en schrijf veelvoorkomende ruimtelijke gegevensbestanden.](spatial-io-read-write-spatial-data.md) Ondersteunde bestandsindelingen zijn: KML, KMZ, GPX, GeoRSS, GML, GeoJSON en CSV bestanden met kolommen met ruimtelijke informatie. Ondersteunt ook bekende tekst (WKT).
- [Maak verbinding met OGC-services (Open Geospatial Consortium) en integreer met Azure Maps web SDK. Overlay Web Map Services (WMS) en Web Map Tile Services (WMTS) als lagen op de kaart](spatial-io-add-ogc-map-layer.md).
- [Querygegevens in een Web Feature Service (WFS)](spatial-io-connect-wfs-service.md).
- [Overlay complexe gegevenssets die stijlinformatie bevatten en deze automatisch laten renderen met behulp van minimale code](spatial-io-add-simple-data-layer.md).
- [Maak gebruik van snelle XML- en afgebakende bestandslezer- en schrijverklassen](spatial-io-core-operations.md).

In deze handleiding leren we hoe we de Spatial IO module kunnen integreren en gebruiken in een webapplicatie.

> [!WARNING]
> Gebruik alleen gegevens en services die afkomstig zijn van een bron die u vertrouwt, vooral als u ernaar verwijst vanuit een ander domein. De ruimtelijke IO-module neemt wel stappen om risico's te minimaliseren, maar de veiligste aanpak is om te beginnen geen danagerous-gegevens in uw toepassing toe te staan. 

## <a name="prerequisites"></a>Vereisten

Voordat u de Module Ruimtelijke IO gebruiken, moet u [een Azure Maps-account aanmaken](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) en [de primaire abonnementssleutel voor uw account opvragen.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>Het installeren van de Spatial IO module

U de ruimtelijke IO-module Azure Maps laden met een van de twee opties:

* De wereldwijd gehoste Azure CDN voor de azure maps ruimtelijke IO-module. Voor deze optie voegt u een verwijzing `<head>` toe naar het JavaScript in het element van het HTML-bestand.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* De broncode voor [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) kan lokaal worden geladen en vervolgens worden gehost met uw app. Dit pakket bevat ook TypeScript-definities. Gebruik voor deze optie de volgende opdracht om het pakket te installeren:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Voeg vervolgens een verwijzing toe naar `<head>` het JavaScript in het element van het HTML-document:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>De module Ruimtelijke IO gebruiken

1. Een nieuw HTML-bestand maken.

2. Laad de Azure Maps Web SDK en initialiseer het kaartbesturingselement. Zie de handleiding voor [kaartbeheer van Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) voor de details. Zodra u klaar bent met deze stap, moet uw HTML-bestand er als volgt uitzien:

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

2. Laad de ruimtelijke IO-module Azure Maps. Gebruik voor deze oefening het CDN voor de spatial IO-module Azure Maps. Voeg de onderstaande `<head>` verwijzing toe aan het element van uw HTML-bestand:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Initialiseer `datasource`een en voeg de gegevensbron toe aan de kaart. Initialiseer `layer`a en voeg de gegevensbron toe aan de kaartlaag. Maak vervolgens zowel de gegevensbron als de laag weer. Voordat u naar beneden scrolt om de volledige code in de volgende stap te zien, moet u nadenken over de beste plaatsen om de gegevensbron- en laagcodefragmenten te plaatsen. Bedenk dat, voordat we programmatisch manipuleren van de kaart, moeten we wachten tot de kaart bron klaar zijn.

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

4. Als je alles bij elkaar zet, moet je HTML-code er uitzien als de volgende code. In dit voorbeeld wordt uitgelegd hoe u een XML-bestand uit een URL lezen. Laad vervolgens de functiegegevens van het bestand op de kaart en geef deze weer. 

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

5. Vergeet niet `<Your Azure Maps Key>` om te vervangen door uw primaire sleutel. Open uw HTML-bestand en u ziet resultaten die vergelijkbaar zijn met de volgende afbeelding:

    <center>

    ![Voorbeeld van ruimtelijke gegevens](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Volgende stappen

De functie die we hier hebben gedemonstreerd is slechts een van de vele functies die beschikbaar zijn in de Spatial IO-module. Lees de onderstaande handleidingen voor meer informatie over het gebruik van andere functionaliteiten in de Spatial IO-module:

> [!div class="nextstepaction"]
> [Een eenvoudige gegevenslaag toevoegen](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ruimtelijke gegevens lezen en schrijven](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Een OGC-kaartlaag toevoegen](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Verbinding maken met een WFS-service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Benut kernactiviteiten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)

Raadpleeg de DocumenteerIO-documentatie voor Azure Maps:

> [!div class="nextstepaction"]
> [Azure Maps Spatial IO-pakket](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
