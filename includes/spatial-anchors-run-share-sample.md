---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903955"
---
## <a name="set-up-your-device-in-unity"></a>Uw apparaat instellen in Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Een Android-apparaat instellen

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Een iOS-apparaat instellen

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>De account-id en-sleutel configureren

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchorsPlugin/Examples` en open het scènebestand `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Ga in het deel venster **project** naar `Assets\AzureSpatialAnchors.Examples\Resources`. Selecteer `SpatialAnchorSamplesConfig`. Voer vervolgens in het deel venster **Inspector** de `Sharing Anchors Service url` (van uw ASP.net Web-app Azure-implementatie) in als de `Base Sharing Url`waarde voor `index.html` , `api/anchors`vervangen door. Dit moet er als volgt uitzien: `https://<app_name>.azurewebsites.net/api/anchors`.

Sla de scène op door **Bestand** > **Opslaan** te selecteren.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

### <a name="deploy-to-android-device"></a>Implementeren op Android-apparaat

Meld u aan bij uw Android-apparaat en verbind deze met de computer via een USB-kabel.

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg ervoor dat onder **scènes in Build**alle scènes een vinkje bevatten.

Zorg ervoor dat het **export project** geen vinkje heeft. Selecteer **Build en run**. U wordt gevraagd het `.apk` bestand op te slaan. U kunt een wille keurige naam kiezen.

Zodra de app is gestart, gebruikt u in het dialoog venster **demo kiezen** de pijl naar links of rechts om de optie **LocalShare** te selecteren en tikt u op **Go!** . Volg de instructies in de app. U kunt **& anker maken** of een **gedeeld anker**selecteren.

In het eerste scenario kunt u een anker maken dat later op hetzelfde apparaat of op een andere kan worden geplaatst.
Als u de app al op hetzelfde apparaat of op een andere manier hebt uitgevoerd, kunt u het tweede scenario gebruiken om eerder gedeelde ankers te vinden. Nadat u het scenario hebt gekozen, wordt u door de app begeleid met verdere instructies. U wordt bijvoorbeeld gevraagd om uw apparaat te verplaatsen om omgevings gegevens te verzamelen. Later plaatst u een anker in de wereld, wacht u totdat het is opgeslagen, enzovoort.

### <a name="deploy-to-an-ios-device"></a>Implementeren op een iOS-apparaat

Open **Build Settings** door **File** > **Build Settings** te selecteren.

Zorg ervoor dat onder **scènes in Build**alle scènes een vinkje bevatten.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Zodra de app is gestart, gebruikt u in het dialoog venster **demo kiezen** de pijl naar links of rechts om de optie **LocalShare** te selecteren en tikt u op **Go!** . Volg de instructies in de app. U kunt **& anker maken** of een **gedeeld anker**selecteren.

In het eerste scenario kunt u een anker maken dat later op hetzelfde apparaat of op een andere kan worden geplaatst.
Als u de app al op hetzelfde apparaat of op een andere manier hebt uitgevoerd, kunt u het tweede scenario gebruiken om eerder gedeelde ankers te vinden. Nadat u het scenario hebt gekozen, wordt u door de app begeleid met verdere instructies. U wordt bijvoorbeeld gevraagd om uw apparaat te verplaatsen om omgevings gegevens te verzamelen. Later plaatst u een anker in de wereld, wacht u totdat het is opgeslagen, enzovoort.

In Xcode stopt u de app door **stoppen**te selecteren.
