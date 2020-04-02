---
title: Aan de slag met Android-kaartbesturingselement | Microsoft Azure Maps
description: In dit artikel leert u hoe u aan de slag met het Android-kaartbesturingselement met behulp van de Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548542"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Aan de slag met Azure Maps Android SDK

De Azure Maps Android SDK is een vectorkaartbibliotheek voor Android. In dit artikel wordt u door de processen van het installeren van de Azure Maps Android SDK en het laden van een kaart geloodst.

## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Als u de procedures in dit artikel wilt voltooien, moet u eerst [een Azure Maps-account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) in de prijscategorie S1 en de primaire sleutel voor uw account [krijgen.](quick-demo-map-app.md#get-the-primary-key-for-your-account)

Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

### <a name="download-android-studio"></a>Android Studio downloaden

Download Android Studio en maak een project met een lege activiteit voordat u de Azure Maps Android SDK installeert. Je [Android Studio](https://developer.android.com/studio/) gratis downloaden van Google. 

## <a name="create-a-project-in-android-studio"></a>Een project maken in Android Studio

Maak eerst een nieuw project met een lege activiteit. Voer deze stappen uit om een Android Studio-project te maken:

1. Selecteer telefoon en **tablet**onder **Uw project kiezen**. Uw aanvraag wordt uitgevoerd op deze form factor.
2. Selecteer op het tabblad **Telefoon en tablet** De optie Activiteit **leeg**maken en selecteer **Volgende**.
3. Selecteer **Configure your project** `API 21: Android 5.0.0 (Lollipop)` onder Uw project configureren als de minimale SDK. Dit is de vroegste versie die wordt ondersteund door de Azure Maps Android SDK.
4. Accepteer de `Activity Name` `Layout Name` standaardinstelling en selecteer **Voltooien**.

Bekijk de [Android Studio-documentatie](https://developer.android.com/studio/intro/) voor meer hulp bij het installeren van Android Studio en het maken van een nieuw project.

![Een project maken in Android-studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Een virtueel apparaat instellen

Met Android Studio u een virtueel Android-apparaat instellen op uw computer. Hierdoor u uw toepassing testen tijdens de ontwikkeling. Als u een virtueel apparaat wilt instellen, selecteert u het Pictogram AVD-beheer (Android Virtual Device) in de rechterbovenhoek van uw projectscherm en selecteert u **Virtueel apparaat maken**. U ook naar De AVD-manager gaan door **Hulpmiddelen** > **Android** > **AVD Manager** op de werkbalk te selecteren. Selecteer **Nexus** **5X**in de categorie Telefoons en selecteer **Volgende**.

Meer informatie over het instellen van een AVD vind je in de [Android Studio-documentatie.](https://developer.android.com/studio/run/managing-avds)

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>De Azure Maps Android SDK installeren

De volgende stap bij het bouwen van uw toepassing is het installeren van de Azure Maps Android SDK. Voer de volgende stappen uit om de SDK te installeren:

1. Open het **build.gradle-bestand op het** hoogste niveau en voeg de volgende code toe aan de sectie **alle projecten**, **repositories** block:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Werk uw **app/build.gradle bij** en voeg er de volgende code aan toe:
    
    1. Zorg ervoor dat de **minSdkVersion** van uw project op API 21 of hoger staat.

    2. Voeg de volgende code toe aan de Android-sectie:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Werk uw afhankelijkheden blokkeren en voeg een nieuwe afhankelijkheidslijn voor implementatie toe voor de nieuwste Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Ga naar **Bestand** op de werkbalk en klik op **Project synchroniseren met Gradle-bestanden**.
3. Voeg een kaartfragment toe aan \> de \> \_hoofdactiviteit (res layout activity main.xml):
    
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
    
    * import toevoegen voor de Azure Maps SDK
    * uw Azure Maps-verificatiegegevens instellen
    * de instantie kaartbesturing in de **methode onCreate** krijgen

    Als u de `AzureMaps` verificatiegegevens in `setSubscriptionKey` `setAadProperties` de klasse globaal instelt met behulp van de of methoden, hoeft u uw verificatiegegevens niet in elke weergave toe te voegen. 

    Het kaartbesturingselement bevat zijn eigen levenscyclusmethoden voor het beheren van de OpenGL-levenscyclus van Android. Deze levenscyclusmethoden moeten rechtstreeks worden aangeroepen vanuit de activiteit die deze bevat. Als u de levenscyclusmethoden van het kaartbesturingselement correct wilt aanroepen, moet u de volgende levenscyclusmethoden in de activiteit die het kaartbesturingselement bevat, overschrijven. En u moet de desbetreffende kaartcontrolemethode aanroepen. 

    * onCreate(Bundel) 
    * onStart() 
    * onHervat() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundel) 
    * onLowMemory() 

    Bewerk het **mainactivity.java-bestand** als volgt:
    
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

## <a name="import-classes"></a>Importklassen

Nadat je de vorige stappen hebt voltooid, krijg je waarschijnlijk waarschuwingen van Android Studio over een deel van de code. Als u deze waarschuwingen wilt oplossen, `MainActivity.java`importeert u de klassen waarnaar wordt verwezen in .

U deze klassen automatisch importeren door Alt+Enter (Option+Return op een Mac) te selecteren.

Selecteer de run-knop, zoals weergegeven in de volgende afbeelding (of druk op Control+R op een Mac) om uw toepassing te bouwen.

![Klik op Uitvoeren](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio zal een paar seconden duren om de applicatie te bouwen. Nadat de build is voltooid, u uw toepassing testen in het geëmuleerde Android-apparaat. Je zou een kaart als deze moeten zien:

<center>

![Azure Maps in Android-toepassing](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>De kaart lokaliseren

De Azure Maps Android SDK biedt drie verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De volgende code geeft aan hoe u de taal instelt op Frans ("fr-FR") en de regionale weergave op "auto". 

De eerste optie is om de taal `AzureMaps` door te `setLanguage` geven `setView` en regionale informatie in de klasse te bekijken met behulp van de statische en methoden wereldwijd. Hiermee wordt de standaardtaal- en regionale weergave ingesteld voor alle Azure Maps-besturingselementen die in uw app zijn geladen.

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

De tweede optie is om de taal door te geven en informatie weer te geven aan xml met kaartbesturingselement.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

De derde optie is om programmatisch de taal en de `setStyle` regionale weergave van de kaart in te stellen met behulp van de kaartmethode. Dit kan op elk moment worden gedaan om de taal en de regionale weergave van de kaart te wijzigen.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Hier is een voorbeeld van Azure Maps met de taal ingesteld op "fr-FR" en regionale weergave ingesteld op "auto".

<center>

![Azure Maps, kaartafbeelding met labels in het Frans](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Een volledige lijst van ondersteunde talen en regionale weergaven wordt [hier](supported-languages.md)gedocumenteerd.

## <a name="navigating-the-map"></a>Navigeren op de kaart

Er zijn verschillende manieren waarop de kaart kan worden ingezoomd, gepand, gedraaid en geworpen. De volgende details alle verschillende manieren om de kaart te navigeren.

**De kaart inzoomen**

- Raak de kaart met twee vingers aan en knijp samen om uit te zoomen of de vingers uit elkaar te spreiden om in te zoomen.
- Dubbeltik op de kaart om één niveau in te zoomen.
- Dubbeltik met twee vingers om de kaart één niveau uit te zoomen.
- Tik twee keer; houd bij de tweede tik uw vinger op de kaart en sleep omhoog om in te zoomen of omlaag om uit te zoomen.

**De kaart pannen**

- Raak de kaart aan en sleep in elke richting.

**De kaart roteren**

- Raak de kaart met twee vingers aan en draai.

**Pitch de kaart**

- Raak de kaart met twee vingers aan en sleep ze samen omhoog of omlaag.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van overlaygegevens op de kaart:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen aan een Android-kaart](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Shapes toevoegen aan een Android-kaart](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Kaartstijlen wijzigen in Android-kaarten](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
