---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72933483"
---
Selecteer **Build**. In het dialoog venster dat wordt geopend, selecteert u een map waarnaar u het Xcode-project wilt exporteren.

Wanneer het exporteren is voltooid, wordt er een map met het geëxporteerde Xcode-project weer gegeven.

> [!NOTE]
> Als er een venster wordt weer gegeven waarin u wordt gevraagd of u wilt vervangen of toevoegen, wordt u aangeraden om **toevoegen** te selecteren, omdat het sneller is. U hoeft alleen **vervangen** te selecteren als u activa in uw scène wilt wijzigen. (Als u bijvoorbeeld bovenliggende/onderliggende relaties toevoegt, verwijdert of wijzigt, of als u eigenschappen toevoegt, verwijdert of wijzigt.) Als u alleen wijzigingen in de bron code aanbrengt, moet de **toevoeging** voldoende zijn.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Het Xcode-project converteren naar xcworkspace met ruimtelijke verwijzingen van Azure

Voer in de geëxporteerde Xcode-projectmap deze opdracht uit in de terminal om de benodigde CocoaPods voor het project te installeren:

```bash
pod install --repo-update
```

U kunt nu openen `Unity-iPhone.xcworkspace` om het project te openen in Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Zie de stappen voor het oplossen van [problemen als u](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) CocoaPod problemen ondervindt na een upgrade naar macOS Catalina (10,15).

Selecteer het knoop punt hoofd **eenheid van iPhone** om de project instellingen weer te geven en selecteer vervolgens het tabblad **Algemeen** .

Zorg ervoor dat het hand tekening **automatisch beheren** onder **ondertekenen**is ingeschakeld. Als dat niet het geval is, schakelt u deze in en selecteert u **automatisch inschakelen** in het dialoog venster dat wordt weer gegeven om de instellingen voor het bouwen opnieuw in te stellen.

Controleer of onder **Deployment Info** de optie **Deployment Info** is ingesteld op `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

![Apparaat selecteren](./media/spatial-anchors-unity/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

![Implementeren en uitvoeren](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Als er een `library not found for -lPods-Unity-iPhone` fout wordt weer gegeven, hebt u `.xcodeproj` waarschijnlijk het bestand in `.xcworkspace` plaats van het bestand geopend.
