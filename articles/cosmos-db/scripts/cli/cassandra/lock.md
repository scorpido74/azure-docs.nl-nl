---
title: Een resourcevergrendeling maken voor een-Cassandra keyspace en -tabel voor Azure Cosmos DB
description: Een resourcevergrendeling maken voor een-Cassandra keyspace en -tabel voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: 5683eece3f6dc1c63be27ce3c98664e972b8d7c6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086741"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Een resourcevergrendeling maken voor een Azure Cosmos Cassandra-API-keyspace en -tabel met behulp van Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.6.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Resourcevergrendeling werkt niet voor wijzigingen die zijn aangebracht door gebruikers die verbinding maken met behulp van een Cassandra-SDK, CQL-shell of Azure Portal, tenzij het Cosmos DB-account eerst is vergrendeld met de eigenschap `disableKeyBasedMetadataWriteAccess` ingeschakeld. Zie [Wijzigingen aan SDK's voorkomen](../../../role-based-access-control.md#prevent-sdk-changes) voor meer informatie over het inschakelen van deze eigenschap.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Hiermee wordt een vergrendeling gemaakt. |
| [az lock list](/cli/azure/lock#az-lock-list) | Hiermee worden vergrendelingsgegevens weergegeven. |
| [az lock show](/cli/azure/lock#az-lock-show) | Hiermee worden eigenschappen van een vergrendeling weergegeven. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Hiermee wordt een vergrendeling verwijderd. |

## <a name="next-steps"></a>Volgende stappen

-[Resources vergrendelen om onverwachte wijzigingen te voorkomen](../../../../azure-resource-manager/management/lock-resources.md)

-[Documentatie voor Azure Cosmos DB CLI](/cli/azure/cosmosdb).

-[Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
