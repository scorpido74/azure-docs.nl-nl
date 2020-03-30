---
title: Een tegellaag toevoegen aan Android-kaarten | Microsoft Azure Maps
description: In dit artikel leert u hoe u een tegellaag op een kaart weergeeft met de Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335567"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Een tegellaag toevoegen aan een kaart met de Azure Maps Android SDK

In dit artikel ziet u hoe u een tegellaag op een kaart renderen met de Azure Maps Android SDK. Met tegellagen u afbeeldingen boven op basiskaarttegels van Azure Maps plaatsen. Meer informatie over azure maps-tegelsysteem is te vinden in de [zoomniveaus en tegelrasterdocumentatie.](zoom-levels-and-tile-grid.md)

Een tegellaag laadt tegels van een server. Deze afbeeldingen kunnen vooraf worden weergegeven en opgeslagen zoals elke andere afbeelding op een server, met behulp van een naamgevingsconventie die de tegellaag begrijpt. Of deze beelden kunnen worden weergegeven met een dynamische service die de beelden in de buurt van real-time genereert. Er zijn drie verschillende tegelservicenaamgevingsconventies die worden ondersteund door de klasse Azure Maps TileLayer:

* X, Y, Zoomnotatie - Op basis van het zoomniveau is x de kolom en y is de rijpositie van de tegel in het tegelraster.
* Quadkey-notatie - Combinatie x, y, zoominformatie in op één tekenreekswaarde die een unieke id voor een tegel is.
* Selectiekader - Selectiekadercoördinaten kunnen worden gebruikt om `{west},{south},{east},{north}` een afbeelding op te geven in de indeling die vaak wordt gebruikt door [Web Mapping Services (WMS).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> Een TileLayer is een geweldige manier om grote datasets op de kaart te visualiseren. Niet alleen kan een tegellaag uit een afbeelding worden gegenereerd, maar kunnen vectorgegevens ook als tegellaag worden weergegeven. Door vectorgegevens als een tegellaag weer te geven, hoeft het kaartbesturingselement alleen de tegels te laden die veel kleiner kunnen zijn in bestandsgrootte dan de vectorgegevens die ze vertegenwoordigen. Deze techniek wordt gebruikt door velen die miljoenen rijen gegevens op de kaart moeten renderen.

De tegel-URL die in een tegellaag wordt doorgegeven, moet een http/https-URL zijn naar een TileJSON-bron of een URL-sjabloon voor tegels die de volgende parameters gebruikt: 

* `{x}`- X-positie van de tegel. Ook `{y}` behoeften `{z}`en .
* `{y}`- Y positie van de tegel. Ook `{x}` behoeften `{z}`en .
* `{z}`- Zoomniveau van de tegel. Ook `{x}` behoeften `{y}`en .
* `{quadkey}`- Tile quadkey-id op basis van de naamconventie van het Bing Maps-tegelsysteem.
* `{bbox-epsg-3857}`- Een selectielijst met `{west},{south},{east},{north}` het formaat in het EPSG 3857 Spatial Reference System.
* `{subdomain}`- Een tijdelijke aanduiding voor de subdomeinwaarden, als de subdomeinwaarde is opgegeven.

## <a name="prerequisites"></a>Vereisten

Als u het proces in dit artikel wilt voltooien, moet u [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installeren om een kaart te laden.


## <a name="add-a-tile-layer-to-the-map"></a>Een tegellaag toevoegen aan de kaart

 In dit voorbeeld ziet u hoe u een tegellaag maakt die naar een set tegels verwijst. Deze tegels maken gebruik van het betegelsysteem "x, y, zoom". De bron van deze tegellaag is een weerradar overlay van de [Iowa Environmental Mesonet van Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

U een tegellaag aan de kaart toevoegen door de onderstaande stappen te volgen.

1. Bewerk **opnieuw > lay-out > activity_main.xml,** zodat het lijkt op de onderstaande:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
    
        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Kopieer het volgende codefragment hieronder naar de `MainActivity.java` methode **onCreate()** van uw klasse.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Het codefragment hierboven verkrijgt eerst een azure maps-kaartbeheerinstantie met behulp van de terugroepmethode **onReady().** Vervolgens wordt `TileLayer` een object gemaakt en wordt een opgemaakte **url van xyz-tegels** in de `tileUrl` optie doorgegeven. De dekking van de laag `0.8` is ingesteld op en aangezien de tegels van de tegelservice die `tileSize` worden gebruikt tegels van 256 pixels zijn, wordt deze informatie doorgegeven aan de optie. De tegellaag wordt vervolgens doorgegeven aan de maps layer manager.

    Na het toevoegen van `MainActivity.java` de code fragment hierboven, moet je eruit zien als de onderstaande:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

Als u uw toepassing nu uitvoert, ziet u een regel op de kaart zoals hieronder te zien:

<center>

![Android-kaartlijn](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over manieren om kaartstijlen in te stellen

> [!div class="nextstepaction"]
> [Kaartstijlen wijzigen in Android-kaarten](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)