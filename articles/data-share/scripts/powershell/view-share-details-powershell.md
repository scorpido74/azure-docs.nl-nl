---
title: 'Power shell-script: Bestaande shares in azure data share weer geven | Microsoft Docs'
description: In dit Power shell-script worden de details van shares weer gegeven.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 53b44441397be6aaa690c75bf362fd16d5a34019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242890"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Power shell gebruiken om de details van een verzonden gegevens share weer te geven

Met dit Power shell-script worden gegevens shares uit een bestaand account weer gegeven en worden de details van een specifieke share opgehaald.


## <a name="sample-script"></a>Voorbeeldscript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShare](/powershell/module/az.resources/get-az) | Hiermee wordt een lijst met shares in een account opgehaald en weer gegeven. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
