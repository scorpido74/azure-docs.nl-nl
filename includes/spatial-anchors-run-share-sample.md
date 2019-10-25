---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882324"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

Het voor beeld van Java Android ondersteunt delen tussen apparaten.
Open het bestand `SharedActivity.java` uit de map met voor beelden in Android Studio. Voer de URL in die u hebt verkregen in de vorige stap (van de Azure-implementatie van uw ASP.NET-Web-app) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedActivity.java` bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit moet er als volgt uitzien: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

Het voor beeld van doelstelling-C iOS ondersteunt delen tussen apparaten.
Open het bestand `SharedDemoViewController.m` in de map Samples. Voer de URL in die u hebt verkregen in de vorige stap (van de Azure-implementatie van uw ASP.NET-Web-app) als de waarde voor `SharingAnchorsServiceUrl` in het `SharedActivity.java` bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit moet er als volgt uitzien: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Zowel Xamarin Android-als iOS-voor beelden bieden ondersteuning voor delen via apparaten.
Open het bestand `AccountDetails.cs` in de map Samples. Voer de URL in die u hebt verkregen in de vorige stap (van de Azure-implementatie van uw ASP.NET-Web-app) als de waarde voor `AnchorSharingServiceUrl` in het `SharedActivity.java` bestand. Vervang de `index.html` in de URL door `api/anchors`. Dit moet er als volgt uitzien: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Een Android-apparaat instellen

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Een iOS-apparaat instellen

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>De account-id en-sleutel configureren

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchorsPlugin/Examples` en open het scènebestand `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Ga in het deel venster **project** naar `Assets\AzureSpatialAnchors.Examples\Resources`. Selecteer `SpatialAnchorSamplesConfig`. Voer vervolgens in het deel venster **Inspector** de `Sharing Anchors Service url` (van uw Azure-implementatie voor ASP.net Web-apps) in als de waarde voor `Base Sharing Url`, waarbij u `index.html` vervangt door `api/anchors`. Dit moet er als volgt uitzien: `https://<app_name>.azurewebsites.net/api/anchors`.

Sla de scène op door **Bestand** > **Opslaan** te selecteren.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

### <a name="deploy-to-android-device"></a>Implementeren op Android-apparaat

Meld u aan bij uw Android-apparaat en verbind deze met de computer via een USB-kabel.

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg ervoor dat onder **scènes in Build**alle scènes een vinkje bevatten.

Zorg ervoor dat het **export project** geen vinkje heeft. Selecteer **Build en run**. U wordt gevraagd uw `.apk`-bestand op te slaan. U kunt een wille keurige naam kiezen.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementeren op een iOS-apparaat

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg ervoor dat onder **scènes in Build**alle scènes een vinkje bevatten.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

In Xcode stopt u de app door **stoppen**te selecteren.
