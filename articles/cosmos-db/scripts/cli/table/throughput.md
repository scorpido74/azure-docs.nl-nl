---
title: RU/s bijwerken voor een Table-API tabel voor Azure Cosmos DB
description: RU/s bijwerken voor een Table-API tabel voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 8cc04b766ba63fb522417310177a539ea04fcdd6
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275379"
---
# <a name="update-rus-for-a-table-api-table-for-azure-cosmos-db-azure-cli"></a>RU/s bijwerken voor een Table-API tabel voor Azure Cosmos DB Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een Table-API tabel en wordt vervolgens de door Voer bijgewerkt in de tabel.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Update RU/s for a Table API table.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Hiermee wordt een Azure Cosmos DB-account gemaakt. |
| [AZ cosmosdb Table Create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Hiermee maakt u een Azure Cosmos Table-API-tabel. |
| [AZ cosmosdb Table doorvoer update](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Update RU/s voor een Azure Cosmos Table-API-tabel. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
