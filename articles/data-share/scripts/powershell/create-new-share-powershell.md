---
title: 'PowerShell-script: een nieuwe Azure-gegevensshare maken | Microsoft Documenten'
description: Met dit PowerShell-script wordt een nieuw gegevensaandeel gemaakt binnen een bestaand Data Share-account.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307250"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell gebruiken om een gegevensshare in Azure te maken

Met dit PowerShell-script wordt een nieuwe gegevensshare gemaakt binnen een bestaand Data Share-account.

## <a name="sample-script"></a>Voorbeeldscript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Nieuw-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Hiermee maakt u een gegevensshare. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)
