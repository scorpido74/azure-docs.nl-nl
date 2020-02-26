---
title: Resource Manager-sjablonen voor de Azure Cosmos DB-API voor MongoDB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB-API voor MongoDB te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: eb3b0537b01c60e79959494c65306c4a56c331a3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587070"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB-API-resources beheren met Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, data bases en containers te automatiseren met behulp van Azure Resource Manager sjablonen. In dit artikel vindt u voor beelden van de Azure Cosmos DB-API voor MongoDB, om voor beelden te vinden voor andere typen API-accounts: gebruik Azure Resource Manager sjablonen met de API van Azure Cosmos DB voor [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [tabel](manage-table-with-resource-manager.md) artikelen.

## Azure Cosmos DB-API maken voor het MongoDB-account, de data base en de verzameling<a id="create-resource"></a>

Azure Cosmos DB resources maken met behulp van een Azure Resource Manager sjabloon. Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor MongoDB-API met twee verzamelingen die een door Voer van 400 RU/s op database niveau delen. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad opgeven met de para meter `--template-file`.

> [!NOTE]
> Account namen moeten kleine letters en 44 of minder tekens bevatten.
> Als u RU/s wilt bijwerken, moet u de sjabloon opnieuw verzenden met bijgewerkte waarden voor doorvoer eigenschappen.
>
> Op dit moment kunt u alleen 3,2-versie maken (dat wil zeggen, accounts die gebruikmaken van het eind punt in de indeling `*.documents.azure.com`) van de Azure Cosmos DB-API voor MongoDB-accounts met behulp van Power shell, CLI en Resource Manager-sjablonen. Als u 3,6-versie van accounts wilt maken, gebruikt u Azure Portal in plaats daarvan.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Implementeren via de Azure CLI

Als u de Azure Resource Manager sjabloon wilt implementeren met behulp van de Azure CLI, **kopieert** u het script en selecteert u **proberen het** te openen Azure Cloud shell. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

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

De `az cosmosdb show` opdracht toont het zojuist gemaakte Azure Cosmos-account nadat het is ingericht. Als u ervoor kiest een lokaal geïnstalleerde versie van de Azure CLI te gebruiken in plaats van Cloud Shell, raadpleegt u het artikel over [Azure cli](/cli/azure/) .

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Documentatie over Azure Resource Manager](/azure/azure-resource-manager/)
- [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/templates/common-deployment-errors.md)