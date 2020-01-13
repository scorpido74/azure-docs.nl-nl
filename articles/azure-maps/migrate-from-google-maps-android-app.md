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
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909192"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Een Android-app migreren vanuit Google Maps

De Azure Maps Android SDK bevat een API-interface die vergelijkbaar is met de Web-SDK. Als u met een van deze Sdk's hebt ontwikkeld, zijn veel van dezelfde concepten, best practices en architecturen van toepassing en moet u uw kennis gemakkelijk van de ene naar de andere kunnen overdragen.

De Azure Maps Android SDK ondersteunt een minimale Android-versie van API 21: Android 5.0.0 (Lollipop).

Alle voor beelden in Java, maar Kotlin kunnen ook worden gebruikt met de Azure Maps Android SDK.

Zie ook de [hand leidingen voor de Azure Maps ANDROID SDK](how-to-use-android-map-control-library.md) voor meer informatie over het ontwikkelen met deze SDK.

## <a name="load-a-map"></a>Een kaart laden

Het laden van een kaart in een Android-app met Google of Azure Maps bestaat uit veel dezelfde stappen. Wanneer u een van beide SDK gebruikt, moet u:

- Down load een API of abonnements sleutel voor toegang tot een van beide platforms.
- Voeg wat XML toe aan een activiteit om op te geven waar de kaart moet worden gerenderd en hoe deze moet worden ingedeeld.
- Alle levenscyclus methoden van de activiteit met de kaart weergave door sturen naar de corresponderende procedures in de klasse map. U moet in het bijzonder de volgende methoden door sturen:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Wacht totdat de kaart gereed is voordat u deze via een programma probeert te openen.

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

1.  In het bestand **MainActivity. java** moet u import bewerkingen toevoegen voor de SDK van Google Maps. Alle levenscyclus methoden van de activiteit met de kaart weergave door sturen naar de corresponderende procedures in de klasse map. Een `MapView`-exemplaar kan worden opgehaald uit het kaart fragment met behulp van de methode `getMapAsync(OnMapReadyCallback)`. De `MapView` initialiseert automatisch het kaarten systeem en de weer gave. Bewerk het bestand **MainActivity. java** als volgt:

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

Wanneer een toepassing wordt uitgevoerd, wordt het kaart besturings element als volgt geladen.

<center>

![eenvoudige Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Na: Azure Maps**

Als u een kaart wilt weer geven met behulp van de Azure Maps SDK voor Android, moeten de volgende stappen worden uitgevoerd:

1. Open het bestand **Build. gradle** op het hoogste niveau en voeg de volgende code toe aan de sectie **alle projecten**, **opslag** plaatsen blok:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Werk uw **app/build. gradle** bij en voeg de volgende code toe:
    
    1. Zorg ervoor dat de **minSdkVersion** van uw project op API 21 of hoger is.

    2. Voeg de volgende code toe aan de sectie Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Werk het blok met afhankelijkheden bij en voeg een nieuwe implementatie afhankelijkheids regel toe voor de nieuwste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > De Azure Maps Android SDK wordt regel matig bijgewerkt en uitgebreid. U kunt de documentatie aan de [slag met Android-beheer](how-to-use-android-map-control-library.md) weer geven voor het meest recente versie nummer van Azure Maps-implementatie. Daarnaast kunt u het versie nummer van ' 0,2 ' instellen op ' 0 + ' zodat het altijd naar de meest recente versie verwijst.
    
    4. Ga naar het **bestand** in de werk balk en klik vervolgens op **project synchroniseren met Gradle-bestanden**.
3. Voeg een kaart fragment toe aan de hoofd activiteit (res \>-indeling \> activiteit\_Main. XML):
    
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
    
    * import bewerkingen toevoegen voor de Azure Maps SDK
    * uw Azure Maps-verificatie-informatie instellen
    * het kaart besturings exemplaar ophalen in de methode **onCreate**

    Als u de verificatie gegevens voor de klasse `AzureMaps` globaal instelt met behulp van de `setSubscriptionKey`-of `setAadProperties`-methoden, is het niet nodig om uw verificatie gegevens toe te voegen aan elke weer gave. 

    Het kaart besturings element bevat eigen levenscyclus methoden voor het beheren van de OpenGL-levens duur van Android, die rechtstreeks vanuit de insluitende activiteit moet worden aangeroepen. Als uw app correct is, roept u de levenscyclus methoden van het kaart besturings element aan. u moet de volgende levenscyclus methoden in de activiteit die het kaart besturings element bevat, overschrijven en de desbetreffende kaart beheer methode aanroepen. 

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

Als u uw toepassing uitvoert, wordt het kaart besturings element als volgt geladen.

<center>

![eenvoudige Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

U ziet dat het Azure Maps besturings element ondersteunt meer uitzoomen en biedt meer een wereld wijde weer gave.

> [!TIP]
> Als u een Android-Emulator in Windows gebruikt, wordt de kaart mogelijk niet gerenderd als gevolg van conflicten met OpenGL en geversnellene grafische weer gave van software. Dit probleem kan worden opgelost met een van de volgende oplossingen. Open AVD Manager en selecteer het virtuele apparaat dat u wilt bewerken. Stel in het gedeelte **geëmuleerde prestaties** de **grafische** optie in op **Hardware**.

## <a name="localizing-the-map"></a>De kaart lokaliseren

Als uw doel groep is verspreid over meerdere landen of andere talen spreekt, is lokalisatie belang rijk.

**Voor: Google Maps**

De taal van de kaart kan worden ingesteld in de `onCreate` methode van de hoofd activiteit door de volgende code toe te voegen voordat u de context weergave van de kaart instelt. Hiermee wordt de taal beperkt tot het Frans met behulp van de taal code fr.

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

Azure Maps biedt drie verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De eerste optie is om de taal-en regionale weergave gegevens door te geven aan de klasse `AzureMaps` met behulp van de statische `setLanguage` en `setView` methoden wereld wijd. Hiermee stelt u de standaard taal en de regionale weer gave in voor alle Azure Maps-besturings elementen die in uw app worden geladen. Hieronder wordt de taal beperkt tot Frans met de taal code fr-FR.

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

De tweede optie is de taal door geven en informatie weer geven in de XML van het kaart besturings element.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

U kunt de derde optie gebruiken om de taal en de regionale weer gave van de kaart programmatisch in te stellen met behulp van de Maps `setStyle` methode. Dit kan op elk gewenst moment worden gedaan om de taal en de regionale weer gave van de kaart te wijzigen.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Hier volgt een voor beeld van Azure Maps met de taal ingesteld op "fr-FR".

<center>

![Azure Maps lokalisatie](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="setting-the-map-view"></a>De kaart weergave instellen

Dynamische kaarten in Azure en Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties door de juiste methoden aan te roepen. In de onderstaande voor beelden ziet u hoe u de kaart kunt weer geven satelliet lucht afbeelding, centreert u de kaart over een locatie met coördinaten (breedte graad: 35,0272, lengte graad:-111,0225) en wijzigt u het zoom niveau in 15 in Google Maps.

> [!NOTE]
> Google Maps maakt gebruik van tegels van 256 pixels in dimensies terwijl Azure Maps een grotere 512 pixels-tegel gebruikt. Dit beperkt het aantal netwerk aanvragen dat nodig is voor Azure Maps om hetzelfde kaart gebied als Google Maps te laden. Als gevolg van de manier waarop tegel piramides werken in kaart besturings elementen, de grotere tegels in Azure Maps betekent dat u hetzelfde zicht bare gebied als een kaart in Google Maps wilt gebruiken, moet u het zoom niveau dat in Google Maps wordt gebruikt, aftrekken door 1 bij het gebruik van Azure Maps.

**Voor: Google Maps**

De camera van het kaart besturings element Google Maps kan via een programma worden verplaatst met behulp van de methode `moveCamera`, waarmee u het midden van de kaart en een zoom niveau kunt opgeven. De methode `setMapType` kan worden gebruikt om het type kaart te wijzigen dat wordt weer gegeven.

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

**Na: Azure Maps**

Zoals eerder is vermeld, kunt u het zoom niveau dat in Google Maps wordt gebruikt, aftrekken om hetzelfde zicht bare gebied in Azure Maps te halen, in dit geval een zoom niveau van 14 te gebruiken.

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

De kaart weergave kan via een programma worden bijgewerkt met de methoden `setCamera` en `setStyle`.

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

Punt gegevens worden vaak op de kaart weer gegeven met behulp van een afbeelding op de kaart. Deze installatie kopieën worden vaak markeringen, markerings punten, pincodes of symbolen genoemd. De volgende voor beelden genereren punt gegevens als markeringen op de kaart op (Latitude: 51,5, lengte graad:-0,2).

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

In Azure Maps punt gegevens kunnen worden weer gegeven op de kaart door de gegevens eerst toe te voegen aan een gegevens bron en deze gegevens bron vervolgens te verbinden met een symbool laag. De gegevens bron optimaliseert het beheer van ruimtelijke gegevens in het kaart besturings element en de laag van het symbool geeft aan hoe punt gegevens moeten worden weer gegeven als een afbeelding en/of tekst.

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

Aangepaste installatie kopieën kunnen worden gebruikt om punten op een kaart weer te geven. De volgende afbeelding wordt gebruikt in de onderstaande voor beelden: gebruik een aangepaste afbeelding om een punt op de kaart weer te geven op (Latitude: 51,5, lengte graad:-0,2) en verschuift de positie van de markering zodat het punt van het punaise pictogram wordt uitgelijnd met de juiste positie op de kaart.

<center>

afbeelding van gele punaise ![](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_punaise. png</center>

In beide voor beelden wordt de bovenstaande afbeelding toegevoegd aan de map drawable van de app-resources.

**Voor: Google Maps**

Met Google Maps kunnen aangepaste installatie kopieën worden gebruikt voor markeringen door ze te laden via de `icon` optie van de markering. Als u het punt van de afbeelding wilt uitlijnen op de coördinaat, kan de optie `anchor` worden gebruikt. Het anker is relatief ten opzichte van de afmetingen van de afbeelding, in dit geval 0,2 eenheden breed en 1 eenheid hoog.

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

Symbool lagen in Azure Maps ook aangepaste installatie kopieën ondersteunen, maar de installatie kopie moet eerst worden geladen in de kaart bronnen en een unieke ID hebben toegewezen. De Symbol-laag kan vervolgens naar deze ID verwijzen. Het symbool kan worden afgestemd op het juiste punt op de installatie kopie met behulp van de `iconOffset` optie. Houd er rekening mee dat de pictogram verschuiving in pixels is. Standaard is de offset relatief ten opzichte van het midden van de afbeelding, maar dit kan worden aangepast met behulp van de `iconAnchor` optie. In dit voor beeld wordt de `iconAnchor` optie ingesteld op `"center"` en wordt een pictogram verschuiving gebruikt om de afbeelding vijf pixels naar rechts te verplaatsen en 15 pixels omhoog om af te stemmen met het punt van de punaise afbeelding.

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

Met Google Maps kan een poly lijn worden gemaakt met behulp van de klasse `PolylineOptions` en met behulp van de methode `addPolyline` aan de kaart worden toegevoegd. De lijn kleur kan worden ingesteld met behulp van de `color` optie, de lijn breedte wordt ingesteld met de optie breedte en een streek streepje-matrix kan worden ingesteld met behulp van de `pattern` optie.

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

In Azure Maps worden polylinen Lines Tring of multi line String-objecten genoemd. Deze objecten kunnen worden toegevoegd aan een gegevens bron en worden weer gegeven met behulp van een laag. Houd er rekening mee dat de eenheden lijn breedte en streepjes matrix ' pixel ' worden uitgelijnd met de Azure Maps Web-SDK in die dezelfde waarden gebruiken in beide Sdk's die dezelfde resultaten opleveren.

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

Veelhoeken worden gebruikt om een gebied op de kaart weer te geven. In de volgende voor beelden ziet u hoe u een veelhoek maakt die een drie hoek vormt op basis van de middelste coördinaat van de kaart.

**Voor: Google Maps**

Met Google Maps kan een veelhoek worden gemaakt met behulp van de klasse `PolygonOptions` en met behulp van de methode `addPolygon` aan de kaart worden toegevoegd. De vulling-en lijn kleuren kunnen worden ingesteld met behulp van de optie `fillColor` en `strokeColor`, de breedte van de lijn wordt ingesteld met de optie `strokeWidth`.

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

In Azure Maps kunnen veelhoek-en multipolygoon objecten worden toegevoegd aan een gegevens bron en met behulp van lagen worden weer gegeven op de kaart. Het gebied van een veelhoek kan worden weer gegeven in een polygoon laag. Het overzicht van een veelhoek kan worden gerenderd met behulp van een line-laag. Houd er rekening mee dat de eenheden lijn breedte en streepjes matrix ' pixel ' worden uitgelijnd met de Azure Maps Web-SDK in die dezelfde waarden gebruiken in beide Sdk's die dezelfde resultaten opleveren.

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

Tegel lagen, ook wel afbeeldings-overlays genoemd in Google Maps, bieden u de mogelijkheid om laag afbeeldingen te overlayen die zijn opgesplitst in kleinere tegel afbeeldingen die zijn afgestemd op het kaarten tegel systeem. Dit is een veelgebruikte manier om laag afbeeldingen of zeer grote gegevens sets te bedekken.

De volgende voor beelden bedekken een weer gave laag van Iowa Environment Mesonet van Iowa State University. De tegels zijn 256 pixels groot.

**Voor: Google Maps**

Met Google Maps kan een tegel laag boven op de kaart worden geplaatst. Gebruik de `TileOverlayOptions`-klasse en voeg deze toe aan de kaart met behulp van de methode `addTileLauer`. Als u de tegels semi-transparant wilt maken, is de optie `transparency` ingesteld op 0,2 of 20% transparant.

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

In Azure Maps kan een tegel laag op ongeveer dezelfde manier als een andere laag worden toegevoegd aan de kaart. Een opgemaakte URL met de tijdelijke aanduidingen x, y en zoomen; `{x}`, `{y}``{z}` respectievelijk wordt gebruikt om de laag voor toegang tot de tegels te geven. De tegel lagen in Azure Maps ondersteunen ook `{quadkey}`, `{bbox-epsg-3857}` en `{subdomain}` tijdelijke aanduidingen. Om de tegel laag semi-transparant te maken, wordt een dekkings waarde van 0,8 gebruikt. Houd er rekening mee dat de dekking en transparantie, ondanks vergelijk bare, omgekeerde waarden gebruiken. Als u tussen deze waarden wilt omzetten, trekt u de waarde van het getal één af.

> [!TIP]
> In Azure Maps lagen kunnen eenvoudig worden weer gegeven onder andere lagen, waaronder basis kaart lagen. Vaak is het wenselijk om tegel lagen onder de kaart labels weer te geven, zodat ze gemakkelijk te lezen zijn. De `map.layers.add`-methode heeft een tweede para meter die de id is van de laag waarin de nieuwe laag moet worden ingevoegd. Als u een tegel laag wilt invoegen onder de kaart labels, kunt u de volgende code gebruiken: `map.layers.add(myTileLayer, "labels");`

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

Verkeers gegevens kunnen zowel Azure-als Google-kaarten overlappen.

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

Azure Maps biedt verschillende opties voor het weer geven van verkeer. Verkeers incidenten, zoals het sluiten van wegsluitingen en ongel ukken, kunnen als pictogrammen op de kaart worden weer gegeven. Verkeers stromen, wegen met kleur code kunnen op de kaart worden geplaatst en de kleuren kunnen worden aangepast aan de grenzen van de geposte snelheid, ten opzichte van de normale verwachte vertraging of absolute vertraging. Incident gegevens in Azure Maps worden elke minuut bijgewerkt en gegevens worden elke twee minuten gestroomd.

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
