---
title: Aan de slag met het besturings element Android map | Microsoft Azure kaarten
description: In dit artikel leert u hoe u aan de slag gaat met het besturings element Android map met behulp van de Microsoft Azure Mapss Android SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ca4051e56f7d51ac7ac5fbe534cb8fc536d64a68
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368682"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Aan de slag met Azure Maps Android SDK

De Azure Maps Android SDK is een vector map-Bibliotheek voor Android. In dit artikel vindt u instructies voor het installeren van de Azure Maps Android SDK en het laden van een kaart.

## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Als u de procedures in dit artikel wilt uitvoeren, moet u eerst [een Azure Maps-account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) in de prijs categorie S1 en de [primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) voor uw account ophalen.

Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

### <a name="download-android-studio"></a>Android Studio downloaden

Down load Android Studio en maak een project met een lege activiteit voordat u de Azure Maps Android SDK installeert. U kunt [Android Studio gratis downloaden](https://developer.android.com/studio/) van Google. 

## <a name="create-a-project-in-android-studio"></a>Een project maken in Android Studio

Maak eerst een nieuw project met een lege activiteit. Voer de volgende stappen uit om een Android Studio project te maken:

1. Onder **Kies uw project**selecteert u **telefoon en Tablet**. Uw toepassing wordt uitgevoerd op deze vorm factor.
2. Op het tabblad **telefoon en Tablet** selecteert u **lege activiteit**en selecteert u **volgende**.
3. Onder **uw project configureren**selecteert u `API 21: Android 5.0.0 (Lollipop)` als de minimale SDK. Dit is de oudste versie die wordt ondersteund door de Azure Maps Android SDK.
4. Accepteer de standaard `Activity Name` en `Layout Name` en selecteer **volt ooien**.

Raadpleeg de [Android Studio-documentatie](https://developer.android.com/studio/intro/) voor meer informatie over het installeren van Android Studio en het maken van een nieuw project.

![Een project maken in Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Een virtueel apparaat instellen

Met Android Studio kunt u een virtueel Android-apparaat op uw computer instellen. Dit kan u helpen bij het testen van uw toepassing tijdens de ontwikkeling. Als u een virtueel apparaat wilt instellen, selecteert u het pictogram Android Virtual Device (AVD) Manager in de rechter bovenhoek van het venster van het project en selecteert u **virtueel apparaat maken**. U kunt ook naar de AVD Manager gaan door **extra** > **Android** > **AVD Manager** te selecteren op de werk balk. In de categorie **telefoons** selecteert u **Nexus 5x**en selecteert u vervolgens **volgende**.

Meer informatie over het instellen van een AVD vindt u in de [documentatie van Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android-Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>De Azure Maps Android SDK installeren

De volgende stap bij het bouwen van uw toepassing is het installeren van de Azure Maps Android SDK. Voer de volgende stappen uit om de SDK te installeren:

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
    * Uw Azure Maps-verificatie-informatie instellen
    * Het kaart besturings exemplaar ophalen in de methode **onCreate**

    Als u de verificatie gegevens voor de klasse `AzureMaps` globaal instelt met behulp van de `setSubscriptionKey`-of `setAadProperties`-methoden, is het niet nodig om uw verificatie gegevens toe te voegen aan elke weer gave. 

    Het kaart besturings element bevat eigen levenscyclus methoden voor het beheren van de OpenGL-levens duur van Android. Deze levenscyclus methoden moeten rechtstreeks vanuit de insluitende activiteit worden aangeroepen. Als u wilt dat uw app de levenscyclus methoden van het kaart besturings element correct aanroept, moet u de volgende levenscyclus methoden overschrijven in de activiteit die het kaart besturings element bevat. En u moet de bijbehorende kaart beheer methode aanroepen. 

    * onCreate (bundel) 
    * onstart () 
    * onResume() 
    * onPause () 
    * onStop () 
    * onDestroy() 
    * onSaveInstanceState (bundel) 
    * onLowMemory() 

    Bewerk het bestand **MainActivity. java** als volgt:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Klassen importeren

Nadat u de voor gaande stappen hebt voltooid, krijgt u waarschijnlijk waarschuwingen van Android Studio over een deel van de code. Als u deze waarschuwingen wilt oplossen, importeert u de klassen waarnaar wordt verwezen in `MainActivity.java`.

U kunt deze klassen automatisch importeren door op ALT + ENTER te drukken (optie + retour neren op een Mac).

Selecteer de knop uitvoeren, zoals wordt weer gegeven in de volgende afbeelding (of druk op Control + R op een Mac) om uw toepassing te bouwen.

![Klik op Uitvoeren](./media/how-to-use-android-map-control-library/run-app.png)

Het duurt een paar seconden Android Studio om de toepassing te bouwen. Nadat de build is voltooid, kunt u de toepassing testen in het geëmuleerde Android-apparaat. U ziet een kaart zoals deze als volgt:

<center>

![Azure Maps in Android-toepassing](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>De kaart lokaliseren

De Azure Maps Android SDK biedt drie verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De volgende code laat zien hoe u de taal instelt op Frans (fr-FR) en de regionale weer gave op ' auto '. 

De eerste optie is de taal door geven en regionale informatie weer geven in de `AzureMaps` klasse met behulp van de statische `setLanguage` en `setView` methoden wereld wijd. Hiermee stelt u de standaard taal en de regionale weer gave in voor alle Azure Maps besturings elementen die in uw app worden geladen.

```Java
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

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

U kunt de derde optie gebruiken om de taal en de regionale weer gave van de kaart programmatisch in te stellen met behulp van de Maps `setStyle` methode. Dit kan op elk gewenst moment worden gedaan om de taal en de regionale weer gave van de kaart te wijzigen.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Hier volgt een voor beeld van Azure Maps waarbij de taal is ingesteld op ' fr-FR ' en de regionale weer gave ingesteld op ' auto '.

<center>

![Azure Maps, kaart afbeelding met labels in de Franse](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van overlay-gegevens op de kaart:

> [!div class="nextstepaction"]
> [Een symbool laag aan een Android-kaart toevoegen](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Vormen toevoegen aan een Android-kaart](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Kaart stijlen wijzigen in Android-kaarten](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
