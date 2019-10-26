---
title: CLI-voor beeld-een afzonderlijke data base aan een failovergroep toevoegen-Azure SQL Database
description: Azure CLI-voorbeeld script om een Azure SQL Database afzonderlijke Data Base te maken, deze toe te voegen aan een failovergroep en failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8e3c525230c3de530a93bd61a9227e9a4d7ed10b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933417"
---
# <a name="use-cli-to-add-an-azure-sql-database-single-database-into-a-failover-group"></a>CLI gebruiken om een Azure SQL Database afzonderlijke data base toe te voegen aan een failovergroep

In dit voor beeld van een Power shell-script maakt u één data base, maakt u een failovergroep, voegt u de data base toe en wordt de failover getest. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [AZ-account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. | 
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die individuele databases en elastische pools host. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewall regels van een server. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Hiermee maakt u een Data Base. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. | 
| [AZ SQL failover-Group List](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Geeft een lijst van de failover-groepen op een server. |
| [AZ SQL failover-groeps Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Stel de primaire groep van de failovergroep in door het uitvoeren van een failover voor alle data bases van de huidige primaire server. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
