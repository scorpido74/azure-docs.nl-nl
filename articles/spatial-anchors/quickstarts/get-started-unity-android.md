---
title: 'Snelstartgids: een app Unitruimte Android maken'
description: In deze quickstart leert u een Android-app maken met Unity en met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615448"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snelstartgids: een app Unitruimte maken met ruimtelijke Azure-ankers

In deze Quick Start wordt beschreven hoe u een app voor de Android-eenheid maakt met behulp van [Azure spatiale ankers](../overview.md). Azure Spatial Anchors is een platformoverschrijdende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARCore Android-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * Build-instellingen voor Unity voorbereiden
> * Spatial Anchors-account-id en -accountsleutel configureren
> * Het Android Studio-project exporteren
> * Implementeren en uitvoeren op een Android-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Windows-of macOS-computer met <a href="https://unity3d.com/get-unity/download" target="_blank">unit 2019,1 of 2019,2</a> , met inbegrip van de Android-build-ondersteuning en android SDK & de modules voor ndk-hulpprogram ma's.
  - Als u werkt met Windows, hebt u ook <a href="https://git-scm.com/download/win" target="_blank">Git nodig voor Windows</a> en <a href="https://git-lfs.github.com/">Git LF</a>.
  - Als u op macOS werkt, kunt u git installeren via HomeBrew. Voer de volgende opdracht in op één regel van de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Voer vervolgens `brew install git` uit en `brew install git-lfs`.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">ontwikkelaar-geactiveerd</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.
  - Er zijn mogelijk extra apparaatstuurprogramma's vereist om uw computer te laten communiceren met uw Android-apparaat. Zie [hier](https://developer.android.com/studio/run/device.html) voor meer informatie en instructies.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het unit-voorbeeld project downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configureer de account-id en -sleutel

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchors.Examples/Scenes` en open het scènebestand `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Sla de scène op door **Bestand** -> **Opslaan** te selecteren.

## <a name="export-the-android-studio-project"></a>Het Android Studio-project exporteren

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecteer uw apparaat in het **uitvoerings apparaat** en klik op **bouwen en uitvoeren**. U wordt gevraagd om een `.apk`-bestand op te slaan waarvoor u een wille keurige naam kunt kiezen.

Volg de instructies in de app om een anker te plaatsen en terug te halen.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="rendering-issues"></a>Weergave problemen

Als u bij het uitvoeren van de app de camera niet als achtergrond ziet (u ziet bijvoorbeeld lege, blauwe of andere structuren) dan moet u waarschijnlijk assets opnieuw in Unity importeren. De app stoppen. Kies in het bovenste menu in Unity **Assets -> Alles opnieuw importeren**. Voer de vervolgens opnieuw app uit.

### <a name="unity-20193"></a>Unity 2019,3

Als gevolg van het verbreken van wijzigingen, wordt unit 2019,3 momenteel niet ondersteund. Gebruik unit 2019,1 of 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
