---
title: 'Zelfstudie: Een Android-app migreren | Microsoft Azure Maps'
description: Een Android-app migreren van Google Maps naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209729"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Een Android-app migreren vanuit Google Maps

De Azure Maps Android SDK heeft een API-interface die vergelijkbaar is met de Web SDK. Als u met een van deze SDK's hebt ontwikkeld, zijn veel van dezelfde concepten, best practices en architecturen van toepassing.

De Azure Maps Android SDK ondersteunt een minimale Android-versie van API 21: Android 5.0.0 (Lollipop).

Alle voorbeelden worden gegeven in Java; U Kotlin echter gebruiken met de Azure Maps Android SDK.

Zie de [handleidingen voor de Azure Maps Android SDK voor](how-to-use-android-map-control-library.md)meer informatie over het ontwikkelen met de Android SDK by Azure Maps.

## <a name="load-a-map"></a>Een kaart laden

Het laden van een kaart in een Android-app met Google of Azure Maps bestaat uit vergelijkbare stappen. Wanneer u een van beide SDK's gebruikt, moet u:

- Download een API- of abonnementssleutel om toegang te krijgen tot beide platforms.
- Voeg wat XML toe aan een activiteit om aan te geven waar de kaart moet worden weergegeven en hoe deze moet worden opgemaakt.
- Overschrijf alle levenscyclusmethoden van de activiteit die de kaartweergave bevatten, naar de bijbehorende methoden in de kaartklasse. In het bijzonder moet u de volgende methoden overschrijven:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Wacht tot de kaart klaar is voordat u probeert deze te openen en te programmeren.

**Voor: Google Maps**

Als u een kaart wilt weergeven met de Google Maps SDK voor Android, worden de volgende stappen uitgevoerd:

1.  Controleer of Google Play-services zijn geïnstalleerd.
2.  Voeg een afhankelijkheid voor de Google Maps-service toe aan het **gradle.build-bestand van** de module: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Voeg een Google Maps API-sleutel toe in het toepassingsgedeelte van het **bestand google\_maps\_api.xml:**
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Voeg een kaartfragment toe aan de hoofdactiviteit:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  In het **bestand MainActivity.java** moet u de Google Maps SDK importeren. Alle levenscyclusmethoden doorsturen van de activiteit die de kaartweergave bevat naar de bijbehorende methoden in de kaartklasse. Een `MapView` instantie ophalen uit het `getMapAsync(OnMapReadyCallback)` kaartfragment met behulp van de methode. De `MapView` automatisch initialiseert de kaarten systeem en de weergave. Bewerk het **mainactivity.java-bestand** als volgt:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Wanneer u een toepassing uitvoert, wordt het kaartbesturingselement geladen zoals in de volgende afbeelding.

<center>

![Eenvoudige Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Na: Azure Maps**

Als u een kaart wilt weergeven met de Azure Maps SDK voor Android, moeten de volgende stappen worden uitgevoerd:

1. Open het **bestand build.gradle op het** hoogste niveau en voeg de volgende code toe aan de sectie alle **projecten:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Werk uw **app/build.gradle bij** en voeg er de volgende code aan toe:
    
    1. Zorg ervoor dat de **minSdkVersion** van uw project op API 21 of hoger staat.

    2. Voeg de volgende code toe aan de Android-sectie:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Werk uw afhankelijkhedenblok bij. Voeg een nieuwe afhankelijkheidslijn voor implementatie toe voor de nieuwste Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > De Azure Maps Android SDK wordt regelmatig geüpgraded en verbeterd. U het [besturingselement voor Android-kaarten](how-to-use-android-map-control-library.md) gebruiken om het nieuwste Azure Maps-versienummer te krijgen. U ook het versienummer instellen van 0,2 naar 0+ om uw code altijd naar de nieuwste versie te laten wijzen.
    
    4. Ga naar **Bestand** op de werkbalk en klik op **Project synchroniseren met Gradle-bestanden**.
3. Een kaartfragment toevoegen aan de \> \> hoofdactiviteit (hoofdactiviteit main.xml van resources-indelingsactiviteit):\_
    
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
            />
    </FrameLayout>
    ```

4. In het **MainActivity.java-bestand** moet u:
    
    * Importeert de Azure Maps SDK
    * Uw Azure Maps-verificatiegegevens instellen
    * De instantie kaartbesturingselement in de **methode onCreate**

     Stel de verificatiegegevens `AzureMaps` in `setSubscriptionKey` de `setAadProperties` klasse in met de of e-methoden. Deze globale update zorgt ervoor dat u uw verificatiegegevens aan elke weergave toevoegt.

    Het kaartbesturingselement bevat zijn eigen levenscyclusmethoden voor het beheren van de OpenGL-levenscyclus van Android. Deze methodes moeten direct van bevat Activiteit worden geroepen. Als u de levenscyclusmethoden van het kaartbesturingselement correct wilt aanroepen, moet u de volgende levenscyclusmethoden in de activiteit die het kaartbesturingselement bevat, overschrijven. Roep de desbetreffende kaartbesturingselementmethode aan.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Bewerk het **mainactivity.java-bestand** als volgt:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

Als u uw toepassing uitvoert, wordt het kaartbesturingselement geladen zoals in de volgende afbeelding.

<center>

![Eenvoudige Azure-kaarten](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Het besturingselement Azure Maps ondersteunt meer uitzoomen en biedt meer een wereldbeeld.

> [!TIP]
> Als u een Android-emulator op een Windows-machine gebruikt, wordt de kaart mogelijk niet weergegeven vanwege conflicten met OpenGL en versnelde grafische rendering van software. Het volgende heeft voor sommige mensen gewerkt om dit probleem op te lossen. Open de AVD-manager en selecteer het virtuele apparaat dat u wilt bewerken. Schuif omlaag in het deelvenster **Configuratie verifiëren.** Stel in de sectie **Emulated Performance** de optie **Afbeeldingen** in **op Hardware**.

## <a name="localizing-the-map"></a>De kaart lokaliseren

Lokalisatie is belangrijk als uw doelgroep verspreid is over meerdere landen of verschillende talen spreekt.

**Voor: Google Maps**

Voeg de volgende `onCreate` code toe aan de methode om de taal van de kaart in te stellen. De code moet worden toegevoegd voordat u de contextweergave van de kaart instelt. De "fr" taalcode beperkt de taal tot frans.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Hier is een voorbeeld van Google Maps met de taal ingesteld op "fr".

<center>

![Lokalisatie van Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Na: Azure Maps**

Azure Maps biedt drie verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De eerste optie is om de taal- `AzureMaps` en regionale weergave-informatie door te geven aan de klas. Deze optie maakt `setLanguage` `setView` gebruik van de statische en methoden wereldwijd. Dit betekent dat de standaardtaal en de regionale weergave zijn ingesteld op alle Azure Maps-besturingselementen die in uw app zijn geladen. In dit voorbeeld wordt Frans ingesteld met behulp van de taalcode "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

De tweede optie is om de taal door te geven en informatie weer te geven aan de XML-code voor kaartbeheer.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

De derde optie is het programmeren van `setStyle` de taal en de regionale kaartweergave met behulp van de kaartmethode. Met deze optie wordt de taal en de regionale weergave bijgewerkt wanneer de code wordt uitgevoerd.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Hier is een voorbeeld van Azure Maps met de taal ingesteld op "fr-FR".

<center>

![Lokalisatie van Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Bekijk de volledige lijst [met ondersteunde talen](supported-languages.md).

## <a name="setting-the-map-view"></a>De kaartweergave instellen

Dynamische kaarten in zowel Azure Maps als Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties door de juiste methoden aan te roepen. Laten we de kaart satellietluchtbeelden weergeven, de kaart centreren boven een locatie met coördinaten en het zoomniveau wijzigen. Voor dit voorbeeld gebruiken we breedtegraad: 35.0272, lengte: -111.0225 en zoomniveau van 15.

**Voor: Google Maps**

De camera van Google Maps kaartbesturingselement kan `moveCamera` programmatisch worden verplaatst met behulp van de methode. Met `moveCamera` de methode u het midden van de kaart en een zoomniveau opgeven. De `setMapType` methode wijzigt het type kaart dat wordt weergegeven.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps-setweergave](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Google Maps gebruikt tegels met afmetingen van 256 pixels, terwijl Azure Maps een grotere tegel van 512 pixels gebruikt. Dit vermindert het aantal netwerkaanvragen dat Azure Maps nodig heeft om hetzelfde kaartgebied te laden als Google Maps. Als u datzelfde zichtbaar gebied wilt bereiken als een kaart in Google Maps, moet u het zoomniveau dat in Google Maps wordt gebruikt, met één aftrekken wanneer u Azure Maps gebruikt. 

**Na: Azure Maps**

Zoals eerder vermeld, trekt u het zoomniveau dat in Google Maps wordt gebruikt door hetzelfde zichtbaar gebied in Azure Maps af te trekken. Gebruik in dit geval een zoomniveau van 14.

De eerste kaartweergave kan worden ingesteld in XML-kenmerken op het kaartbesturingselement.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

De kaartweergave kan worden geprogrammeerd `setCamera` `setStyle` met behulp van de kaarten en methoden.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Weergave azure Maps-set](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Aanvullende bronnen:**

- [Ondersteunde kaartstijlen](supported-map-styles.md)

## <a name="adding-a-marker"></a>Een markering toevoegen

Puntgegevens worden vaak weergegeven met behulp van een afbeelding op de kaart. Deze afbeeldingen worden markers, punaises, pins of symbolen genoemd. De volgende voorbeelden geven puntgegevens weer als markeringen op de kaart op breedtegraad: 51,5, lengte: -0,2.

**Voor: Google Maps**

Met Google Maps worden markeringen toegevoegd `addMarker` met behulp van de kaartmethode.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps-markering](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Na: Azure Maps**

Geef in Azure Maps puntgegevens op de kaart door eerst de gegevens toe te voegen aan een gegevensbron. Die gegevensbron worden vervolgens verbonden met een symboollaag. De gegevensbron optimaliseert het beheer van ruimtelijke gegevens in de kaartbesturing. De symboollaag geeft aan hoe puntgegevens worden weergegeven met behulp van een afbeelding of tekst.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Azure Maps-markering](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

Aangepaste afbeeldingen kunnen worden gebruikt om punten op een kaart weer te geven. De kaart in onderstaande voorbeelden maakt gebruik van een aangepaste afbeelding om een punt op de kaart weer te geven. Het punt is op breedtegraad: 51,5 en lengte: -0,2. Het anker compenseert de positie van de markering, zodat het punt van het punaisepictogram wordt uitgelijnd met de juiste positie op de kaart.

<center>

![gele punaise afbeelding](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_punaise.png</center>

In beide voorbeelden wordt de bovenstaande afbeelding toegevoegd aan de opneembare map van de bronnen voor apps.

**Voor: Google Maps**

Met Google Maps kunnen aangepaste afbeeldingen worden gebruikt voor markeringen. Aangepaste afbeeldingen laden met `icon` de optie van de markering. Als u het punt van de afbeelding `anchor` wilt uitlijnen op de coördinaat, gebruikt u de optie. Het anker is relatief ten opzichte van de afmetingen van de afbeelding. In dit geval is het anker 0,2 eenheden breed en 1 eenheid hoog.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Aangepaste google maps-markering](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Na: Azure Maps**

Symboollagen in Azure Maps ondersteunen aangepaste afbeeldingen, maar eerst moet de afbeelding worden geladen naar de kaartbronnen en een unieke id worden toegewezen. Vervolgens moet de symboollaag naar deze id verwijzen. Het symbool uitlijnen op het juiste punt `iconOffset` op de afbeelding met de optie. De pictogramverschuiving is in pixels. Standaard is de verschuiving relatief ten opzichte van het onderste midden van de `iconAnchor` afbeelding, maar deze verschuivingswaarde kan worden aangepast met behulp van de optie. In dit `iconAnchor` voorbeeld `"center"`wordt de optie ingesteld op . Het maakt gebruik van een pictogram verschuiving om de afbeelding vijf pixels naar rechts en 15 pixels omhoog te laten uitlijnen met het punt van de punaise afbeelding.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Aangepaste azure maps-markering](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Een polylijn toevoegen

Polylijnen worden gebruikt om een lijn of pad op de kaart weer te geven. In de volgende voorbeelden ziet u hoe u een gestippelde polylijn op de kaart maakt.

**Voor: Google Maps**

Met Google Maps maakt u `PolylineOptions` een polylijn weer met behulp van de klasse. Voeg de polylijn toe `addPolyline` aan de kaart met behulp van de methode. Stel de lijnkleur `color` in met de optie. Stel de lijnbreedte `width` in met de optie. Voeg met de `pattern` optie een lijnstreepjesarray toe.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps-polylijn](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Na: Azure Maps**

In Azure Maps worden `LineString` polylijnen aangeroepen of `MultiLineString` objecten. Voeg deze objecten toe aan een gegevensbron en maak ze weer met behulp van een lijnlaag. Stel de lijnbreedte `strokeWidth` in met de optie. Voeg met de `strokeDashArray` optie een lijnstreepjesarray toe.

De lijnbreedte en de 'pixel'-eenheden van de dasharray in de Azure Maps Web SDK zijn dezelfde als in de Google Maps-service. Beide accepteren dezelfde waarden om dezelfde resultaten te produceren.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps-polylijn](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Polygonen worden gebruikt om een gebied op de kaart weer te geven. In de volgende voorbeelden ziet u hoe u een veelhoek maakt. Deze veelhoek vormt een driehoek op basis van de middelste coördinaat van de kaart.

**Voor: Google Maps**

Met Google Maps maakt u `PolygonOptions` een veelhoek weer met behulp van de klasse. Voeg de veelhoek toe `addPolygon` aan de kaart met behulp van de methode. Stel de vul- en `fillColor` `strokeColor` lijnkleuren in met respectievelijk de opties en opties. Stel de lijnbreedte `strokeWidth` in met de optie.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Maps-veelhoek](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Na: Azure Maps**

Voeg in Azure `Polygon` `MultiPolygon` Maps een gegevensbron toe en maak deze weer op de kaart met lagen. Het gebied van een veelhoek in een veelhoeklaag weergeven. De omtrek van een veelhoek weergeven met behulp van een lijnlaag. Stel de lijnkleur en `strokeColor` `strokeWidth` -breedte in met de opties en opties.

De lijnbreedte- en dasharray -pixeleenheden in Azure Maps Web SDK sluiten aan op de respectievelijke eenheden in Google Maps. Beide accepteren dezelfde waarden en produceren dezelfde resultaten.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Polygoon Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Een tegellaag bedekken

 Gebruik Tegellagen om laagafbeeldingen te bedekken die zijn opgesplitst in kleinere betegelde afbeeldingen, die overeenkomen met het betegelingssysteem kaarten. Deze aanpak is een veel voorkomende manier om laagafbeeldingen of grote gegevenssets te overlopen. Tegellagen worden afbeeldingsoverlays genoemd in Google Maps.

De volgende voorbeelden overlay een weer radar tegel laag van Iowa Environmental Mesonet van Iowa State University. De tegels zijn 256 pixels groot.

**Voor: Google Maps**

Met Google Maps kan een tegellaag bovenop de kaart worden gelegd. Gebruik `TileOverlayOptions` de klas. Voeg de tegellaag toe `addTileLauer` aan de kaart met de methode. Om de tegels semi-transparant `transparency` te maken, is de optie ingesteld op 0,2 of 20% transparant.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Tegellaag van Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Na: Azure Maps**

Een tegellaag kan op dezelfde manier aan de kaart worden toegevoegd als elke andere laag. Een opgemaakte URL met tijdelijke aanduidingen x, y en zoom; `{x}`, `{y}` `{z}` wordt respectievelijk gebruikt om de laag te vertellen waar toegang te krijgen tot de tegels. Ook tegellagen in Azure `{quadkey}` `{bbox-epsg-3857}`Maps `{subdomain}` ondersteunen en tijdelijke aanduidingen. Om de tegellaag semi-transparant te maken, wordt een dekkingswaarde van 0,8 gebruikt. Dekking en transparantie, hoewel vergelijkbaar, gebruiken omgekeerde waarden. Als u tussen beide opties wilt converteren, trekt u de waarde van de nummer één af.

> [!TIP]
> In Azure Maps is het handig om lagen onder andere lagen weer te geven, inclusief basiskaartlagen. Ook is het vaak wenselijk om tegellagen onder de kaartlabels weer te geven, zodat ze gemakkelijk te lezen zijn. De `map.layers.add` methode neemt een tweede parameter die de id van de laag waarin de nieuwe laag hieronder in te voegen. Als u een tegellaag onder de kaartlabels wilt invoegen, kan de volgende code worden gebruikt:`map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Tegellaag Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Verkeer weergeven

Zowel Azure Maps als Google Maps hebben opties om verkeersgegevens te bedekken.

**Voor: Google Maps**

Met Google Maps kunnen verkeersstroomgegevens boven op de kaart worden gelegd `setTrafficEnabled` door de methode van de kaart trouw te geven aan de methode van de kaart.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps-verkeer](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Na: Azure Maps**

Azure Maps biedt verschillende opties voor het weergeven van verkeer. Verkeersincidenten, zoals wegafsluitingen en ongevallen, kunnen als pictogrammen op de kaart worden weergegeven. Verkeersstroom en kleurgecodeerde wegen kunnen op de kaart worden bedekt. De kleuren kunnen worden gewijzigd om te worden weergegeven ten opzichte van de geboekte snelheidslimiet, ten opzichte van de normale verwachte vertraging, of de absolute vertraging. Incidentgegevens in Azure Maps worden elke minuut bijgewerkt en de stroomgegevens worden elke twee minuten bijgewerkt.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps-verkeer](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Android SDK.

> [!div class="nextstepaction"]
> [Het Android-kaartbesturingselement gebruiken](how-to-use-android-map-control-library.md)
