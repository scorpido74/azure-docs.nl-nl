---
title: 'Quick Start: een iOS-app Unit maken'
description: In deze quickstart leert u een iOS-app maken met Unity en met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615384"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Quick Start: een app Unitruimte maken met ruimtelijke Azure-ankers

In deze Quick Start wordt beschreven hoe u een iOS-app unit maakt met behulp van [Azure spatiale ankers](../overview.md). Azure Spatial Anchors is een platformoverschrijdende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARKit iOS-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * Build-instellingen voor Unity voorbereiden
> * Spatial Anchors-account-id en -accountsleutel configureren
> * Xcode-project exporteren
> * Implementeren en uitvoeren op een iOS-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een macOS-computer met <a href="https://unity3d.com/get-unity/download" target="_blank">unit 2019,1 of 2019,2</a>, de meest recente versie van <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>en <a href="https://cocoapods.org" target="_blank">CocoaPods</a> is geïnstalleerd.
- Git geïnstalleerd via HomeBrew. Voer de volgende opdracht in op één regel van de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Voer vervolgens `brew install git` uit en `brew install git-lfs`.
- Een door een ontwikkelaar geactiveerd en <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">met ARKit compatibel</a> iOS-apparaat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het unit-voorbeeld project downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configureer de account-id en -sleutel

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchors.Examples/Scenes` en open het scènebestand `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Sla de scène op door **Bestand** -> **Opslaan** te selecteren.

## <a name="export-the-xcode-project"></a>Xcode-project exporteren

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Volg de instructies in de app om een anker te plaatsen en terug te halen.

Wanneer u klaar bent, stopt u de app door te klikken op **stoppen** in Xcode.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="rendering-issues"></a>Weergave problemen

Als u bij het uitvoeren van de app de camera niet als achtergrond ziet (u ziet bijvoorbeeld lege, blauwe of andere structuren) dan moet u waarschijnlijk assets opnieuw in Unity importeren. De app stoppen. Kies in het bovenste menu in Unity **Assets -> Alles opnieuw importeren**. Voer de vervolgens opnieuw app uit.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problemen met macOS Catalina (10,15)

Als u onlangs een update hebt uitgevoerd voor macOS Catalina (10,15) en CocoaPods al eerder hebt geïnstalleerd, kan het zijn dat CocoaPods een gebroken status heeft en de bestanden van uw peul en `.xcworkspace` project niet goed kan configureren. U kunt dit probleem oplossen door de volgende opdrachten uit te voeren om CocoaPods opnieuw te installeren:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Unity 2019,3

Als gevolg van het verbreken van wijzigingen, wordt unit 2019,3 momenteel niet ondersteund. Gebruik unit 2019,1 of 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
