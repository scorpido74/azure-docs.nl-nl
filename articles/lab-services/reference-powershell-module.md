---
title: Power shell-module voor Azure Lab Services | Microsoft Docs
description: Dit artikel bevat informatie over een Power shell-module die helpt bij het beheren van artefacten in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d9c184f3917be378eb77d1bf4096bfebb5ee1884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445590"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell-module (preview)
AZ. LabServices is een Power shell-module die het beheer van Azure Lab-Services vereenvoudigt. Het biedt samenstel bare functies voor het maken, doorzoeken, bijwerken en verwijderen van Lab-accounts, Labs, Vm's en installatie kopieën. Zie voor meer informatie over deze module de [Introductie pagina AZ. LabServices op github](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Deze module is beschikbaar als **Preview-versie**. 

## <a name="example-command"></a>Voorbeeldopdracht
Hier volgt een voor beeld van het gebruik van een Power shell-opdracht om alle actieve Vm's in alle Labs te stoppen.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Aan de slag
1. Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) als deze niet op uw computer aanwezig is. 
2. Down load [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) naar uw computer.
3. De module importeren:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Voer de volgende opdracht uit om alle Labs in uw abonnement weer te geven.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Volgende stappen
Zie de [Introductie pagina AZ. LabServices op github](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
