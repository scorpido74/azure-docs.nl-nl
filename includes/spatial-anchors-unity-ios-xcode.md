---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89536250"
---
Selecteer **Build**. Selecteer in het dialoogvenster dat wordt geopend, een map waarnaar u het Xcode-project wilt exporteren.

Als het exporteren is voltooid, wordt er een map weergegeven met het geëxporteerde Xcode-project.

> [!NOTE]
> Als er een venster wordt weergegeven waarin u wordt gevraagd of u wilt vervangen of toevoegen, wordt u aangeraden **Toevoegen** te selecteren, omdat dat sneller gaat. U hoeft alleen **Vervangen** te selecteren als u de activa in uw scène wilt wijzigen. (Dit is bijvoorbeeld het geval als u bovenliggende/onderliggende relaties toevoegt, verwijdert of wijzigt, of als u eigenschappen toevoegt, verwijdert of wijzigt.) Als u alleen wijzigingen in de broncode aanbrengt, moet **Toevoegen** voldoende zijn.

## <a name="open-the-xcode-project"></a>Xcode-project openen

U kunt `Unity-iPhone.xcodeproj` nu openen in Xcode. U kunt Xcode starten en het geëxporteerde project `Unity-iPhone.xcodeproj` openen of het project in Xcode starten door de volgende opdracht uit te voeren vanaf de locatie waar u het project hebt geëxporteerd:

```bash
open ./Unity-iPhone.xcodeproj
```

Selecteer het hoofdknooppunt **Unity-iPhone** om de projectinstellingen weer te geven en selecteer vervolgens het tabblad **General**.

Controleer of onder **Signing** de instelling **Automatically manage signing** is ingeschakeld. Als dat niet het geval is, schakelt u deze in en selecteert u **Enable Automatic** in het dialoogvenster dat wordt weergegeven om de opbouwinstellingen opnieuw in te stellen.

Controleer of onder **Deployment Info** de optie **Deployment Info** is ingesteld op `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Verbind het iOS-apparaat met de Mac en stel het **actieve schema** in voor uw iOS-apparaat.

![Apparaat selecteren](./media/spatial-anchors-unity/select-device.png)

Selecteer **Build and then run the current scheme** (Het huidige schema compileren en uitvoeren).

![Implementeren en uitvoeren](./media/spatial-anchors-unity/deploy-run.png)
