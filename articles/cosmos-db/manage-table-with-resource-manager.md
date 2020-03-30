---
title: Resourcebeheersjablonen voor Azure Cosmos DB Table API
description: Gebruik Azure Resource Manager-sjablonen om Azure Cosmos DB Table API te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246706"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Table API-resources beheren met Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, databases en containers te automatiseren met Azure Resource Manager-sjablonen. In dit artikel vindt u alleen voorbeelden voor Tabel-API-accounts om voorbeelden te vinden voor andere API-typeaccounts: gebruik Azure Resource Manager-sjablonen met Azure Cosmos DB's API voor [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md), [SQL-artikelen.](manage-sql-with-resource-manager.md)

## <a name="create-azure-cosmos-account-and-table"></a>Azure Cosmos-account en -tabel maken<a id="create-resource"></a>

Azure Cosmos DB-resources maken met behulp van een Azure Resource Manager-sjabloon. Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor Tabel-API met één tabel met een doorvoersnelheid van 400 RU/s. Kopieer de sjabloon en implementeer zoals hieronder weergegeven of ga naar [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) en implementeer vanuit de Azure-portal. U de sjabloon ook downloaden naar uw lokale computer of `--template-file` een nieuwe sjabloon maken en het lokale pad met de parameter opgeven.

> [!NOTE]
> Accountnamen moeten kleine letters en 44 of minder tekens zijn.
> Als u RU/s wilt bijwerken, dient u de sjabloon opnieuw in met bijgewerkte eigenschapswaarden voor doorvoer.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Implementeren via PowerShell

Als u de resourcebeheersjabloon wilt implementeren met PowerShell, **kopieert u** het script en selecteert **u Proberen om** Azure Cloud Shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Als u ervoor kiest om een lokaal geïnstalleerde versie van PowerShell te gebruiken in plaats van vanuit de Azure Cloud-shell, moet u de Azure PowerShell-module [installeren.](/powershell/azure/install-az-ps) Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken.

### <a name="deploy-via-the-azure-cli"></a>Implementeren via de Azure CLI

Als u de azure resource manager-sjabloon wilt implementeren met de Azure CLI, **kopieert u** het script en selecteert **u Try it** om Azure Cloud Shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

De `az cosmosdb show` opdracht toont het nieuw gemaakte Azure Cosmos-account nadat het is ingericht. Zie het Azure [CLI-artikel](/cli/azure/) als u een lokaal geïnstalleerde versie van de Azure CLI wilt gebruiken in plaats van Cloud Shell te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)
- [Azure Cosmos DB-resourceproviderschema](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende azure resourcemanager-implementatiefouten oplossen](../azure-resource-manager/templates/common-deployment-errors.md)