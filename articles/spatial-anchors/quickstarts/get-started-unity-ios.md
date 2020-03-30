---
title: 'Snelstart: een Unity iOS-app maken'
description: In deze quickstart leert u een iOS-app maken met Unity en met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240588"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Snelstart: een Unity iOS-app maken met Azure Spatial Anchors

Deze quickstart heeft betrekking op het maken van een Unity iOS-app met [azure spatial anchors.](../overview.md) Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARKit iOS-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * Build-instellingen voor Unity voorbereiden
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Xcode-project exporteren
> * Implementeren en uitvoeren op een iOS-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een macOS-machine met <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 of 2019.2</a>, de nieuwste versie van <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>en <a href="https://cocoapods.org" target="_blank">CocoaPods</a> geïnstalleerd.
- Git geïnstalleerd via HomeBrew. Voer de volgende opdracht in op `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`één regel van de terminal: . Ren dan `brew install git` `brew install git-lfs`en.
- Een door een ontwikkelaar geactiveerd en <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">met ARKit compatibel</a> iOS-apparaat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het voorbeeldproject Unity downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

Ga in het deelvenster **Project** naar `Assets/AzureSpatialAnchors.Examples/Scenes` en open het scènebestand `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Sla de scène op door **Bestand** -> **opslaan te**selecteren .

## <a name="export-the-xcode-project"></a>Xcode-project exporteren

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Volg de instructies in de app om een anker te plaatsen en terug te halen.

Wanneer u klaar bent, stopt u de app door op **Stoppen** in Xcode te drukken.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="rendering-issues"></a>Rendering problemen

Als u bij het uitvoeren van de app de camera niet als achtergrond ziet (u ziet bijvoorbeeld lege, blauwe of andere structuren) dan moet u waarschijnlijk assets opnieuw in Unity importeren. De app stoppen. Kies in het bovenste menu in Unity de optie **Activa -> Importeer alles opnieuw**. Voer de vervolgens opnieuw app uit.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problemen op macOS Catalina (10.15)

Als u onlangs hebt bijgewerkt naar macOS Catalina (10.15) en cacaopods vooraf hebt geïnstalleerd, is `.xcworkspace` CacaoPods mogelijk in een gebroken staat en kunnen ze uw pods en projectbestanden niet goed configureren. Als u dit probleem wilt oplossen, moet u CocoaPods opnieuw installeren door de volgende opdrachten uit te voeren:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Eenheid 2019.3

Vanwege het breken van veranderingen wordt Unity 2019.3 momenteel niet ondersteund. Gebruik Unity 2019.1 of 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Ruimtelijke ankers delen op verschillende apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
