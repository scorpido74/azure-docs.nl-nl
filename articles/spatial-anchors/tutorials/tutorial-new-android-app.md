---
title: 'Zelfstudie: Een nieuwe Android-app maken'
description: In deze zelfstudie leert u hoe u een nieuwe Android-app maakt met Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1773ef81a5b727187a9a69ccc7ce7ad0421fb2c
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80246769"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Zelfstudie: stapsgewijze instructies voor het maken van een nieuwe Android-app met Azure Spatial Anchors

In deze zelfstudie ziet u hoe u een nieuwe Android-app maken die ARCore-functionaliteit integreert met Azure Spatial Anchors.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Windows- of macOS-machine met <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerde</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.

## <a name="getting-started"></a>Aan de slag

Start Android Studio. Klik **in** het venster Welkom bij Android Studio op Een nieuw Android **Studio-project starten**. Als u al een project hebt geopend, selecteert u **Bestand**->**Nieuw project**.

Kies in het venster **Nieuw project maken** onder de sectie Telefoon en **tablet** de optie **Activiteit leegmaken**en klik op **Volgende**. Kies vervolgens onder Minimaal `API 26: Android 8.0 (Oreo)` **API-niveau**en kies `Java`en zorg ervoor dat de **taal** is ingesteld op . U de projectnaam & locatie en de naam van het pakket wijzigen. Laat de andere opties zoals ze zijn. Klik op **Voltooien**. De **componentinstaller** wordt uitgevoerd. Zodra het klaar is, klikt u op **Voltooien.** Na enige verwerking opent Android Studio de IDE.

## <a name="trying-it-out"></a>Het uitproberen

Als u uw nieuwe app wilt testen, sluit u uw apparaat met een ontwikkelaar met een USB-kabel aan op uw ontwikkelingsmachine. Klik op**Uitvoeren 'app'** **uitvoeren.**-> Selecteer in het venster **Implementatiedoel** selecteren uw apparaat en klik op **OK**. Android Studio installeert de app op je verbonden apparaat en start deze. Je zou nu "Hello World!" moeten zien. weergegeven in de app die op uw apparaat wordt uitgevoerd. Klik **op Stoppen**->**stoppen 'app'** uitvoeren .

## <a name="integrating-_arcore_"></a>ArCore _ARCore_ integreren

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> is google's platform voor het bouwen van Augmented Reality-ervaringen, waardoor uw apparaat zijn positie kan volgen terwijl het beweegt en zijn eigen begrip van de echte wereld opbouwt.

Wijzigen `app\manifests\AndroidManifest.xml` om de volgende vermeldingen `<manifest>` in het hoofdknooppunt op te nemen. Dit codefragment doet een paar dingen:

- Hiermee heeft uw app toegang tot de camera van uw apparaat.
- Het zorgt er ook voor dat uw app alleen zichtbaar is in de Google Play Store op apparaten die ARCore ondersteunen.
- De Google Play Store wordt geconfigureerd om ARCore te downloaden en te installeren, als deze nog niet is geïnstalleerd wanneer uw app is geïnstalleerd.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Wijzigen `Gradle Scripts\build.gradle (Module: app)` om de volgende vermelding op te nemen. Deze code zorgt ervoor dat uw app zich richt op ARCore-versie 1.8. Na deze wijziging krijgt u mogelijk een melding van Gradle waarin u wordt gevraagd te synchroniseren: klik nu op **Synchroniseren**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Scènevorm _Sceneform_ integreren

[_Sceneform_](https://developers.google.com/sceneform/develop/) maakt het eenvoudig om realistische 3D-scènes weer te geven in Augmented Reality-apps, zonder OpenGL te hoeven leren.

Wijzigen `Gradle Scripts\build.gradle (Module: app)` om de volgende vermeldingen op te nemen. Met deze code kan uw app taalconstructies van `Sceneform` Java 8 gebruiken, wat vereist. Het zorgt er ook `Sceneform` voor dat uw app zich richt op versie 1.8, omdat deze overeenkomt met de versie van ARCore die uw app gebruikt. Na deze wijziging krijgt u mogelijk een melding van Gradle waarin u wordt gevraagd te synchroniseren: klik nu op **Synchroniseren**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Open `app\res\layout\activity_main.xml`uw element Hello Wolrd `<TextView>` en vervang het volgende ArFragment. Deze code zorgt ervoor dat de camerafeed op het scherm wordt weergegeven, zodat ARCore de positie van uw apparaat kan volgen terwijl deze beweegt.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Implementeer uw](#trying-it-out) app opnieuw op uw apparaat om deze opnieuw te valideren. Deze keer moet u worden gevraagd om camera machtigingen. Zodra u is goedgekeurd, ziet u de weergave van de camerafeed op uw scherm.

## <a name="place-an-object-in-the-real-world"></a>Plaats een object in de echte wereld

Laten we een object & plaatsen met uw app. Voeg eerst de volgende `app\java\<PackageName>\MainActivity`invoer toe aan uw:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Voeg vervolgens de volgende lidvariabelen `MainActivity` toe aan uw klas:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Voeg vervolgens de volgende `app\java\<PackageName>\MainActivity` `onCreate()` code toe aan uw methode. Deze code zal aansluiten op `handleTap()`een luisteraar, genaamd, die detecteert wanneer de gebruiker tikt op het scherm op uw apparaat. Als de tik toevallig op een echte wereld oppervlak dat al is erkend door arcore's tracking, zal de luisteraar worden uitgevoerd.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Voeg tot slot `handleTap()` de volgende methode toe, die alles aan elkaar bindt. Het zal een bol creëren en deze op de aangeboorde locatie plaatsen. De bol zal in eerste `this.recommendedSessionProgress` instantie zwart zijn, omdat het nu op nul is ingesteld. Deze waarde wordt later aangepast.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Implementeer uw](#trying-it-out) app opnieuw op uw apparaat om deze opnieuw te valideren. Deze keer u zich op uw apparaat verplaatsen om ARCore ertoe te brengen uw omgeving te herkennen. Tik vervolgens op het scherm om & plaats je zwarte bol over het oppervlak van je keuze.

## <a name="attach-a-local-azure-spatial-anchor"></a>Een lokaal Azure-ruimtelijk anker toevoegen

Wijzigen `Gradle Scripts\build.gradle (Module: app)` om de volgende vermelding op te nemen. Deze code zorgt ervoor dat uw app zich richt op Azure Spatial Anchors versie 2.2.0. Dat gezegd hebbende, moet verwijzen naar een recente versie van Azure Spatial Anchors werken. De release notes vind je [hier.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klik met `app\java\<PackageName>` ->de rechtermuisknop op **Nieuwe**->**Java-klasse**. **Stel Naam** in _op MyFirstApp_en **Superclass** op _android.app.Application_. Laat de andere opties zoals ze zijn. Klik op **OK**. Er wordt `MyFirstApp.java` een bestand aangemaakt dat wordt aangeroepen. Voeg er de volgende import aan toe:

```java
import com.microsoft.CloudServices;
```

Voeg vervolgens de volgende code `MyFirstApp` toe in de nieuwe klasse, die ervoor zorgt dat Azure Spatial Anchors wordt geïnitialiseerd met de context van uw toepassing.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Wijzig nu `app\manifests\AndroidManifest.xml` om de volgende vermelding `<application>` in het hoofdknooppunt op te nemen. Met deze code wordt de klasse Toepassing die u hebt gemaakt, aangesloten op uw app.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Terug `app\java\<PackageName>\MainActivity`in, voeg de volgende invoer in het:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Voeg vervolgens de volgende lidvariabelen `MainActivity` toe aan uw klas:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Laten we vervolgens de `initializeSession()` volgende methode `mainActivity` in uw klas toevoegen. Eenmaal gebeld, zorgt het ervoor dat een Azure Spatial Anchors-sessie wordt gemaakt en goed wordt geïnitialiseerd tijdens het opstarten van uw app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Nu, laten we `initializeSession()` je methode `onCreate()` aansluiten op je methode. We zorgen er ook voor dat frames uit uw camerafeed worden verzonden naar Azure Spatial Anchors SDK voor verwerking.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Voeg ten slotte de `handleTap()` volgende code toe aan uw methode. Het zal een lokaal Azure Spatial Anchor koppelen aan de zwarte bol die we in de echte wereld plaatsen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Implementeer uw](#trying-it-out) app opnieuw. Beweeg rond uw apparaat, tik op het scherm en plaats een zwarte bol. Deze keer wordt uw code echter een lokaal Azure-ruimtelijk anker aan uw bol maken en eraan koppelen.

Voordat u verder gaat, moet u een Azure Spatial Anchors-account-id en -sleutel maken als u deze nog niet hebt. Volg de volgende sectie om ze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Upload uw lokale anker in de cloud

Zodra u uw Azure Spatial Anchors-account-id en `app\java\<PackageName>\MainActivity`-sleutel hebt, kunnen we teruggaan naar binnen, de volgende invoer toevoegen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Voeg vervolgens de volgende lidvariabelen `MainActivity` toe aan uw klas:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Voeg nu de volgende `initializeSession()` code toe aan uw methode. Ten eerste kan uw app met deze code de voortgang van de Azure Spatial Anchors SDK controleren terwijl deze frames verzamelt uit uw camerafeed. Zoals het doet, zal de kleur van uw bol beginnen te veranderen van zijn oorspronkelijke zwart, in grijs. Vervolgens wordt het wit zodra er voldoende frames zijn verzameld om uw anker in de cloud in te dienen. Ten tweede biedt deze code de referenties die nodig zijn om te communiceren met de back-end van de cloud. Hier configureert u uw app om uw account-id en sleutel te gebruiken. U hebt ze gekopieerd naar een teksteditor bij [het instellen van de bron Ruimtelijke ankers](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Voeg vervolgens de `uploadCloudAnchorAsync()` volgende `mainActivity` methode toe in uw klasse. Eenmaal aangeroepen, zal deze methode asynchroon wachten tot er voldoende frames worden verzameld van uw apparaat. Zodra dat gebeurt, wordt de kleur van uw bol in geel omgezet en wordt uw lokale Azure Spatial Anchor in de cloud geüpload. Zodra de upload is voltooid, retourneert de code een anker-id.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Tot slot, laten we alles aan elkaar haken. Voeg `handleTap()` in uw methode de volgende code toe. Het zal `uploadCloudAnchorAsync()` je methode aanroepen zodra je bol is gemaakt. Zodra de methode terugkeert, zal de onderstaande code een laatste update uitvoeren voor uw bol, en de kleur veranderen in blauw.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Implementeer uw](#trying-it-out) app opnieuw. Beweeg rond uw apparaat, tik op het scherm en plaats uw bol. Deze keer, hoewel, zal uw bol zijn kleur veranderen van zwart naar wit, als camera frames worden verzameld. Zodra we genoeg frames hebben, wordt de bol geel en wordt het uploaden van de cloud gestart. Zodra de upload is voltooid, wordt je bol blauw. Optioneel u het `Logcat` venster in Android Studio ook gebruiken om de logboekberichten te controleren die uw app verzendt. Bijvoorbeeld de voortgang van de sessie tijdens het frame vangt en de anker-id die de cloud retourneert zodra de upload is voltooid.

## <a name="locate-your-cloud-spatial-anchor"></a>Zoek uw ruimtelijk anker in de cloud

Een van uw anker is geüpload naar de cloud, we zijn klaar om te proberen het opnieuw te lokaliseren. Laten we eerst de volgende invoer toevoegen aan uw code.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Laten we vervolgens de volgende code `handleTap()` toevoegen aan uw methode. Deze code zal:

- Verwijder onze bestaande blauwe bol van het scherm.
- Initialiseer onze Azure Spatial Anchors-sessie opnieuw. Deze actie zal ervoor zorgen dat het anker dat we gaan vinden afkomstig is van de cloud in plaats van het lokale anker dat we hebben gemaakt.
- Geef een query uit voor het anker dat we naar de cloud hebben geüpload.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Laten we nu de code aansluiten die wordt aangeroepen wanneer het anker waar we naar zoeken zich bevindt. Voeg `initializeSession()` in de methode de volgende code toe. Dit fragment creëert & een groene bol plaatsen zodra het ruimtelijk anker van de wolk zich bevindt. Het zal ook weer op het scherm tikken, zodat u het hele scenario opnieuw herhalen: maak een ander lokaal anker, upload het en zoek het opnieuw.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Dat is alles. [Implementeer](#trying-it-out) uw app nog een laatste keer om het hele scenario van eind tot eind uit te proberen. Beweeg rond uw apparaat en plaats uw zwarte bol. Blijf vervolgens uw apparaat bewegen om cameraframes vast te leggen totdat de bol geel wordt. Je lokale anker wordt geüpload en je bol wordt blauw. Tik tot slot nogmaals op uw scherm, zodat uw lokale anker wordt verwijderd, en vervolgens vragen we naar de tegenhanger van de cloud. Blijf uw apparaat verplaatsen totdat uw ruimtelijk anker in de cloud zich bevindt. Een groene bol moet op de juiste plaats worden weergegeven en u het hele scenario opnieuw & herhalen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
