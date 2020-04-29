---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129232"
---
Als u uw apparaat opnieuw wilt instellen, moet u alle gegevens op de gegevens schijf en de opstart schijf van uw apparaat veilig wissen. 

Gebruik de `Reset-HcsAppliance` cmdlet om zowel de gegevens schijven als de opstart schijf of alleen de gegevens schijven te wissen. Met `ClearData` de `BootDisk` opties en kunt u de gegevens schijven en de opstart schijf respectievelijk wissen.

De `BootDisk` switch wist de opstart schijf en maakt het apparaat onbruikbaar. Deze moet alleen worden gebruikt wanneer het apparaat moet worden geretourneerd naar micro soft. Zie [het apparaat terugsturen naar micro soft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)voor meer informatie.

Als u het opnieuw instellen van het apparaat in de lokale webgebruikersinterface gebruikt, worden alleen de gegevens schijven veilig gewist, maar wordt de opstart schijf intact bewaard. De opstart schijf bevat de apparaatconfiguratie.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Typ het volgende na de opdrachtprompt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    In het volgende voor beeld ziet u hoe u deze cmdlet gebruikt:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
