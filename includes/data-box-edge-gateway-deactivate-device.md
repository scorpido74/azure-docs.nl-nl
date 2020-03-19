---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129232"
---
Om uw apparaat te herstellen naar de fabrieksinstellingen, moet u alle gegevens op de gegevensschijf en de opstartschijf veilig wissen. 

Gebruik de cmdlet `Reset-HcsAppliance` om zowel de gegevensschijven als de opstartschijf of alleen de gegevensschijven te wissen. Met de schakelopties `ClearData` en `BootDisk` kunt u kiezen om alleen de gegevensschijven of de opstartschijf te wissen.

Met de schakeloptie `BootDisk` wordt de opstartschijf gewist en wordt het apparaat onbruikbaar. Deze optie dient alleen te worden gebruikt wanneer het apparaat moet worden geretourneerd naar Microsoft. Raadpleeg voor meer informatie [Het apparaat terugsturen naar Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Als u het apparaat opnieuw instelt in de lokale webinterface, worden alleen de gegevensschijven veilig gewist, maar wordt de opstartschijf intact gehouden. De opstartschijf bevat de apparaatconfiguratie.

1. [Maak verbinding met de PowerShell-interface](#connect-to-the-powershell-interface).
2. Typ in de opdrachtprompt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    In het volgende voorbeeld ziet u hoe u deze cmdlet gebruikt:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
