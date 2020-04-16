---
title: Azure Cosmos DB maken en beheren met Resource Manager-sjablonen
description: Azure Resource Manager-sjablonen gebruiken om Azure Cosmos DB voor SQL (Core) API te maken en te configureren
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390893"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Api-bronnen van Azure Cosmos DB SQL (Core) beheren met Azure Resource Manager-sjablonen

In dit artikel leert u hoe u Azure Resource Manager-sjablonen kunt gebruiken om het beheer van uw Azure Cosmos DB-accounts, -databases en -containers te automatiseren.

In dit artikel worden alleen voorbeelden van Azure Resource Manager-sjabloonvoorbeelden voor SQL API-accounts weergegeven. U ook sjabloonvoorbeelden vinden voor [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)en Tabel-API's. [Table](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Een Azure Cosmos-account, -database en -container maken

Met de volgende Azure Resource Manager-sjabloon wordt een Azure Cosmos-account gemaakt met:

* Twee containers die de doorvoer van 400 gevraagde eenheden per seconde (RU/s) delen op databaseniveau.
* Eén container met speciale 400 RU/s doorvoer.

Als u de Azure Cosmos DB-resources wilt maken, kopieert u de volgende voorbeeldsjabloon en implementeert u deze zoals beschreven, via [PowerShell](#deploy-via-powershell) of [Azure CLI](#deploy-via-azure-cli).

* Optioneel u de [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) bezoeken en de sjabloon implementeren vanuit de Azure-portal.
* U de sjabloon ook downloaden naar uw lokale computer of `--template-file` een nieuwe sjabloon maken en het lokale pad met de parameter opgeven.

> [!IMPORTANT]
>
> * Wanneer u locaties toevoegt of verwijdert aan een Azure Cosmos-account, u andere eigenschappen niet tegelijkertijd wijzigen. Deze bewerkingen moeten afzonderlijk worden uitgevoerd.
> * Accountnamen zijn beperkt tot 44 tekens, alle kleine letters.
> * Als u de doorvoerwaarden wilt wijzigen, dient u de sjabloon opnieuw in met bijgewerkte RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Als u een container met grote partitiesleutel wilt `"version":2` maken, `partitionKey` wijzigt u de vorige sjabloon om de eigenschap in het object op te nemen.

### <a name="deploy-via-powershell"></a>Implementeren via PowerShell

Ga als u PowerShell wilt gebruiken om de sjabloon Azure Resource Manager te implementeren:

1. **Kopieer** het script.
2. Selecteer **Probeer deze** om Azure Cloud Shell te openen.
3. Klik met de rechtermuisknop in het Azure Cloud Shell-venster en selecteer **Plakken**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

U ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van PowerShell in plaats van Azure Cloud Shell. U moet [de Azure PowerShell-module installeren.](/powershell/azure/install-az-ps) Voer `Get-Module -ListAvailable Az` uit om de vereiste versie te vinden.

### <a name="deploy-via-azure-cli"></a>Implementeren via Azure CLI

Ga als u Azure CLI wilt gebruiken om de sjabloon Azure Resource Manager te implementeren:

1. **Kopieer** het script.
2. Selecteer **Probeer deze** om Azure Cloud Shell te openen.
3. Klik met de rechtermuisknop in het Azure Cloud Shell-venster en selecteer **Plakken**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

De `az cosmosdb show` opdracht toont het nieuw gemaakte Azure Cosmos-account nadat het is ingericht. U ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van Azure CLI in plaats van Azure Cloud Shell. Zie het [CLI-artikel (Azure Command-Line Interface) voor](/cli/azure/) meer informatie.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Een Azure Cosmos DB-container maken met functionaliteit aan de serverzijde

U een Azure Resource Manager-sjabloon gebruiken om een Azure Cosmos DB-container te maken met een opgeslagen procedure, trigger en door de gebruiker gedefinieerde functie.

Kopieer de volgende voorbeeldsjabloon en implementeer deze zoals beschreven, met [PowerShell](#deploy-with-powershell) of [Azure CLI](#deploy-with-azure-cli).

* Optioneel u [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) bezoeken en de sjabloon implementeren vanuit de Azure-portal.
* U de sjabloon ook downloaden naar uw lokale computer of `--template-file` een nieuwe sjabloon maken en het lokale pad met de parameter opgeven.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Implementeren met PowerShell

Ga als u PowerShell wilt gebruiken om de sjabloon Azure Resource Manager te implementeren:

1. **Kopieer** het script.
1. Selecteer **Probeer deze** om Azure Cloud Shell te openen.
1. Klik met de rechtermuisknop op het Azure Cloud Shell-venster en selecteer **Plakken**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

U ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van PowerShell in plaats van Azure Cloud Shell. U moet [de Azure PowerShell-module installeren.](/powershell/azure/install-az-ps) Voer `Get-Module -ListAvailable Az` uit om de vereiste versie te vinden.

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

Ga als u Azure CLI wilt gebruiken om de sjabloon Azure Resource Manager te implementeren:

1. **Kopieer** het script.
2. Selecteer **Probeer deze** om Azure Cloud Shell te openen.
3. Klik met de rechtermuisknop in het Azure Cloud Shell-venster en selecteer **Plakken**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

* [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)
* [Azure Cosmos DB-resourceproviderschema](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Veelvoorkomende azure resourcemanager-implementatiefouten oplossen](../azure-resource-manager/templates/common-deployment-errors.md)
