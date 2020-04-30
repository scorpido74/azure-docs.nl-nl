---
title: Resource Manager-sjablonen voor Azure Cosmos DB Gremlin-API
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB Gremlin-API te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 77390d58412530208c2886a51460d3aab8114d27
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200765"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB Gremlin-API-resources beheren met Azure Resource Manager sjablonen

In dit artikel wordt beschreven hoe u verschillende bewerkingen uitvoert om het beheer van uw Azure Cosmos DB-accounts, data bases en containers te automatiseren met behulp van Azure Resource Manager sjablonen. In dit artikel vindt u voor beelden van Gremlin-API-accounts, om voor beelden te vinden voor andere typen API-accounts: gebruik Azure Resource Manager sjablonen met de API van Azure Cosmos DB voor [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDb](manage-mongodb-with-resource-manager.md), [tabel](manage-table-with-resource-manager.md) artikelen.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Azure Cosmos DB-API maken voor het MongoDB-account, de data base en de verzameling<a id="create-resource"></a>

Azure Cosmos DB resources maken met behulp van een Azure Resource Manager sjabloon. Met deze sjabloon wordt een Azure Cosmos-account voor Gremlin-API gemaakt met een Data Base en grafiek met een door Voer van 400 RU/s. Kopieer de sjabloon en implementeer deze zoals hieronder wordt weer gegeven of ga naar de [Galerie van Azure Quick](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) start en implementeer deze vanuit de Azure Portal. U kunt de sjabloon ook downloaden naar uw lokale computer of een nieuwe sjabloon maken en het lokale pad met de `--template-file` para meter opgeven.

> [!NOTE]
> Account namen moeten kleine letters en 44 of minder tekens bevatten.
> Als u RU/s wilt bijwerken, implementeert u de sjabloon opnieuw met bijgewerkte waarden voor doorvoer eigenschappen.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
       "accountName": {
           "type": "string",
           "defaultValue": "",
           "metadata": {
               "description": "Cosmos DB account name"
           }
       },
       "location": {
           "type": "string",
           "defaultValue": "[resourceGroup().location]",
           "metadata": {
               "description": "Location for the Cosmos DB account."
           }
       },
       "primaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The primary replica region for the Cosmos DB account."
           }
       },
       "secondaryRegion": {
           "type": "string",
           "metadata": {
               "description": "The secondary replica region for the Cosmos DB account."
           }
       },
       "defaultConsistencyLevel": {
           "type": "string",
           "defaultValue": "Session",
           "allowedValues": [
               "Eventual",
               "ConsistentPrefix",
               "Session",
               "BoundedStaleness",
               "Strong"
           ],
           "metadata": {
               "description": "The default consistency level of the Cosmos DB account."
           }
       },
       "maxStalenessPrefix": {
           "type": "int",
           "defaultValue": 100000,
           "minValue": 10,
           "maxValue": 1000000,
           "metadata": {
               "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
           }
       },
       "maxIntervalInSeconds": {
           "type": "int",
           "defaultValue": 300,
           "minValue": 5,
           "maxValue": 86400,
           "metadata": {
               "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
           }
       },
       "automaticFailover": {
           "type": "bool",
           "defaultValue": true,
           "allowedValues": [
               true,
               false
           ],
           "metadata": {
               "description": "Enable automatic failover for regions"
           }
       },
       "databaseName": {
           "type": "string",
           "defaultValue": "database1",
           "metadata": {
               "description": "The name for the Gremlin database"
           }
       },
       "graphName": {
           "type": "string",
           "defaultValue": "graph1",
           "metadata": {
               "description": "The name for the Gremlin graph"
           }
       },
       "throughput": {
           "type": "int",
           "defaultValue": 400,
           "minValue": 400,
           "maxValue": 1000000,
           "metadata": {
               "description": "Throughput for the Gremlin graph"
           }
       }
   },
   "variables": {
       "accountName": "[toLower(parameters('accountName'))]",
       "consistencyPolicy": {
           "Eventual": {
               "defaultConsistencyLevel": "Eventual"
           },
           "ConsistentPrefix": {
               "defaultConsistencyLevel": "ConsistentPrefix"
           },
           "Session": {
               "defaultConsistencyLevel": "Session"
           },
           "BoundedStaleness": {
               "defaultConsistencyLevel": "BoundedStaleness",
               "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
               "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
           },
           "Strong": {
               "defaultConsistencyLevel": "Strong"
           }
       },
       "locations": [
           {
               "locationName": "[parameters('primaryRegion')]",
               "failoverPriority": 0,
               "isZoneRedundant": false
           },
           {
               "locationName": "[parameters('secondaryRegion')]",
               "failoverPriority": 1,
               "isZoneRedundant": false
           }
       ]
   },
   "resources": [
       {
           "type": "Microsoft.DocumentDB/databaseAccounts",
           "name": "[variables('accountName')]",
           "apiVersion": "2020-03-01",
           "location": "[parameters('location')]",
           "kind": "GlobalDocumentDB",
           "properties": {
               "capabilities": [
                   {
                       "name": "EnableGremlin"
                   }
               ],
               "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
               "locations": "[variables('locations')]",
               "databaseAccountOfferType": "Standard",
               "enableAutomaticFailover": "[parameters('automaticFailover')]"
           }
       },
       {
           "type": "Microsoft.DocumentDB/databaseAccounts/gremlinDatabases",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('databaseName')]"
               }
           }
       },
       {
           "type": "Microsoft.DocumentDb/databaseAccounts/gremlinDatabases/graphs",
           "name": "[concat(variables('accountName'), '/', parameters('databaseName'), '/', parameters('graphName'))]",
           "apiVersion": "2020-03-01",
           "dependsOn": [
               "[resourceId('Microsoft.DocumentDB/databaseAccounts/gremlinDatabases', variables('accountName'),  parameters('databaseName'))]"
           ],
           "properties": {
               "resource": {
                   "id": "[parameters('graphName')]",
                   "indexingPolicy": {
                       "indexingMode": "consistent",
                       "includedPaths": [
                           {
                               "path": "/*"
                           }
                       ],
                       "excludedPaths": [
                           {
                               "path": "/myPathToNotIndex/*"
                           }
                       ]
                   },
                   "partitionKey": {
                       "paths": [
                           "/myPartitionKey"
                       ],
                       "kind": "Hash"
                   },
                   "options": {
                       "throughput": "[parameters('throughput')]"
                   }
               }
           }
       }
   ]
}
```

## <a name="deploy-with-the-azure-cli"></a>Implementeren met de Azure CLI

Als u de Azure Resource Manager sjabloon wilt implementeren met behulp van de Azure CLI, **kopieert** u het script en selecteert u **proberen het** te openen Azure Cloud shell. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graphName=$graphName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

De `az cosmosdb show` opdracht toont het zojuist gemaakte Azure Cosmos-account nadat het is ingericht. Als u ervoor kiest een lokaal geïnstalleerde versie van de Azure CLI te gebruiken in plaats van Cloud Shell, raadpleegt u het artikel over [Azure cli](/cli/azure/) .

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

- [Documentatie over Azure Resource Manager](/azure/azure-resource-manager/)
- [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/templates/common-deployment-errors.md)