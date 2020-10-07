---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358756"
---
## <a name="android"></a>[Android](#tab/Android)

Het Java-voorbeeld voor Android biedt ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `SharedActivity.java` in de map met voorbeelden in Android Studio. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedActivity.java`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Het Objective-C-voorbeeld voor iOS biedt ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `SharedDemoViewController.m` in de map met voorbeelden. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedDemoViewController.m`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

Implementeer de app op uw apparaat. Nadat de app is gestart, kiest u de optie **Tik om de gedeelde demo te starten**. Volg de instructies in de app. U kunt **Tik selecteren om het ankernummer te vinden** of **Tik om een anker te maken en op te slaan in de service**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

De Xamarin-voorbeelden voor zowel Android als iOS bieden ondersteuning voor het delen van gegevens op meerdere apparaten.
Open het bestand `AccountDetails.cs` in de map met voorbeelden. Voer de URL in die u in de vorige stap hebt verkregen (via uw implementatie van de ASP.NET web-app in Azure) als de waarde voor `AnchorSharingServiceUrl` in het `AccountDetails.cs`-bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit ziet er als volgt uit: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

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
