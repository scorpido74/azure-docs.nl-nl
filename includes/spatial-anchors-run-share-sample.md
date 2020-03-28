---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882324"
---
## <a name="android"></a>[Android](#tab/Android)

Het Java-voorbeeld android ondersteunt delen op verschillende apparaten.
Open het `SharedActivity.java` bestand vanuit de map met voorbeelden in Android Studio. Voer de url in die u in de vorige stap hebt `SharingAnchorsServiceUrl` verkregen `SharedActivity.java` (uit uw ASP.NET Azure-implementatie) als de waarde voor in het bestand. Vervang `index.html` de in `api/anchors`de url door . Het moet er `https://<app_name>.azurewebsites.net/api/anchors`zo uitzien: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Het Objective-C iOS-voorbeeld ondersteunt delen op verschillende apparaten.
Open het `SharedDemoViewController.m` bestand in de map met voorbeelden. Voer de url in die u in de vorige stap hebt `SharingAnchorsServiceUrl` verkregen `SharedActivity.java` (uit uw ASP.NET Azure-implementatie) als de waarde voor in het bestand. Vervang `index.html` de in `api/anchors`de url door . Het moet er `https://<app_name>.azurewebsites.net/api/anchors`zo uitzien: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Zowel Xamarin Android- als iOS-samples ondersteunen delen op verschillende apparaten.
Open het `AccountDetails.cs` bestand in de map met voorbeelden. Voer de url in die u in de vorige stap hebt `AnchorSharingServiceUrl` verkregen `SharedActivity.java` (uit uw ASP.NET Azure-implementatie) als de waarde voor in het bestand. Vervang `index.html` de in `api/anchors`de url door . Het moet er `https://<app_name>.azurewebsites.net/api/anchors`zo uitzien: .

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Een Android-apparaat instellen

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Een iOS-apparaat instellen

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>De account-id en -sleutel configureren

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchorsPlugin/Examples` en open het scènebestand `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Navigeer **Project** in het deelvenster `Assets\AzureSpatialAnchors.Examples\Resources`Project naar . Selecteer `SpatialAnchorSamplesConfig`. Voer vervolgens **Inspector** in het deelvenster `Sharing Anchors Service url` Controle de (vanuit uw ASP.NET-web-app Azure-implementatie) in als de waarde voor `Base Sharing Url`, vervangen `index.html` door `api/anchors`. Het moet er `https://<app_name>.azurewebsites.net/api/anchors`zo uitzien: .

Sla de scène op door **Bestand** > **opslaan te**selecteren .

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

### <a name="deploy-to-android-device"></a>Implementeren op Android-apparaat

Meld u aan op uw Android-apparaat en sluit deze aan op uw computer via een USB-kabel.

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg er **onder Scenes In Build**voor dat alle scènes een vinkje ernaast hebben.

Zorg ervoor dat **Project exporteren** geen vinkje heeft. Selecteer **Bouwen en uitvoeren**. U wordt gevraagd uw `.apk` bestand op te slaan. Je er een naam voor kiezen.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementeren op een iOS-apparaat

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg er **onder Scenes In Build**voor dat alle scènes een vinkje ernaast hebben.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Stop in Xcode de app door **Stoppen te**selecteren.
