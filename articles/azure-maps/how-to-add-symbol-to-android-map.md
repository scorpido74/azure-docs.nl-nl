---
title: Een symboollaag toevoegen aan Android-kaarten | Microsoft Azure Maps
description: In dit artikel leert u hoe u puntgegevens op een kaart renderen door er een symboollaag aan toe te voegen met de Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335575"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Een symboollaag toevoegen aan een kaart met Azure Maps Android SDK

In dit artikel ziet u hoe u puntgegevens uit een gegevensbron weergeven als symboollaag op een kaart met behulp van de Azure Maps Android SDK.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel volledig wilt volgen, moet u [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installeren om een kaart te laden.

## <a name="add-a-symbol-layer"></a>Een symboollaag toevoegen

Als u een markering op de kaart wilt toevoegen met de symboollaag, voert u de onderstaande stappen uit:

1.  > **Herbewerkingsindeling** >  **res****activity_main.xml** bewerken, zodat het lijkt op de volgende XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Kopieer het volgende codefragment naar de methode `MainActivity.java` **onCreate()** van uw klasse.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Het codefragment hierboven verkrijgt eerst een azure maps-kaartbeheerinstantie met behulp van de terugroepmethode **onReady().** Vervolgens wordt een gegevensbronobject gemaakt met de klasse **DataSource** en wordt het aan de kaart toegevoegd. Vervolgens wordt er een **functie** met een puntgeometrie aan toegevoegd. Een rode markeringsafbeelding wordt vervolgens ingesteld als pictogram voor het symbool. Een **symboollaag** gebruikt tekst of pictogrammen om op punten gebaseerde gegevens die in de gegevensbron zijn verpakt, weer te geven als symbool op de kaart. Vervolgens wordt een symboollaag gemaakt en wordt de gegevensbron aan deze laag doorgegeven om te renderen en vervolgens toegevoegd aan de lagen van de kaart.
    
    Na het toevoegen van `MainActivity.java` de code fragment hierboven, moet je eruit zien als de onderstaande:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
Op dit punt, als u uw toepassing uitvoert zou u een teller op de kaart moeten zien, zoals hier getoond:

<center>

![Android-kaartpin](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Volgende stappen

Zie het als nodig om meer dingen aan je kaart toe te voegen:

> [!div class="nextstepaction"]
> [Shapes toevoegen aan een Android-kaart](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)