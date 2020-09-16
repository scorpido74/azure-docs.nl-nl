---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 8e3f58a5a88495263396c4e915a6363ad065c202
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89570254"
---
## <a name="android"></a>[Android](#tab/Android)

Het Java-voorbeeld voor Android biedt ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `SharedActivity.java` in de map met voorbeelden in Android Studio. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedActivity.java`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Het Objective-C-voorbeeld voor iOS biedt ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `SharedDemoViewController.m` in de map met voorbeelden. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedDemoViewController.m`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

De Xamarin-voorbeelden voor zowel Android als iOS bieden ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `AccountDetails.cs` in de map met voorbeelden. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `AnchorSharingServiceUrl` in het `AccountDetails.cs`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Een Android-apparaat instellen

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Een iOS-apparaat instellen

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Ga in het deelvenster **Project** naar `Assets\AzureSpatialAnchors.Examples\Resources`. Selecteer `SpatialAnchorSamplesConfig`. Voer vervolgens in het deelvenster **Inspector** de `Sharing Anchors Service url` (van uw ASP.NET-web-app-implementatie in Azure) als de waarde voor `Base Sharing Url` in, waarbij u `index.html` door `api/anchors` vervangt. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

Sla de scène op door **Bestand** > **Opslaan** te selecteren.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

### <a name="deploy-to-android-device"></a>Implementeren op een Android-apparaat

Meld u aan op uw Android-apparaat en maak verbinding met uw computer via een USB-kabel.

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Controleer onder **Scènes in Build** of er naast elke scène een selectievakje staat.

Zorg ervoor dat er naast **Project exporteren** geen selectievakje staat. Selecteer **Bouwen en uitvoeren**. U wordt gevraagd uw `.apk`-bestand op te slaan. U kunt hiervoor een willekeurige naam kiezen.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementeren op een iOS-apparaat

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Controleer onder **Scènes in Build** of er naast elke scène een selectievakje staat.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

In Xcode stopt u de app door **Stop** te selecteren.
