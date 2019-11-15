---
title: 'Quickstart: Android-app maken met Azure Spatial Anchors | Microsoft Docs'
description: In deze quickstart leert u een Android-app bouwen met behulp van Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 248b66a928d373678d3058be488e226ee51bbcae
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092130"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Snelstartgids: een Android-app maken met behulp van ruimtelijke Azure-ankers

In deze quickstart wordt besproken hoe u een Android-app maakt met behulp van [Azure Spatial Anchors](../overview.md) in Java of C++/NDK. Spatial Anchors is een platformoverschrijdende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARCore Android-app gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een Android-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Windows-of macOS-computer met <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Als u werkt met Windows, hebt u ook <a href="https://git-scm.com/download/win" target="_blank">Git nodig voor Windows</a>.
  - Als u op macOS werkt, kunt u git installeren via HomeBrew. Voer de volgende opdracht in op één regel van de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Voer vervolgens `brew install git`uit.
  - Als u het NDK-voor beeld wilt maken, moet u ook de Hulpprogram Ma's voor NDK en CMake 3,6 of hoger installeren in Android Studio.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerde</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.
  - Er zijn mogelijk extra apparaatstuurprogramma's vereist om uw computer te laten communiceren met uw Android-apparaat. Zie [hier](https://developer.android.com/studio/run/device.html) voor meer informatie en instructies.
- Uw app moet gericht zijn op ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Down load [hier](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) `arcore_c_api.h` en plaats deze in `Android\NDK\libraries\include`.

Initialiseer vanuit de zojuist gekloonde opslag plaats submodules door de volgende opdracht uit te voeren:

```console
git submodule update --init --recursive
```

---

Open Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selecteer **Bestaand Android Studio-project openen** en selecteer het project dat zich bevindt in `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selecteer **Bestaand Android Studio-project openen** en selecteer het project dat zich bevindt in `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is het configureren van de app voor het gebruik van uw account-id en de account sleutel. U hebt deze in een tekst editor gekopieerd bij [het instellen van de bron voor ruimtelijke ankers](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Open `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het `SpatialAnchorsAccountId`-veld en vervang `Set me` met de account-id.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Open `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het `SpatialAnchorsAccountId`-veld en vervang `Set me` met de account-id.

---

## <a name="deploy-the-app-to-your-android-device"></a>De app op uw Android-apparaat implementeren

Start het Android-apparaat, meld u aan en maak verbinding met de pc via een USB-kabel.

Selecteer **Uitvoeren** via de werkbalk van Android Studio.

![Android Studio implementeren en uitvoeren](./media/get-started-android/android-studio-deploy-run.png)

Selecteer het Android-apparaat in het dialoogvenster **Implementatiedoel selecteren** en selecteer **OK** om de app op het Android-apparaat uit te voeren.

Volg de instructies in de app om een anker te plaatsen en terug te halen.

Stop de app door **Stop** te selecteren in de werkbalk van Android Studio.

![Android Studio Stop](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
