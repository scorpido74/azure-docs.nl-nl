---
title: Azure Resource Manager sjablonen voor Azure Cosmos DB Cassandra-API
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB Cassandra-API te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: f09ab6958eb768895a2d16f129354074cb6f00c8
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961872"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Cassandra-API resources beheren met behulp van Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, data bases en containers te automatiseren met behulp van Azure Resource Manager sjablonen. In dit artikel vindt u voor beelden voor SQL-API-accounts, om voor beelden te vinden voor andere typen API-accounts: gebruik Azure Resource Manager sjablonen met de API van Azure Cosmos DB voor [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDb](manage-mongodb-with-resource-manager.md), [tabel](manage-table-with-resource-manager.md) artikelen.

## Een Azure Cosmos-account,-schijf ruimte en-tabel maken<a id="create-resource"></a>

Azure Cosmos DB resources maken met behulp van een Azure Resource Manager sjabloon. Met deze sjabloon maakt u een Azure Cosmos-account voor Cassandra-API met twee tabellen die de door Voer van 400 RU/s op het niveau van de spatie delen. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

> [!NOTE]
> Account namen moeten kleine letters en 44 of minder tekens bevatten.
> Als u RU/s wilt bijwerken, moet u de sjabloon opnieuw verzenden met bijgewerkte waarden voor doorvoer eigenschappen.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-the-azure-cli"></a>Implementeren met de Azure CLI

Als u de Azure Resource Manager sjabloon wilt implementeren met behulp van de Azure CLI, **kopieert** u het script en selecteert u **proberen het** te openen Azure Cloud shell. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

De `az cosmosdb show` opdracht toont het zojuist gemaakte Azure Cosmos-account nadat het is ingericht. Als u ervoor kiest een lokaal geïnstalleerde versie van de Azure CLI te gebruiken in plaats van Cloud Shell, raadpleegt u het artikel over [Azure cli](/cli/azure/) .


## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Documentatie over Azure Resource Manager](/azure/azure-resource-manager/)
- [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/resource-manager-common-deployment-errors.md)
