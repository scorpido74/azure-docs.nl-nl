---
title: Resources beheren - Azure CLI
description: Gebruik Azure CLI en Azure Resource Manager om uw resources te beheren. Hier ziet u hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485531"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure-resources beheren met Azure CLI

Meer informatie over het gebruik van Azure CLI met [Azure Resource Manager](overview.md) om uw Azure-bronnen te beheren. Zie [Azure-brongroepen beheren met Azure CLI](manage-resource-groups-cli.md)voor het beheren van resourcegroepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met behulp van de Azure-portal](manage-resources-portal.md)
- [Azure-resources beheren met Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resourcegroep

U Azure-resources rechtstreeks implementeren met Azure CLI of een resourcebeheersjabloon implementeren om Azure-resources te maken.

### <a name="deploy-a-resource"></a>Een resource implementeren

In het volgende script wordt een opslagaccount gemaakt.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Een sjabloon implementeren

Met het volgende script wordt een Quickstart-sjabloon geïmplementeerd om een opslagaccount te maken. Zie [Snelstart: Azure Resource Manager-sjablonen maken met Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)voor meer informatie.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI voor](../templates/deploy-cli.md)meer informatie.

## <a name="deploy-a-resource-group-and-resources"></a>Een resourcegroep en resources implementeren

U een resourcegroep maken en resources implementeren in de groep. Zie [Resourcegroep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-group-and-resources)voor meer informatie.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Resources implementeren voor meerdere abonnementen of resourcegroepen

Doorgaans implementeert u alle bronnen in uw sjabloon in één resourcegroep. Er zijn echter scenario's waarin u een set resources samen wilt implementeren, maar deze in verschillende resourcegroepen of -abonnementen wilt plaatsen. Zie [Azure-resources implementeren voor meerdere abonnementen of resourcegroepen](../templates/cross-resource-group-deployment.md)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

In het volgende script ziet u hoe u een opslagaccount verwijdert.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="move-resources"></a>Resources verplaatsen

In het volgende script ziet u hoe u een opslagaccount van de ene resourcegroep naar een andere resourcegroep verwijdert.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

Het volgende script vergrendelt een opslagaccount, zodat het account niet kan worden verwijderd.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

In het volgende script worden alle vergrendelingen voor een opslagaccount opgeslagen:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

In het volgende script wordt een vergrendeling van een opslagaccount verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Tagging helpt bij het logisch organiseren van uw resourcegroep en resources. Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#azure-cli)voor meer informatie.

## <a name="manage-access-to-resources"></a>Toegang tot bronnen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met RBAC en Azure CLI](../../role-based-access-control/role-assignments-cli.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .
