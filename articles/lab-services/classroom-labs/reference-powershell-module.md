---
title: PowerShell-module voor Azure Lab Services | Microsoft Documenten
description: In dit artikel vindt u informatie over een PowerShell-module die helpt bij het beheren van artefacten in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609396"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell-module (preview)
Az.LabServices is een PowerShell-module die het beheer van Azure Lab-services vereenvoudigt. Het biedt composable functies voor het maken, opvragen, bijwerken en verwijderen van labaccounts, labs, VM's en afbeeldingen. Zie de [startpagina van Az.LabServices op GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)voor meer informatie over deze module.

> [!NOTE]
> Deze module is in **preview**. 

## <a name="example-command"></a>Voorbeeldopdracht
Hier is een voorbeeld van het gebruik van een PowerShell-opdracht om alle draaiende VM's in alle laboratoria te stoppen.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Aan de slag
1. Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) als deze niet op uw machine bestaat. 
2. Download [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) naar uw machine.
3. De module importeren:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Voer de volgende opdracht uit om alle labs in uw abonnement weer te geven.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Volgende stappen
Zie de [startpagina van Az.LabServices op GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)
