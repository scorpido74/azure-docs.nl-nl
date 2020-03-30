---
title: 'PowerShell-script: nieuwe Azure Data Share-account maken | Microsoft Documenten'
description: Met dit PowerShell-script wordt een nieuw Data Share-account gemaakt.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307270"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>PowerShell gebruiken om een account voor gegevensshare te maken in Azure

Met dit PowerShell-script wordt een nieuw Data Share-account gemaakt. 

## <a name="sample-script"></a>Voorbeeldscript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Hiermee maakt u een account voor gegevensshare. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)
