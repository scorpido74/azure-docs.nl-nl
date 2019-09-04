---
title: 'Power shell-script: Het gebruik van een Azure-gegevens share bewaken | Microsoft Docs'
description: Met dit Power shell-script worden de metrische gegevens over het gebruik van een gezonden data-share opgehaald.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9cc7cbe8a1f523a50c91125802804e564b48a164
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242955"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Power shell gebruiken voor het bewaken van het gebruik van een verzonden gegevens share

Met dit Power shell-script wordt het gegevens gebruik bewaakt door de synchronisaties van een verzonden gegevens share weer te geven en de details van een specifieke synchronisatie op te halen.

## <a name="sample-script"></a>Voorbeeldscript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Synchronisaties op een share weer geven. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Hiermee worden de synchronisatie gegevens van een synchronisatie van shares opgehaald. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
