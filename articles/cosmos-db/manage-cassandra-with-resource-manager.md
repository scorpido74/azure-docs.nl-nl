---
title: Resource Manager-sjablonen voor Azure Cosmos DB Cassandra API
description: Gebruik Azure Resource Manager-sjablonen om Azure Cosmos DB Cassandra API te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251893"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Cassandra API-resources beheren met Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, databases en containers te automatiseren met Azure Resource Manager-sjablonen. In dit artikel vindt u alleen voorbeelden voor SQL API-accounts om voorbeelden te vinden voor andere API-typeaccounts: gebruik Azure Resource Manager-sjablonen met Azure Cosmos DB's API voor [SQL](manage-sql-with-resource-manager.md), [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md), [Tabelartikelen.](manage-table-with-resource-manager.md)

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Azure Cosmos-account, sleutelruimte en tabel maken<a id="create-resource"></a>

Azure Cosmos DB-resources maken met behulp van een Azure Resource Manager-sjabloon. Deze sjabloon maakt een Azure Cosmos-account voor Cassandra API met twee tabellen die de doorvoer van 400 RU/s delen op keyspace-niveau. Kopieer de sjabloon en implementeer zoals hieronder weergegeven of ga naar [Azure Quickstart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) en implementeer vanuit de Azure-portal. U de sjabloon ook downloaden naar uw lokale computer of `--template-file` een nieuwe sjabloon maken en het lokale pad met de parameter opgeven.

> [!NOTE]
> Accountnamen moeten kleine letters en 44 of minder tekens zijn.
> Als u RU/s wilt bijwerken, dient u de sjabloon opnieuw in met bijgewerkte eigenschapswaarden voor doorvoer.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Implementeren met de Azure CLI

Als u de azure resource manager-sjabloon wilt implementeren met de Azure CLI, **kopieert u** het script en selecteert **u Try it** om Azure Cloud Shell te openen. Als u het script wilt plakken, klikt u met de rechtermuisknop op de shell en selecteert u **Plakken:**

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

De `az cosmosdb show` opdracht toont het nieuw gemaakte Azure Cosmos-account nadat het is ingericht. Zie het Azure [CLI-artikel](/cli/azure/) als u een lokaal geïnstalleerde versie van de Azure CLI wilt gebruiken in plaats van Cloud Shell te gebruiken.


## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)
- [Azure Cosmos DB-resourceproviderschema](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende azure resourcemanager-implementatiefouten oplossen](../azure-resource-manager/templates/common-deployment-errors.md)
