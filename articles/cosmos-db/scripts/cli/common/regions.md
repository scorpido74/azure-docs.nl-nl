---
title: Regio's toevoegen, prioriteit van failover wijzigen, failover activeren voor een Azure Cosmos-account
description: Regio's toevoegen, prioriteit van failover wijzigen, failover activeren voor een Azure Cosmos-account
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71275547"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Regio's toevoegen, failover-prioriteit wijzigen, failover activeren voor een Azure Cosmos-account met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0.73 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

In dit script worden drie bewerkingen gedemonstreerd.

- Voeg een regio toe aan een bestaand Azure Cosmos-account.
- De prioriteit van de regionale failover wijzigen (is van toepassing op accounts met automatische failover)
- Een hand matige failover activeren vanuit primaire naar secundaire regio's (van toepassing op accounts met hand matige failover)

> [!NOTE]
> Het toevoegen en verwijderen van regio bewerkingen voor een Cosmos-account kan niet worden uitgevoerd terwijl andere eigenschappen worden gewijzigd.

> [!NOTE]
> Dit voor beeld laat zien hoe u een SQL (core) API-account gebruikt, maar deze bewerkingen zijn identiek voor alle data base-Api's in Cosmos DB.

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Hiermee werkt u een Azure Cosmos DB-account (regio toevoegen of verwijderen) bij. |
| [AZ cosmosdb failover-Priority-Change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Failover-prioriteit bijwerken of failover activeren voor een Azure Cosmos DB-account. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Cosmos DB cli-documentatie](/cli/azure/cosmosdb)voor meer informatie over de Azure Cosmos DB cli.

Alle Azure Cosmos DB CLI-voorbeeld scripts vindt u in de [Azure Cosmos DB cli github-opslag plaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
