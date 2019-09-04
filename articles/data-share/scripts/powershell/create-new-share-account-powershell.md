---
title: 'Power shell-script: Nieuw Azure data share-account maken | Microsoft Docs'
description: Met dit Power shell-script maakt u een nieuw gegevens share-account.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: cc4e157856a05290da82aca686e2268a5ed2b3ce
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243020"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Power shell gebruiken voor het maken van een gegevens share-account in azure

Met dit Power shell-script maakt u een nieuw gegevens share-account. 

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
| [New-AzDataShareAccount](/powershell/module/az.resources/new-azdatashareaccount) | Hiermee maakt u een gegevens share-account. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
