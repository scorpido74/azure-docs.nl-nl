---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667856"
---
## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Resources opschonen met de Azure-portal

Verwijder de resources in de Azure-portal door de stappen te volgen bij [het opschonen van resources.](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)

### <a name="clean-up-resources-using-powershell"></a>Resources opschonen met PowerShell

Als de Cloud Shell nog steeds open is, hoeft u de eerste regel (Read-Host) niet te kopiëren/uitvoeren.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```