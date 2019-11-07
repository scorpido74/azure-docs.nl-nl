---
title: CLI-voor beeld-Azure SQL database-groep voor elastische SQL-groepen verplaatsen
description: Voorbeeld van Azure CLI-script voor het verplaatsen van een SQL-database naar een elastische SQL-groep
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: d974685040d2a5759ca7719e82be02a7ec8950c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691591"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Een Azure SQL-database verplaatsen naar een elastische SQL-groep met de CLI

In dit voorbeeld van een Azure CLI-script worden twee elastische groepen of pools gemaakt, waarna een Azure SQL-database van de ene elastische SQL-groep naar een andere elastische SQL-groep wordt verplaatst. Vervolgens wordt de database vanuit de elastische groep verplaatst naar een bepaalde categorie rekenkracht voor individuele databases.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Hiermee maakt u een elastische pool. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Hiermee maakt u een individuele database of een andere database in een elastische pool. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Hiermee worden database-eigenschappen bijgewerkt of worden databasegegevens verplaatst naar, uit of tussen elastische pools. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
