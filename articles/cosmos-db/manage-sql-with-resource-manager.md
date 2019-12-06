---
title: Azure Cosmos DB maken en beheren met Azure Resource Manager sjablonen
description: Azure Resource Manager sjablonen gebruiken om Azure Cosmos DB te maken en te configureren voor de SQL-API (core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 62c04fed03ad2346d0f548a4a8028f2d7d6b3486
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850462"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Azure Cosmos DB SQL-API-resources (core) beheren met Azure Resource Manager sjablonen

In dit artikel leert u hoe u Azure Resource Manager sjablonen kunt gebruiken om het beheer van uw Azure Cosmos DB accounts, data bases en containers te automatiseren.

In dit artikel worden alleen Azure Resource Manager sjabloon voorbeelden voor SQL-API-accounts weer gegeven. U kunt ook voor beelden van sjablonen vinden voor [Cassandra](manage-cassandra-with-resource-manager.md)-, [Gremlin](manage-gremlin-with-resource-manager.md)-, [MongoDb](manage-mongodb-with-resource-manager.md)-en [Table](manage-table-with-resource-manager.md) -api's.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Een Azure Cosmos-account,-data base en-container maken

Met de volgende Azure Resource Manager sjabloon maakt u een Azure Cosmos-account met:

* Twee containers die de door Voer van 400 aangevraagde eenheden per seconde (RU/s) delen op database niveau.
* Eén container met toegewezen 400 RU/s-door voer.

Als u de Azure Cosmos DB resources wilt maken, kopieert u de volgende voorbeeld sjabloon en implementeert u deze zoals beschreven, via [Power shell](#deploy-via-powershell) of [Azure cli](#deploy-via-azure-cli).

* U kunt ook de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) start bezoeken en de sjabloon implementeren vanuit de Azure Portal.
* U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

> [!IMPORTANT]
>
> * Wanneer u locaties toevoegt aan of verwijdert uit een Azure Cosmos-account, kunt u niet tegelijkertijd andere eigenschappen wijzigen. Deze bewerkingen moeten afzonderlijk worden uitgevoerd.
> * Account namen zijn beperkt tot 44 tekens, alle kleine letters.
> * Als u de doorvoer waarden wilt wijzigen, dient u de sjabloon opnieuw in met de bijgewerkte RU/s.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Als u een container met een grote partitie sleutel wilt maken, wijzigt u de vorige sjabloon om de eigenschap `"version":2` op te nemen in het `partitionKey`-object.

### <a name="deploy-via-powershell"></a>Implementeren via Power shell

Als u Power shell wilt gebruiken om de Azure Resource Manager sjabloon te implementeren:

1. **Kopieer** het script.
2. Selecteer **proberen** om Azure Cloud shell te openen.
3. Klik met de rechter muisknop in het Azure Cloud Shell venster en selecteer vervolgens **Plakken**.

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

U kunt ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van Power shell in plaats van Azure Cloud Shell. U moet [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Get-Module -ListAvailable Az` uit om de vereiste versie te vinden.

### <a name="deploy-via-azure-cli"></a>Implementeren via Azure CLI

Als u Azure CLI wilt gebruiken om de Azure Resource Manager-sjabloon te implementeren:

1. **Kopieer** het script.
2. Selecteer **proberen** om Azure Cloud shell te openen.
3. Klik met de rechter muisknop in het Azure Cloud Shell venster en selecteer vervolgens **Plakken**.

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

De `az cosmosdb show` opdracht toont het zojuist gemaakte Azure Cosmos-account nadat het is ingericht. U kunt ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van Azure CLI in plaats Azure Cloud Shell. Zie het artikel over de [Azure-opdracht regel interface (CLI)](/cli/azure/) voor meer informatie.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Een Azure Cosmos DB-container maken met functionaliteit aan server zijde

U kunt een Azure Resource Manager sjabloon gebruiken om een Azure Cosmos DB container te maken met een opgeslagen procedure, trigger en door de gebruiker gedefinieerde functie.

Kopieer de volgende voorbeeld sjabloon en implementeer deze zoals beschreven, met [Power shell](#deploy-with-powershell) of [Azure cli](#deploy-with-azure-cli).

* U kunt ook de galerie van [Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) start bezoeken en de sjabloon implementeren vanuit de Azure Portal.
* U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Implementeren met PowerShell

Als u Power shell wilt gebruiken om de Azure Resource Manager sjabloon te implementeren:

1. **Kopieer** het script.
1. Selecteer **proberen** om Azure Cloud shell te openen.
1. Klik met de rechter muisknop op het Azure Cloud Shell venster en selecteer vervolgens **Plakken**.

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

U kunt ervoor kiezen om de sjabloon te implementeren met een lokaal geïnstalleerde versie van Power shell in plaats van Azure Cloud Shell. U moet [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Get-Module -ListAvailable Az` uit om de vereiste versie te vinden.

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

Als u Azure CLI wilt gebruiken om de Azure Resource Manager-sjabloon te implementeren:

1. **Kopieer** het script.
2. Selecteer **proberen** om Azure Cloud shell te openen.
3. Klik met de rechter muisknop in het Azure Cloud Shell venster en selecteer vervolgens **Plakken**.

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

* [Documentatie over Azure Resource Manager](/azure/azure-resource-manager/)
* [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/resource-manager-common-deployment-errors.md)
