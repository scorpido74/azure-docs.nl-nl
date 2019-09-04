---
title: 'Power shell-script: een nieuwe Azure-gegevens share maken | Microsoft Docs'
description: Met dit Power shell-script maakt u een nieuwe gegevens share binnen een bestaand gegevens share-account.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c76dfea83d0f736dbd48239cc3be3496a88ec8a2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242994"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Power shell gebruiken voor het maken van een gegevens share in azure

Met dit Power shell-script maakt u een nieuwe gegevens share binnen een bestaand gegevens share-account.

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
| [New-AzDataShare](/powershell/module/az.resources/new-azdatashare) | Hiermee maakt u een gegevens share. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
