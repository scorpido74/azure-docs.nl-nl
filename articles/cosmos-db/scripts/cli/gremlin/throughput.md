---
title: RU/s bijwerken voor een Gremlin-database en grafiek voor Azure Cosmos DB
description: RU/s bijwerken voor een Gremlin-database en grafiek voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275519"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>RU/s bijwerken voor een Gremlin-database en grafiek voor Azure Cosmos DB met Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit onderwerp de Azure CLI-versie 2.0.73 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

Dit script maakt een Gremlin-database met gedeelde doorvoer en een Gremlin-grafiek met speciale doorvoer en werkt vervolgens de doorvoer voor zowel de database als de grafiek bij.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

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
| [az cosmosdb gremlin database maken](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Hiermee maakt u een Azure Cosmos Gremlin-database. |
| [az cosmosdb gremlin grafiek te maken](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Hiermee maakt u een Azure Cosmos Gremlin-grafiek. |
| [az cosmosdb gremlin database throughput update](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Ru/s bijwerken voor een Azure Cosmos Gremlin-database. |
| [az cosmosdb gremlin grafiek doorvoer update](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Update RU/s voor een Azure Cosmos Gremlin-grafiek. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB CLI-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-scriptvoorbeelden zijn te vinden in de [Azure Cosmos DB CLI GitHub Repository.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
