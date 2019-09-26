---
title: RU/s bijwerken voor een Cassandra-en-tabel voor Azure Cosmos DB
description: Bijwerken van RU/s voor het Cassandra van een spatie en tabel voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: fc635be68c87d29b21c41bb70aac4e3cbe34bcd3
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275414"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>RU/s bijwerken voor een Cassandra-opslag ruimte en-tabel met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="sample-script"></a>Voorbeeldscript

Met dit script wordt een Cassandra-opslag ruimte gemaakt met een gedeelde door Voer en een Cassandra-tabel met een toegewezen door Voer, en wordt vervolgens de door Voer voor de spatie en de tabel bijgewerkt.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [AZ cosmosdb Cassandra Keys Create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Hiermee maakt u een Azure Cosmos Cassandra-regel. |
| [AZ cosmosdb Cassandra-tabel Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Hiermee maakt u een Azure Cosmos Cassandra-tabel. |
| [AZ cosmosdb Cassandra-doorvoer update voor de opslag ruimte](/cli/azure/cosmosdb/cassandra/keyspace/throughputaz-cosmosdb-cassandra-keyspace-throughput-update) | Update RU/s voor een Azure Cosmos Cassandra. |
| [AZ cosmosdb Cassandra Table doorvoer update](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Update RU/s voor een Azure Cosmos Cassandra-tabel. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
