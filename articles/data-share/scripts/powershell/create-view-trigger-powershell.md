---
title: 'PowerShell-script: een Azure Data Share-momentopnamemaken en weergeven| Microsoft Documenten'
description: Dit PowerShell-script maakt en krijgt share snapshot triggers.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4f3148a4d5bd0d39ccfcf7e92e80300a7e19effa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307219"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell gebruiken om het gebruik van een verzonden gegevensaandeel te controleren

Dit PowerShell-script maakt en krijgt share snapshot triggers.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Nieuwe AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | Maak een trigger voor momentopnamen voor delen. |
| [Get-AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Hier worden synchronisatie-instellingen van een sharesynchronisatie opdegewenste status krijgen. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)
