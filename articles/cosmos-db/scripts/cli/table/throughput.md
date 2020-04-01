---
title: RU/s bijwerken voor een tabel-API-tabel voor Azure Cosmos DB
description: RU/s bijwerken voor een tabel-API-tabel voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 8cc04b766ba63fb522417310177a539ea04fcdd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275379"
---
# <a name="update-rus-for-a-table-api-table-for-azure-cosmos-db-azure-cli"></a>RU/s bijwerken voor een tabel-API-tabel voor Azure Cosmos DB Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit onderwerp de Azure CLI-versie 2.0.73 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een tabel-API-tabel gemaakt en wordt de doorvoer van de tabel bijgewerkt.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Update RU/s for a Table API table.")]

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
| [az cosmosdb tabel te maken](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Hiermee maakt u een API-tabel voor Azure Cosmos Table. |
| [update az cosmosdb-tabeldoorvoer](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Ru/s bijwerken voor een Azure Cosmos Table API-tabel. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB CLI-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-scriptvoorbeelden zijn te vinden in de [Azure Cosmos DB CLI GitHub Repository.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
