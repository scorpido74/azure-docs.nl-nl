---
title: Azure Resource Manager sjablonen voor Azure Cosmos DB-API voor MongoDB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB-API voor MongoDB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 92aaa42aeca294db62445a9434d0b5838a1f3855
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606539"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB-API-resources beheren met Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, data bases en containers te automatiseren met behulp van Azure Resource Manager sjablonen. In dit artikel vindt u voor beelden van de Azure Cosmos DB-API voor MongoDB, om voor beelden te vinden voor andere typen API-accounts: gebruik Resource Manager-sjablonen met de API van Azure Cosmos DB voor [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [tabel](manage-table-with-resource-manager.md) artikelen.

## Azure Cosmos DB-API maken voor het MongoDB-account, de data base en de verzameling<a id="create-resource"></a>

Azure Cosmos DB resources maken met behulp van een Azure Resource Manager sjabloon. Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor MongoDB-API met twee verzamelingen die een door Voer van 400 RU/s op database niveau delen. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

> [!NOTE]
> Account namen moeten kleine letters en < 31 tekens zijn.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Implementeren via Azure CLI

Als u de Resource Manager-sjabloon wilt implementeren met behulp van Azure CLI, **kopieert** u het script en selecteert u **proberen** de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

De `az cosmosdb show` opdracht toont het zojuist gemaakte Azure Cosmos-account nadat het is ingericht. Als u ervoor kiest een lokaal geïnstalleerde versie van Azure CLI te gebruiken in plaats van Cloud shell, raadpleegt u het artikel [Azure Command-Line Interface (CLI)](/cli/azure/) .

## De door Voer (RU/s) bijwerken voor een Data Base<a id="database-ru-update"></a>

Met de volgende sjabloon wordt de door Voer van een Data Base bijgewerkt. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Database sjabloon implementeren via Azure CLI

Als u de Resource Manager-sjabloon wilt implementeren met behulp van Azure CLI, selecteert u **proberen** de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## De door Voer (RU/s) bijwerken voor een verzameling<a id="collection-ru-update"></a>

Met de volgende sjabloon wordt de door Voer van een verzameling bijgewerkt. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Verzamelings sjabloon implementeren via Azure CLI

Als u de Resource Manager-sjabloon wilt implementeren met behulp van Azure CLI, selecteert u **proberen** de Azure Cloud shell te openen. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Documentatie over Azure Resource Manager](/azure/azure-resource-manager/)
- [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/resource-manager-common-deployment-errors.md)