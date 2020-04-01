---
title: Regio's toevoegen, failoverprioriteit wijzigen, failover activeren voor een Azure Cosmos-account
description: Regio's toevoegen, failoverprioriteit wijzigen, failover activeren voor een Azure Cosmos-account
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275547"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Regio's toevoegen, failoverprioriteit wijzigen, failover activeren voor een Azure Cosmos-account met Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit onderwerp de Azure CLI-versie 2.0.73 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

Dit script toont drie bewerkingen.

- Een regio toevoegen aan een bestaand Azure Cosmos-account.
- Regionale failoverprioriteit wijzigen (geldt voor accounts met automatische failover)
- Een handmatige failover activeren van primaire naar secundaire regio's (geldt voor accounts met handmatige failover)

> [!NOTE]
> Regiobewerkingen toevoegen en verwijderen op een Cosmos-account kunnen niet worden uitgevoerd terwijl andere eigenschappen worden gewijzigd.

> [!NOTE]
> Dit voorbeeld toont aan dat u een SQL (Core) API-account gebruikt, maar deze bewerkingen zijn identiek voor alle database-API's in Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Werkt een Azure Cosmos DB-account bij (regio toevoegen of verwijderen). |
| [az cosmosdb failover-prioriteit-verandering](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Update failover prioriteit of trigger failover op een Azure Cosmos DB-account. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB CLI-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-scriptvoorbeelden zijn te vinden in de [Azure Cosmos DB CLI GitHub Repository.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
