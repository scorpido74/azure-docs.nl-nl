---
title: In het CLI-voor beeld wordt een elastische SQL-pool geschaald-Azure SQL Database
description: Voorbeeld van Azure CLI-script voor het schalen van een elastische pool naar Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 0494ab163e7fb7e8ea93cf255837bfda2d7b1570
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691544"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Een elastische pool schalen naar Azure SQL Database met CLI

In dit voorbeeld van een Azure CLI-script worden elastische pools gemaakt, pooldatabases verplaatst en rekenkracht van elastische SQL-pools gewijzigd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script maakt gebruik van de volgende opdrachten voor het maken van een resourcegroep, SQL Database-server, individuele database en SQL Database-firewallregels. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Hiermee maakt u een elastische pool. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Maakt een enkelvoudige of pooldatabase. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) | Hiermee wordt een elastische pool bijgewerkt, in dit geval door het wijzigen van het toegewezen aantal eDTU's. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
