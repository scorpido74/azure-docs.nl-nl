---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79129232"
---
Om uw apparaat te herstellen naar de fabrieksinstellingen, moet u alle gegevens op de gegevensschijf en de opstartschijf veilig wissen. 

Gebruik de `Reset-HcsAppliance` cmdlet om zowel de gegevens schijven als de opstart schijf of alleen de gegevens schijven te wissen. `ClearData`Met de `BootDisk` Opties en kunt u de gegevens schijven en de opstart schijf respectievelijk wissen.

De `BootDisk` Switch wist de opstart schijf en maakt het apparaat onbruikbaar. Deze optie dient alleen te worden gebruikt wanneer het apparaat moet worden geretourneerd naar Microsoft. Zie [het apparaat terugsturen naar micro soft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)voor meer informatie.

Als u het apparaat opnieuw instelt in de lokale webinterface, worden alleen de gegevensschijven veilig gewist, maar wordt de opstartschijf intact gehouden. De opstartschijf bevat de apparaatconfiguratie.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Typ in de opdrachtprompt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    In het volgende voor beeld ziet u hoe u deze cmdlet gebruikt:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
