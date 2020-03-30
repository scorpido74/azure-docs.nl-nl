---
title: Verkeersgegevens weergeven op de Android-kaart | Microsoft Azure Maps
description: In dit artikel leert u hoe u verkeersgegevens op een kaart weergeeft met de Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335377"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Verkeersgegevens op de kaart weergeven met Azure Maps Android SDK

Stroomgegevens en incidentengegevens zijn de twee soorten verkeersgegevens die op de kaart kunnen worden weergegeven. In deze handleiding ziet u hoe u beide typen verkeersgegevens weergeven. Incidenten gegevens bestaat uit punt en lijn-gebaseerde gegevens voor dingen zoals constructies, wegafsluitingen, en ongevallen. Stroomgegevens tonen statistieken over de doorstroming van het verkeer op de weg.

## <a name="prerequisites"></a>Vereisten

Voordat u verkeer op de kaart weergeven, moet u [een Azure-account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)en [een abonnementssleutel verkrijgen.](quick-demo-map-app.md#get-the-primary-key-for-your-account) Vervolgens moet u de [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) installeren en een kaart laden.

## <a name="incidents-traffic-data"></a>Verkeersgegevens incidenten 

U moet de volgende bibliotheken importeren `setTraffic` om `incidents`te bellen en:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 In het volgende codefragment ziet u hoe u verkeersgegevens op de kaart weergeven. We geven een booleaanse waarde door aan de `incidents` methode en geven dat door aan de `setTraffic` methode. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Stroomverkeersgegevens

U moet eerst de volgende bibliotheken importeren `setTraffic` `flow`om te bellen en:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Gebruik het volgende codefragment om verkeersstroomgegevens in te stellen. Net als bij de code in de vorige `flow` sectie geven `setTraffic` we de retourwaarde van de methode door aan de methode. Er zijn vier waarden die `flow`kunnen worden doorgegeven aan , en elke waarde zou leiden tot `flow` het retourneren van de respectievelijke waarde. De retourwaarde `flow` van wordt vervolgens doorgegeven als argument aan `setTraffic`. Zie de onderstaande tabel voor deze vier waarden:

| | |
| :-- | :-- |
| TrafficFlow.NONE | Geeft geen verkeersgegevens weer op de kaart |
| TrafficFlow.RELATIVE | Toont verkeersgegevens die ten opzichte van de vrije doorstromingssnelheid van de weg zijn |
| TrafficFlow.RELATIVE_DELAY | Geeft gebieden weer die langzamer zijn dan de gemiddelde verwachte vertraging |
| TrafficFlow.ABSOLUTE | Toont de absolute snelheid van alle voertuigen op de weg |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Verkeersgegevens voor incidenten weergeven door op een functie te klikken

Om de incidenten voor een specifieke functie te verkrijgen, u de onderstaande code gebruiken. Wanneer op een functie wordt geklikt, controleert de codelogica op incidenten en wordt een bericht over het incident gemaakt. Onderaan het scherm verschijnt een bericht met de details.

1. Eerst moet u res bewerken **> lay-out > activity_main.xml,** zodat het lijkt op de onderstaande. U `mapcontrol_centerLat`de `mapcontrol_centerLng`, `mapcontrol_zoom` , en met de gewenste waarden vervangen. Terugroepen, het zoomniveau is een waarde tussen 0 en 22. Bij zoomniveau 0 past de hele wereld op één tegel.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Voeg de volgende code toe aan uw **MainActivity.java-bestand.** Het pakket is standaard inbegrepen, dus zorg ervoor dat u uw pakket aan de bovenkant houdt.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Zodra u de bovenstaande code in uw toepassing hebt opgenomen, u op een functie klikken en de details van de verkeersincidenten bekijken. Afhankelijk van de breedtegraad, lengtegraad en de zoomniveauwaarden die u in uw **activity_main.xml-bestand** hebt gebruikt, ziet u resultaten die vergelijkbaar zijn met de volgende afbeelding:

   <center>

   ![Incident-verkeer-op-de-kaart](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende handleidingen om te leren hoe u meer gegevens aan uw kaart toevoegen:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Shapes toevoegen aan de Android-kaart](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Functie-informatie weergeven](display-feature-information-android.md)
