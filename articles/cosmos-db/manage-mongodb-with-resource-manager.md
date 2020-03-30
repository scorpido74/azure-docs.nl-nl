---
title: Resource Manager-sjablonen voor Azure Cosmos DB API voor MongoDB
description: Gebruik Azure Resource Manager-sjablonen om Azure Cosmos DB API voor MongoDB te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063644"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB API-resources beheren met Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, databases en containers te automatiseren met Azure Resource Manager-sjablonen. In dit artikel vindt u alleen voorbeelden voor de API van Azure Cosmos DB voor MongoDB om voorbeelden te vinden voor andere API-typeaccounts: gebruik Azure Resource Manager-sjablonen met azure cosmos DB's API voor [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [Tabelartikelen.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Azure Cosmos DB API maken voor MongoDB-account, database en verzameling<a id="create-resource"></a>

Azure Cosmos DB-resources maken met behulp van een Azure Resource Manager-sjabloon. Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor de MongoDB API met twee verzamelingen die de doorvoer van 400 RU/s delen op databaseniveau. Kopieer de sjabloon en implementeer zoals hieronder weergegeven of ga naar [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) en implementeer vanuit de Azure-portal. U de sjabloon ook downloaden naar uw lokale computer of `--template-file` een nieuwe sjabloon maken en het lokale pad met de parameter opgeven.

> [!NOTE]
> Accountnamen moeten kleine letters en 44 of minder tekens zijn.
> Als u RU/s wilt bijwerken, dient u de sjabloon opnieuw in met bijgewerkte eigenschapswaarden voor doorvoer.
>
> Momenteel u alleen 3.2-versie maken (dat wil `*.documents.azure.com`zeggen accounts met behulp van het eindpunt in de indeling) van de API van Azure Cosmos DB voor MongoDB-accounts met PowerShell en CLI. Als u 3.6-versie van accounts wilt maken, gebruikt u in plaats daarvan Resource Manager-sjablonen (onder) of Azure-portal.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Implementeren via de Azure CLI

Als u de azure resource manager-sjabloon wilt implementeren met de Azure CLI, **kopieert u** het script en selecteert **u Try it** om Azure Cloud Shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

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

De `az cosmosdb show` opdracht toont het nieuw gemaakte Azure Cosmos-account nadat het is ingericht. Zie het Azure [CLI-artikel](/cli/azure/) als u een lokaal geïnstalleerde versie van de Azure CLI wilt gebruiken in plaats van Cloud Shell te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)
- [Azure Cosmos DB-resourceproviderschema](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende azure resourcemanager-implementatiefouten oplossen](../azure-resource-manager/templates/common-deployment-errors.md)
