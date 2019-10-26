---
title: 'Quickstart: een iOS-app maken met Azure Spatial Anchors | Microsoft Docs'
description: In deze quickstart leert u een iOS-app maken met behulp van Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 42a97646a6881ead61ceeaff3fe8c0c8d73d6057
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929400"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Snelstartgids: een iOS-app maken met behulp van ruimtelijke Azure-ankers in SWIFT of objectief-C

In deze quickstart wordt besproken hoe u een iOS-app maakt met behulp van [Azure Spatial Anchors](../overview.md) in Swift of Objective-C. Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een ARKit iOS-app gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een iOS-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een macOS-machine met ontwikkelaarstoegang met daarop <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> en <a href="https://cocoapods.org" target="_blank">CocoaPods</a> geïnstalleerd.
- Git geïnstalleerd via HomeBrew. Voer de volgende opdracht in op één regel van de terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Voer vervolgens `brew install git`uit.
- Een door een ontwikkelaar geactiveerd en <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">met ARKit compatibel</a> iOS-apparaat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

Gebruik de terminal om de volgende acties uit te voeren.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

De benodigde pods installeren met CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Navigeer naar `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Navigeer naar `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Voer `pod install --repo-update` uit om de CocoaPods voor het project te installeren.

Open nu `.xcworkspace` in Xcode.

> [!NOTE]
> Zie de stappen voor het oplossen van [problemen als u](#cocoapods-issues-on-macos-catalina-1015) CocoaPod problemen ondervindt na een upgrade naar macOS Catalina (10,15).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configureer de account-id en -sleutel

De volgende stap is het configureren van de app voor het gebruik van uw account-id en de account sleutel. U hebt deze in een tekst editor gekopieerd bij [het instellen van de bron voor ruimtelijke ankers](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Open `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Zoek het veld `spatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het veld `spatialAnchorsAccountId` en vervang `Set me` met de account-id.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Open `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het veld `SpatialAnchorsAccountId` en vervang `Set me` met de account-id.

---

## <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

![Apparaat selecteren](./media/get-started-ios/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

![Implementeren en uitvoeren](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Als u de fout `library not found for -lPods-SampleObjC` ziet, hebt u waarschijnlijk het bestand `.xcodeproj` geopend in plaats van `.xcworkspace`. Open `.xcworkspace` en probeer het opnieuw.

In Xcode stopt u de app door op **Stop** te klikken.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problemen met macOS Catalina (10,15)

Als u onlangs een update hebt uitgevoerd voor macOS Catalina (10,15) en CocoaPods al eerder hebt geïnstalleerd, kan het zijn dat CocoaPods een gebroken status heeft en de bestanden van uw peul en `.xcworkspace` project niet goed kan configureren. U kunt dit probleem oplossen door de volgende opdrachten uit te voeren om CocoaPods opnieuw te installeren:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
