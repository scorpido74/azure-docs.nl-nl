---
title: 'PowerShell-script: instellingen voor azure data share-instellingen instellen en weergeven| Microsoft Documenten'
description: Dit PowerShell-script stelt synchronisatie-instellingen voor delen in en krijgt deze.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b8d01a383e816bfc73413d21e9cc8d51d326ab14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307185"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell gebruiken om het gebruik van een verzonden gegevensaandeel te controleren

Dit PowerShell-script stelt synchronisatie-instellingen voor delen in en krijgt deze.

## <a name="sample-script"></a>Voorbeeldscript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | Maak een synchronisatie van delen. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Hier worden synchronisatie-instellingen van een sharesynchronisatie opdegewenste status krijgen. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)
