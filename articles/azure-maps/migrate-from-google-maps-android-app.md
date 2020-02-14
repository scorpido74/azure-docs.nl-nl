---
title: 'Zelf studie: een Android-app migreren | Microsoft Azure kaarten'
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
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209729"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Een Android-app migreren vanuit Google Maps

De Azure Maps Android SDK bevat een API-interface die vergelijkbaar is met de Web-SDK. Als u met een van deze Sdk's hebt ontwikkeld, zijn veel van dezelfde concepten, aanbevolen procedures en architecturen van toepassing.

De Azure Maps Android SDK ondersteunt een minimale Android-versie van API 21: Android 5.0.0 (Lollipop).

Alle voor beelden zijn opgenomen in Java. u kunt Kotlin echter gebruiken met de Azure Maps Android SDK.

Voor meer informatie over het ontwikkelen met de Android SDK door Azure Maps, raadpleegt u de [hand leidingen voor de Azure Maps ANDROID SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Een kaart laden

Het laden van een kaart in een Android-app met Google of Azure Maps bestaat uit soort gelijke stappen. Wanneer u een van de SDK gebruikt, moet u het volgende doen:

- Down load een API of abonnements sleutel voor toegang tot een van beide platforms.
- Voeg wat XML toe aan een activiteit om op te geven waar de kaart moet worden gerenderd en hoe deze moet worden ingedeeld.
- Alle levenscyclus methoden van de activiteit met de kaart weergave vervangen door de overeenkomstige methoden in de kaart klasse. In het bijzonder moet u de volgende methoden onderdrukken:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Wacht totdat de kaart gereed is voordat u deze probeert te openen en Program meren.

**Voor: Google Maps**

Als u een kaart wilt weer geven met behulp van de Google Maps SDK voor Android, kunt u de volgende stappen uitvoeren:

1.  Zorg ervoor dat Google Play Services is geïnstalleerd.
2.  Voeg een afhankelijkheid voor de Google Maps-service toe aan het bestand **gradle. build** van de module: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Voeg een Google Maps-API-sleutel toe binnen de sectie toepassing van de **Google\_mapss\_API. XML-** bestand:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Voeg een kaart fragment toe aan de hoofd activiteit:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  In het bestand **MainActivity. java** moet u de SDK van Google Maps importeren. Alle levenscyclus methoden van de activiteit met de kaart weergave door sturen naar de corresponderende procedures in de klasse map. Haal een `MapView` exemplaar op uit het kaart fragment met behulp van de methode `getMapAsync(OnMapReadyCallback)`. De `MapView` initialiseert automatisch het kaarten systeem en de weer gave. Bewerk het bestand **MainActivity. java** als volgt:

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

Wanneer u een toepassing uitvoert, wordt het kaart besturings element geladen zoals in de volgende afbeelding.

<center>

![eenvoudige Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Na: Azure Maps**

Als u een kaart wilt weer geven met behulp van de Azure Maps SDK voor Android, moeten de volgende stappen worden uitgevoerd:

1. Open het bestand **Build. gradle** op het hoogste niveau en voeg de volgende code toe aan de sectie **alle projecten** blok keren:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Werk uw **app/build. gradle** bij en voeg de volgende code toe:
    
    1. Zorg ervoor dat de **minSdkVersion** van uw project op API 21 of hoger is.

    2. Voeg de volgende code toe aan de sectie Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Werk het blok met afhankelijkheden bij. Voeg een nieuwe regel voor de implementatie afhankelijkheid toe voor de nieuwste Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > De Azure Maps Android SDK wordt regel matig geüpgraded en uitgebreid. U kunt aan de slag [met Android map control](how-to-use-android-map-control-library.md) het meest recente Azure Maps versie nummer ophalen. U kunt ook het versie nummer van ' 0,2 ' instellen op ' 0 + ' zodat uw code altijd naar de meest recente versie verwijst.
    
    4. Ga naar het **bestand** in de werk balk en klik vervolgens op **project synchroniseren met Gradle-bestanden**.
3. Voeg een kaart fragment toe aan de hoofd activiteit (resources \> indeling \> activiteit\_Main. XML):
    
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

4. In het bestand **MainActivity. java** moet u het volgende doen:
    
    * Hiermee wordt de Azure Maps SDK geïmporteerd
    * Uw Azure Maps-verificatie-informatie instellen
    * Het kaart besturings exemplaar ophalen in de methode **onCreate**

     Stel de verificatie gegevens in de klasse `AzureMaps` in met behulp van de `setSubscriptionKey`-of `setAadProperties`-methoden. Deze globale update, zorg ervoor dat u uw verificatie gegevens toevoegt aan elke weer gave.

    Het kaart besturings element bevat eigen levenscyclus methoden voor het beheren van de OpenGL-levens duur van Android. Deze methoden moeten rechtstreeks vanuit de Inge sloten activiteit worden aangeroepen. Als u de levenscyclus methoden van het kaart besturings element correct wilt aanroepen, moet u de volgende levenscyclus methoden overschrijven in de activiteit die het kaart besturings element bevat. Roep de desbetreffende kaart beheer methode aan.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Bewerk het bestand **MainActivity. java** als volgt:
    
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

Als u uw toepassing uitvoert, wordt het kaart besturings element geladen zoals in de volgende afbeelding.

<center>

![eenvoudige Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

U ziet dat het Azure Maps besturings element ondersteunt meer uitzoomen en biedt meer een wereld wijde weer gave.

> [!TIP]
> Als u een Android-Emulator op een Windows-computer gebruikt, wordt de kaart mogelijk niet weer gegeven vanwege conflicten met OpenGL-en software matige grafische rendering. Het volgende heeft voor sommige personen gewerkt, om dit probleem op te lossen. Open AVD Manager en selecteer het virtuele apparaat dat u wilt bewerken. Schuif omlaag in het deel venster **Configuratie controleren** . Stel in het gedeelte **geëmuleerde prestaties** de **grafische** optie in op **Hardware**.

## <a name="localizing-the-map"></a>De kaart lokaliseren

Lokalisatie is belang rijk als uw doel groep is verspreid over meerdere landen of andere talen spreekt.

**Voor: Google Maps**

Voeg de volgende code toe aan de methode `onCreate` om de taal van de kaart in te stellen. De code moet worden toegevoegd voordat u de context weergave van de kaart instelt. De taal code FR beperkt de taal tot het Frans.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Hier volgt een voor beeld van Google Maps waarbij de taal is ingesteld op "FR".

<center>

lokalisatie van Google Maps ![](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Na: Azure Maps**

Azure Maps biedt drie verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De eerste optie is om de taal-en regionale weergave gegevens door te geven aan de `AzureMaps` klasse. Deze optie maakt gebruik van de statische `setLanguage`-en `setView`-methoden wereld wijd. Dit betekent dat de standaard taal en de regionale weer gave worden ingesteld voor alle Azure Maps besturings elementen die in uw app worden geladen. In dit voor beeld wordt Frans gebruikt met de taal code ' fr-FR '.

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

De tweede optie is om de taal door te geven en informatie te bekijken in de XML-code voor het kaart besturings element.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

De derde optie is om de taal-en regionale kaart weergave te Program meren met de methode Maps `setStyle`. Met deze optie worden de taal en de regionale weer gave bijgewerkt wanneer de code wordt uitgevoerd.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Hier volgt een voor beeld van Azure Maps met de taal ingesteld op "fr-FR".

<center>

![Azure Maps lokalisatie](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Bekijk de volledige lijst met [ondersteunde talen](supported-languages.md).

## <a name="setting-the-map-view"></a>De kaart weergave instellen

Dynamische toewijzingen in zowel Azure Maps als Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties door de juiste methoden aan te roepen. Laten we de kaart weer geven satelliet lucht afbeelding, centreren op een locatie met coördinaten en wijzig het zoom niveau. Voor dit voor beeld gebruiken we Latitude: 35,0272, lengte graad:-111,0225 en zoom niveau 15.

**Voor: Google Maps**

De camera van het kaart besturings element Google Maps kan via een programma worden verplaatst met behulp van de `moveCamera` methode. Met de `moveCamera` methode kunt u het middel punt van de kaart en een zoom niveau opgeven. Met de methode `setMapType` wordt het type kaart gewijzigd dat wordt weer gegeven.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps set-weer gave](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Google Maps maakt gebruik van tegels van 256 pixels in dimensies terwijl Azure Maps een grotere 512 pixels-tegel gebruikt. Dit beperkt het aantal netwerk aanvragen dat nodig is voor Azure Maps om hetzelfde kaart gebied als Google Maps te laden. Als u hetzelfde zicht bare gebied als een kaart in Google Maps wilt gebruiken, moet u het zoom niveau dat in Google Maps wordt gebruikt, aftrekken wanneer u Azure Maps gebruikt. 

**Na: Azure Maps**

Zoals eerder is vermeld, kunt u hetzelfde zicht bare gebied in Azure Maps het zoom niveau dat in Google Maps wordt gebruikt, aftrekken met één. In dit geval gebruikt u een zoom niveau van 14.

De eerste kaart weergave kan worden ingesteld in XML-kenmerken op het kaart besturings element.

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

De kaart weergave kan worden geprogrammeerd met de methoden `setCamera` en `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

weer gave ![Azure Maps instellen](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Aanvullende bronnen:**

- [Ondersteunde kaart stijlen](supported-map-styles.md)

## <a name="adding-a-marker"></a>Een markering toevoegen

Punt gegevens worden vaak weer gegeven met behulp van een afbeelding op de kaart. Deze installatie kopieën worden aangeduid als markeringen, markerings punten, pincodes of symbolen. De volgende voor beelden genereren punt gegevens als markeringen op de kaart met de breedte graad: 51,5, lengte graad:-0,2.

**Voor: Google Maps**

Met Google Maps worden markeringen toegevoegd met behulp van de Maps `addMarker` methode.

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

In Azure Maps rendert u punt gegevens op de kaart door de gegevens eerst toe te voegen aan een gegevens bron. Vervolgens maakt u de verbinding tussen die gegevens bron en een symbool laag. De gegevens bron optimaliseert het beheer van ruimtelijke gegevens in het kaart besturings element. Met de laag symbool geeft u op hoe punt gegevens moeten worden weer gegeven als een afbeelding of tekst.

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

![Azure Maps markering](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

Aangepaste installatie kopieën kunnen worden gebruikt om punten op een kaart weer te geven. De map in de onderstaande voor beelden maakt gebruik van een aangepaste afbeelding om een punt op de kaart weer te geven. Het punt bevindt zich op breedte graad: 51,5 en lengte graad:-0,2. Het anker verschuift de positie van de markering, zodat het punt van het pictogram punaise wordt uitgelijnd met de juiste positie op de kaart.

<center>

afbeelding van gele punaise ![](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_punaise. png</center>

In beide voor beelden wordt de bovenstaande afbeelding toegevoegd aan de map drawable van de app-resources.

**Voor: Google Maps**

Met Google Maps kunnen aangepaste installatie kopieën worden gebruikt voor markeringen. Laad aangepaste installatie kopieën met de optie `icon` van de markering. Als u het punt van de afbeelding wilt uitlijnen op de coördinaat, gebruikt u de optie `anchor`. Het anker is relatief ten opzichte van de afmetingen van de afbeelding. In dit geval is het anker 0,2 eenheden breed en 1 eenheid hoog.

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

aangepaste markering voor ![Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Na: Azure Maps**

Symbool lagen in Azure Maps aangepaste installatie kopieën ondersteunen, maar eerst moet de afbeelding worden geladen naar de kaart bronnen en een unieke ID worden toegewezen. Vervolgens moet de Symbol-laag verwijzen naar deze ID. Verschuif het symbool om af te stemmen op het juiste punt op de afbeelding met behulp van de `iconOffset` optie. De pictogram verschuiving is in pixels. Standaard is de offset relatief ten opzichte van het midden van de afbeelding, maar deze offset waarde kan worden aangepast met de optie `iconAnchor`. In dit voor beeld wordt de `iconAnchor`-optie ingesteld op `"center"`. Er wordt een pictogram verschuiving gebruikt om de afbeelding vijf pixels naar rechts te verplaatsen en 15 pixels omhoog om uit te lijnen met het punt van de punaise afbeelding.

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

![Azure Maps aangepaste markering](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Een poly lijn toevoegen

Polylines worden gebruikt om een lijn of pad op de kaart weer te geven. In de volgende voor beelden ziet u hoe u een stippel lijn op de kaart maakt.

**Voor: Google Maps**

Met Google Maps rendert u een poly lijn met behulp van de klasse `PolylineOptions`. Voeg de poly lijn toe aan de kaart met behulp van de `addPolyline` methode. Stel de lijn kleur in met behulp van de optie `color`. Stel de lijn dikte in met behulp van de optie `width`. Voeg een streek streepje-matrix met de optie `pattern` toe.

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

![Google Maps-poly lijn](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Na: Azure Maps**

In Azure Maps worden polylinen `LineString` of `MultiLineString` objecten genoemd. Deze objecten toevoegen aan een gegevens bron en weer geven met behulp van een laag. Stel de lijn dikte in met behulp van de optie `strokeWidth`. Voeg een streek streepje-matrix met de optie `strokeDashArray` toe.

De streek breedte en de streepjes matrix pixel eenheden in de Azure Maps Web-SDK zijn hetzelfde als in de Google Maps-service. Beide accepteren dezelfde waarden om dezelfde resultaten te produceren.

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

![Azure Maps poly lijn](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Veelhoeken worden gebruikt om een gebied op de kaart weer te geven. In de volgende voor beelden ziet u hoe u een veelhoek maakt. Deze veelhoek vormt een drie hoek op basis van de middelste coördinaat van de kaart.

**Voor: Google Maps**

Met Google Maps kunt u een veelhoek renderen met behulp van de klasse `PolygonOptions`. Voeg de veelhoek aan de kaart toe met behulp van de `addPolygon` methode. Stel de vulling-en lijn kleuren in met behulp van de opties `fillColor` en `strokeColor`. Stel de lijn dikte in met behulp van de optie `strokeWidth`.

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

![Google Maps-polygoon](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Na: Azure Maps**

Voeg in Azure Maps `Polygon` en `MultiPolygon` objecten toe aan een gegevens bron en Genereer ze op de kaart met behulp van lagen. Het gebied van een veelhoek in een polygoon laag weer geven. De omtrek van een veelhoek weer geven met behulp van een line-laag. Stel de kleur en breedte van de lijn in met behulp van de opties `strokeColor` en `strokeWidth`.

De eenheden lijn breedte en streepjes matrix ' pixel ' in Azure Maps Web SDK worden uitgelijnd met de betreffende eenheden in Google Maps. Beide accepteren dezelfde waarden en produceren dezelfde resultaten.

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

![Azure Maps veelhoek](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Een tegel laag bedekken

 Gebruik tegel lagen om laag afbeeldingen te bedekken die zijn opgesplitst in kleinere naast elkaar geplaatste afbeeldingen, die worden uitgelijnd met het kaarten tegel systeem. Deze benadering is een veelgebruikte manier voor het bedekken van laag afbeeldingen of grote gegevens sets. Tegel lagen worden afbeeldings-overlays genoemd in Google Maps.

De volgende voor beelden bedekken een weer gave laag van Iowa Environment Mesonet van Iowa State University. De tegels zijn 256 pixels groot.

**Voor: Google Maps**

Met Google Maps kan een tegel laag boven op de kaart worden overlapt. Gebruik de klasse `TileOverlayOptions`. Voeg de laag tegel toe aan de kaart met behulp van de `addTileLauer` methode. Als u de tegels semi-transparant wilt maken, is de optie `transparency` ingesteld op 0,2 of 20% transparant.

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

![Google Maps-tegel laag](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Na: Azure Maps**

Een tegel laag kan op een vergelijk bare manier worden toegevoegd aan de kaart als elke andere laag. Een opgemaakte URL met de tijdelijke aanduidingen x, y en zoomen; `{x}`, `{y}``{z}` respectievelijk wordt gebruikt om de laag voor toegang tot de tegels te geven. Daarnaast bieden tegel lagen in Azure Maps `{quadkey}`, `{bbox-epsg-3857}`en `{subdomain}` tijdelijke aanduidingen. Om de tegel laag semi-transparant te maken, wordt een dekkings waarde van 0,8 gebruikt. Dekking en transparantie, hoewel ze vergelijkbaar zijn, gebruikt u omgekeerde waarden. Als u beide opties wilt converteren, trekt u de waarde van het getal één af.

> [!TIP]
> In Azure Maps is het handig om lagen onder andere lagen weer te geven, waaronder basis kaart lagen. Daarnaast is het vaak wenselijk om tegel lagen onder de kaart labels weer te geven, zodat ze gemakkelijk te lezen zijn. De `map.layers.add`-methode heeft een tweede para meter die de id is van de laag waarin de nieuwe laag moet worden ingevoegd. Als u een tegel laag wilt invoegen onder de kaart labels, kunt u de volgende code gebruiken: `map.layers.add(myTileLayer, "labels");`

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

![Azure Maps-laag van de tegel](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Verkeer weergeven

Zowel Azure Maps als Google Maps bevatten opties voor het overlayen van verkeers gegevens.

**Voor: Google Maps**

Met Google Maps kunnen gegevens over de verkeers stroom worden weer gegeven boven op de kaart door True door te geven aan de `setTrafficEnabled` methode van de kaart.

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

Azure Maps biedt verschillende opties voor het weer geven van verkeer. Verkeers incidenten, zoals het sluiten van wegsluitingen en ongel ukken, kunnen als pictogrammen op de kaart worden weer gegeven. De netwerk stroom en de kleurgecodeerde wegen kunnen worden overlapt op de kaart. De kleuren kunnen worden gewijzigd om te worden weer gegeven ten opzichte van de limiet voor de geboekte snelheid, ten opzichte van de normale verwachte vertraging of de absolute vertraging. Incident gegevens in Azure Maps worden elke minuut bijgewerkt en stroom gegevens worden elke twee minuten bijgewerkt.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps verkeer](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Maps Android SDK.

> [!div class="nextstepaction"]
> [Het besturings element Android-kaart gebruiken](how-to-use-android-map-control-library.md)
