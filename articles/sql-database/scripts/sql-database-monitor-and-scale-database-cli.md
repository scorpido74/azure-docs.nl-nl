---
title: CLI-voor beeld-monitor-Scale-één Azure-SQL database
description: Azure CLI-voorbeeldscript voor het controleren en schalen van één Azure SQL-database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 96a27d5bfc76c78688d6b6b2a02c988dde764248
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691603"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>CLI gebruiken voor het controleren en schalen van één Azure SQL-database

In dit Azure CLI-voorbeeldscript wordt één Azure SQL-database naar een andere rekenkracht geschaald nadat er een query is gestuurd over de grootte van de database.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Gebruik [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) voor een lijst met bewerkingen die worden uitgevoerd op de database en gebruik [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) om een update van de database te annuleren.

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
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql#az-sql-show-usage) | Toont de gebruikte grootte voor een individuele of pooldatabase. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Hiermee worden database-eigenschappen (zoals de servicelaag of de rekenkracht) bijgewerkt of wordt een database verplaatst naar, uit of tussen elastische pools. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
