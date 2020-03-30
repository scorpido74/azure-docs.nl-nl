---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129232"
---
Als u uw apparaat opnieuw wilt instellen, moet u alle gegevens op de gegevensschijf en de opstartschijf van uw apparaat veilig wissen. 

Gebruik `Reset-HcsAppliance` de cmdlet om zowel de gegevensschijven als de opstartschijf of alleen de gegevensschijven uit te roeien. Met `ClearData` `BootDisk` de schakelaars u respectievelijk de gegevensschijven en de opstartschijf wissen.

De `BootDisk` schakelaar veegt de opstartschijf en maakt het apparaat onbruikbaar. Het mag alleen worden gebruikt wanneer het apparaat moet worden teruggestuurd naar Microsoft. Zie [Het apparaat retourneren naar Microsoft voor](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)meer informatie.

Als u de apparaatreset gebruikt in de lokale webgebruikersinterface, worden alleen de gegevensschijven veilig gewist, maar blijft de opstartschijf intact. De opstartschijf bevat de apparaatconfiguratie.

1. [Maak verbinding met de PowerShell-interface](#connect-to-the-powershell-interface).
2. Typ het volgende na de opdrachtprompt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    In het volgende voorbeeld ziet u hoe u deze cmdlet gebruikt:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
