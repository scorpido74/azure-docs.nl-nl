---
title: 'Zelf studie: een nieuwe Android-app maken'
description: In deze zelf studie leert u hoe u een nieuwe Android-app kunt maken met behulp van Azure spatiale ankers.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c02595ebdb2f011dcc94b517771a79a4b2df754d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161717"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Zelf studie: stapsgewijze instructies voor het maken van een nieuwe Android-app met behulp van ruimtelijke ankers van Azure

In deze zelf studie wordt uitgelegd hoe u een nieuwe Android-app maakt die de ARCore-functionaliteit integreert met Azure spatiale ankers.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Windows-of macOS-computer met <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">ontwikkelaar-geactiveerd</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.

## <a name="getting-started"></a>Aan de slag

Android Studio starten. Klik in het venster **Welkom bij Android Studio** op **een nieuw Android Studio-project starten**. Als u een project al hebt geopend, selecteert u **bestand**->**Nieuw project**.

Kies in het venster **Nieuw project maken** , onder de sectie **telefoon en Tablet** , **lege activiteit**en klik op **volgende**. Kies vervolgens bij **Mini maal API-niveau**`API 26: Android 8.0 (Oreo)`en zorg ervoor dat de **taal** is ingesteld op `Java`. U kunt de naam van het project & locatie en de naam van het pakket wijzigen. Wijzig de andere opties. Klik op **Voltooien**. Het **installatie programma** van het onderdeel wordt uitgevoerd. Zodra de bewerking is voltooid, klikt u op **volt ooien**. Na sommige verwerking wordt de IDE door Android Studio geopend.

## <a name="trying-it-out"></a>Probeer het uit

Als u uw nieuwe app wilt testen, sluit u uw apparaat met ontwikkel aars aan op uw ontwikkel machine met een USB-kabel. Klik op **uitvoeren**->**app uit te voeren**. Selecteer uw apparaat in het venster **implementatie doel selecteren** en klik op **OK**. Android Studio installeert de app op het aangesloten apparaat en start het. Nu ziet u "Hallo wereld!" wordt weer gegeven in de app die op uw apparaat wordt uitgevoerd. Klik op **uitvoeren**->de **app te stoppen**.

## <a name="integrating-_arcore_"></a>_ARCore_ integreren

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> is het platform van Google voor het ontwikkelen van uitgebreide realiteit-ervaringen, waardoor uw apparaat de positie kan bijhouden wanneer het wordt verplaatst en de eigen inzichten van de echte wereld worden gebouwd.

Wijzig `app\manifests\AndroidManifest.xml` zodat de volgende vermeldingen worden meegenomen in het hoofd `<manifest>` knoop punt. Dit code fragment doet enkele dingen:

- Hiermee kan uw app toegang krijgen tot de camera van uw apparaat.
- Er wordt ook gegarandeerd dat uw app alleen zichtbaar is in de Google Play Store op apparaten die ondersteuning bieden voor ARCore.
- Hiermee configureert u de Google Play Store voor het downloaden en installeren van ARCore, als deze nog niet is geïnstalleerd, wanneer uw app is geïnstalleerd.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat de volgende vermelding moet worden toegevoegd. Met deze code zorgt u ervoor dat uw app is gericht op ARCore versie 1,8. Na deze wijziging wordt er mogelijk een melding weer van Gradle u wordt gevraagd om te synchroniseren: Klik op **Nu synchroniseren**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>_Sceneform_ integreren

Met <a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> kunt u eenvoudig realistische 3D-scènes weer geven in uitgebreide reality-apps, zonder dat u hoeft te leren van OpenGL.

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat de volgende vermeldingen worden toegevoegd. Met deze code kan uw app taal constructies van Java 8 gebruiken, die `Sceneform` vereist. Het zorgt er ook voor dat uw app doelen `Sceneform` versie 1,8, omdat deze moet overeenkomen met de versie van ARCore die door uw app wordt gebruikt. Na deze wijziging wordt er mogelijk een melding weer van Gradle u wordt gevraagd om te synchroniseren: Klik op **Nu synchroniseren**.

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

Open uw `app\res\layout\activity_main.xml`en vervang het bestaande Hello Wolrd-`<TextView>` element door de volgende ArFragment. Deze code zorgt ervoor dat de camera feed wordt weer gegeven op het scherm, waardoor ARCore de positie van uw apparaat kan bijhouden wanneer het wordt verplaatst.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Implementeer](#trying-it-out) uw app opnieuw op uw apparaat om het opnieuw te valideren. Deze keer moet u worden gevraagd om camera machtigingen. Na goed keuring wordt de weer gave van de camera feed op het scherm weer gegeven.

## <a name="place-an-object-in-the-real-world"></a>Plaats een object in de praktijk

Laten we een object maken & deze met uw app te plaatsen. Voeg eerst de volgende invoer toe aan uw `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity` klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Voeg vervolgens de volgende code toe aan de methode `onCreate()` van `app\java\<PackageName>\MainActivity`. Met deze code wordt een listener met de naam `handleTap()`, die detecteert wanneer de gebruiker het scherm op het apparaat tikt. Als de kraan zich op een echt wereld vlak bevindt dat al is herkend door het volgen van de ARCore, wordt de listener uitgevoerd.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Voeg ten slotte de volgende `handleTap()` methode toe, waarmee alles aan elkaar wordt gekoppeld. Er wordt een bol gemaakt en op de getikte locatie geplaatst. De bol is in eerste instantie zwart, want `this.recommendedSessionProgress` is nu ingesteld op nul. Deze waarde wordt later aangepast.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Implementeer](#trying-it-out) uw app opnieuw op uw apparaat om het opnieuw te valideren. Deze keer kunt u door uw apparaat overstappen om ARCore te krijgen om uw omgeving te herkennen. Tik vervolgens op het scherm om uw zwarte bol te maken & plaats op het gewenste Opper vlak.

## <a name="attach-a-local-azure-spatial-anchor"></a>Een lokaal ruimtelijk anker van Azure koppelen

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat de volgende vermelding moet worden toegevoegd. Met deze code wordt ervoor gezorgd dat uw app de Azure spatiale ankers versie 2.2.0. Hiervoor moet worden verwezen naar een recente versie van Azure spatiale ankers. U vindt hier de release opmerkingen [.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klik met de rechter muisknop op `app\java\<PackageName>`**nieuwe**->**Java-klasse**->. Stel **name** in op _Mijneersteapp_en **Super klasse** to _Android. app. Application_. Wijzig de andere opties. Klik op **OK**. Er wordt een bestand met de naam `MyFirstApp.java` gemaakt. Voeg de volgende import toe:

```java
import com.microsoft.CloudServices;
```

Voeg vervolgens de volgende code toe in de nieuwe `MyFirstApp`-klasse, waardoor ruimtelijke bevestigingen van Azure worden geïnitialiseerd met de context van uw toepassing.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Wijzig nu `app\manifests\AndroidManifest.xml` zodat de volgende vermelding wordt vermeld in het hoofd knooppunt `<application>`. Deze code koppelt de toepassings klasse die u in uw app hebt gemaakt.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Voeg in `app\java\<PackageName>\MainActivity`de volgende invoer toe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity` klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Nu gaan we de volgende `initializeSession()`-methode toevoegen binnen uw `mainActivity` klasse. Zodra de app is aangeroepen, zorgt deze ervoor dat er een Azure spatiale-anker sessie wordt gemaakt en op de juiste wijze wordt geïnitialiseerd tijdens het opstarten van uw toepassing.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Nu gaan we uw `initializeSession()`-methode koppelen aan uw `onCreate()`-methode. Het is ook belang rijk dat frames van uw camera feed worden verzonden naar de SDK voor de ruimtelijke micro soft Azure voor verwerking.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Voeg tot slot de volgende code toe aan de `handleTap()` methode. Er wordt een lokaal ruimtelijk anker van Azure aangesloten op de zwarte bol die we in de praktijk plaatsen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Implementeer](#trying-it-out) uw app nog een keer. Schuif door het apparaat, tik op het scherm en plaats een zwarte bol. Deze keer is uw code echter bezig met het maken en koppelen van een lokaal ruimtelijke Azure-anker aan uw Sphere.

Voordat u verder gaat, moet u een Azure spatiale-ankers account-id en-sleutel maken, als u deze nog niet hebt. Volg de volgende sectie om ze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Uw lokale anker uploaden naar de Cloud

Zodra u uw Azure spatiale-ankers account-id en-sleutel hebt, kunnen we teruggaan in `app\java\<PackageName>\MainActivity`, de volgende invoer toevoegen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity` klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Voeg nu de volgende code toe aan de `initializeSession()` methode. Met deze code kunt u de voortgang bewaken die de Azure spatiale ankers SDK maakt, omdat deze frames van uw camera feed verzamelt. Net als bij de kleur van uw bol wordt de kleuren van de oorspronkelijke zwart gewijzigd in grijs. Vervolgens wordt de kleur wit zodra er voldoende frames zijn verzameld om uw anker naar de cloud te verzenden. Ten tweede geeft deze code de referenties op die nodig zijn om te communiceren met de back-end van de Cloud. Hier kunt u uw app configureren voor het gebruik van uw account-id en-sleutel. U hebt deze in een tekst editor gekopieerd bij [het instellen van de bron voor ruimtelijke ankers](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Voeg vervolgens de volgende `uploadCloudAnchorAsync()`-methode toe binnen uw `mainActivity`-klasse. Wanneer deze methode wordt aangeroepen, wordt asynchroon gewacht tot er voldoende frames van het apparaat zijn verzameld. Zodra dat gebeurt, wordt de kleur van uw bol overgeschakeld naar geel en wordt vervolgens uw lokale Azure-ruimtelijk anker naar de Cloud geüpload. Zodra het uploaden is voltooid, wordt met de code een anker-id geretourneerd.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Ten slotte gaan we alles aansluiten. Voeg de volgende code toe aan de methode `handleTap()`. De `uploadCloudAnchorAsync()` methode wordt aangeroepen zodra uw Sphere is gemaakt. Zodra de methode is geretourneerd, voert de volgende code een laatste update uit op uw Sphere, waardoor de kleur wordt gewijzigd in blauw.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Implementeer](#trying-it-out) uw app nog een keer. Schuif door het apparaat, tik op het scherm en plaats uw bol. De kleur van uw bol verandert echter van zwart naar wit, wanneer er camera frames worden verzameld. Zodra er voldoende frames zijn, verandert de bol in geel en wordt het uploaden van de Cloud gestart. Zodra het uploaden is voltooid, wordt uw bol blauw. U kunt eventueel ook het `Logcat`-venster in Android Studio gebruiken om de logboek berichten te bewaken die uw app verzendt. Bijvoorbeeld: de sessie voortgang tijdens frame opnamen en de anker-id die de Cloud retourneert wanneer het uploaden is voltooid.

## <a name="locate-your-cloud-spatial-anchor"></a>Het ruimtelijke fixeer punt in de Cloud zoeken

Er wordt een anker naar de Cloud geüpload, zodat u het opnieuw kunt proberen. Eerst voegen we de volgende invoer toe aan uw code.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Vervolgens voegen we de volgende code toe aan de `handleTap()` methode. Deze code gaat als volgt:

- Verwijder onze bestaande blauwe bol van het scherm.
- Initialiseer onze Azure Spatial-anker sessie opnieuw. Met deze actie zorgt u ervoor dat het anker dat we gaan vinden, afkomstig is uit de cloud in plaats van het lokale anker dat we hebben gemaakt.
- Geef een query op voor het anker dat is geüpload naar de Cloud.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nu gaan we de code die wordt aangeroepen wanneer er een query voor het anker is gevonden. Voeg de volgende code toe in de `initializeSession()` methode. Met dit fragment wordt een groene bol gemaakt & geplaatst zodra het ruimtelijke bevinden van de Cloud zich bevindt. U kunt ook het hele scenario nog een keer herhalen: Maak een ander lokaal anker, upload het en zoek het opnieuw.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Dat is alles. [Implementeer](#trying-it-out) uw app een laatste keer opnieuw om het hele scenario end-to-end uit te proberen. Ga op het apparaat door en plaats uw zwarte bol. Blijf vervolgens uw apparaat verplaatsen naar camera frames vastleggen totdat de bol geel draait. Uw lokale anker wordt geüpload en uw Sphere wordt blauw. Tik ten slotte nogmaals op uw scherm, zodat uw lokale anker wordt verwijderd en zoek vervolgens naar de Cloud equivalent. Blijf door gaan met het verplaatsen van uw apparaat totdat het ruimtelijke bewaarde in de Cloud zich bevindt. Een groene bol moet op de juiste locatie worden weer gegeven en u kunt afspoelen & het hele scenario opnieuw te herhalen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
