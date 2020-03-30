---
title: Resources beheren - Azure PowerShell
description: Gebruik Azure PowerShell en Azure Resource Manager om uw resources te beheren. Hier ziet u hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485401"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure-resources beheren met Azure PowerShell

Meer informatie over het gebruik van Azure PowerShell met [Azure Resource Manager](overview.md) om uw Azure-bronnen te beheren. Zie [Azure-brongroepen beheren met Azure PowerShell](manage-resource-groups-powershell.md)voor het beheren van brongroepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met behulp van de Azure-portal](manage-resources-portal.md)
- [Azure-resources beheren met Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resourcegroep

U Azure-resources rechtstreeks implementeren met Azure PowerShell of een Resource Manager-sjabloon implementeren om Azure-resources te maken.

### <a name="deploy-a-resource"></a>Een resource implementeren

In het volgende script wordt een opslagaccount gemaakt.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Een sjabloon implementeren

Met het volgende script wordt een Quickstart-sjabloon geïmplementeerd om een opslagaccount te maken. Zie [Snelstart: Azure Resource Manager-sjablonen maken met Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)voor meer informatie.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../templates/deploy-powershell.md)voor meer informatie.

## <a name="deploy-a-resource-group-and-resources"></a>Een resourcegroep en resources implementeren

U een resourcegroep maken en resources implementeren in de groep. Zie [Resourcegroep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-group-and-resources)voor meer informatie.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Resources implementeren voor meerdere abonnementen of resourcegroepen

Doorgaans implementeert u alle bronnen in uw sjabloon in één resourcegroep. Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze in verschillende resourcegroepen of -abonnementen wilt plaatsen. Zie [Azure-resources implementeren voor meerdere abonnementen of resourcegroepen](../templates/cross-resource-group-deployment.md)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

In het volgende script ziet u hoe u een opslagaccount verwijdert.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="move-resources"></a>Resources verplaatsen

In het volgende script ziet u hoe u een opslagaccount van de ene resourcegroep naar een andere resourcegroep verwijdert.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

Het volgende script vergrendelt een opslagaccount, zodat het account niet kan worden verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

In het volgende script worden alle vergrendelingen voor een opslagaccount opgeslagen:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

In het volgende script wordt een vergrendeling van een opslagaccount verwijderd:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Tagging helpt bij het logisch organiseren van uw resourcegroep en resources. Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#powershell)voor meer informatie.

## <a name="manage-access-to-resources"></a>Toegang tot bronnen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met RBAC en Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .
