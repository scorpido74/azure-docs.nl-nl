---
title: RU/s bijwerken voor een Data Base en verzameling voor de MongoDB-API voor Azure Cosmos DB
description: RU/s bijwerken voor een Data Base en verzameling voor de MongoDB-API voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71275505"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>RU/s bijwerken voor een Data Base en verzameling voor de MongoDB-API voor Azure Cosmos DB met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een Data Base met een gedeelde door Voer en een verzameling met een speciale door Voer voor Azure Cosmos DB voor de MongoDB-API. vervolgens wordt de door Voer voor zowel de Data Base als de verzameling bijgewerkt.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Update RU/s for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Hiermee wordt een Azure Cosmos DB-account gemaakt. |
| [AZ cosmosdb MongoDb data base Create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Hiermee maakt u een Azure Cosmos MongoDB API-data base. |
| [AZ cosmosdb MongoDb-verzameling maken](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Hiermee maakt u een Azure Cosmos MongoDB API-verzameling. |
| [AZ cosmosdb MongoDb data base-doorvoer update](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Update RUs voor een Azure Cosmos MongoDB API-data base. |
| [Update AZ cosmosdb MongoDb verzameling door Voer](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Update RUs voor een Azure Cosmos MongoDB API-verzameling. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
