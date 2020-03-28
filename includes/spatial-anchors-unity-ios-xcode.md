---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933483"
---
Selecteer **Bouwen**. Selecteer in het dialoogvenster dat wordt geopend een map om het Xcode-project naar te exporteren.

Wanneer de export is voltooid, wordt een map weergegeven die het geëxporteerde Xcode-project bevat.

> [!NOTE]
> Als er een venster wordt weergegeven waarin u vraagt of u wilt vervangen of toevoegen, raden we u aan **Toevoegen te** selecteren omdat het sneller is. U hoeft alleen **vervangen** te selecteren als u elementen in uw scène wijzigt. (Bijvoorbeeld als u bovenliggende/onderliggende relaties toevoegt, verwijdert of wijzigt, of als u eigenschappen toevoegt, verwijdert of wijzigt.) Als u alleen wijzigingen in de broncode doorvoert, moet **Toevoegen** voldoende zijn.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Het Xcode-project converteren naar xcworkspace met azure-verwijzingen naar ruimtelijke ankers

Voer deze opdracht in de map met het geëxporteerde Xcode-project uit in de Terminal om de benodigde CocoaPods voor het project te installeren:

```bash
pod install --repo-update
```

Nu `Unity-iPhone.xcworkspace` u het project openen in Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Bekijk [hier](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) de stappen voor het oplossen van problemen als u problemen hebt met CocoaPod na een upgrade naar macOS Catalina (10.15).

Selecteer het **hoofdunity-iPhone-knooppunt** om de projectinstellingen weer te geven en selecteer vervolgens het tabblad **Algemeen.**

Controleer **onder Ondertekenen**of het automatisch beheren van **ondertekening** is ingeschakeld. Als dit niet het is, schakelt u deze in en selecteert u **Automatisch inschakelen** in het dialoogvenster dat wordt weergegeven om de build-instellingen opnieuw in te stellen.

Controleer of onder **Deployment Info** de optie **Deployment Info** is ingesteld op `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

![Apparaat selecteren](./media/spatial-anchors-unity/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

![Implementeren en uitvoeren](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Als er `library not found for -lPods-Unity-iPhone` een fout wordt `.xcodeproj` weergegeven, hebt `.xcworkspace` u waarschijnlijk het bestand geopend in plaats van het bestand.
