---
title: CLI-voor beeld-een Azure-SQL database maken
description: Gebruik dit Azure CLI-voorbeeldscript voor het maken van een SQL-database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: aa13e5f245a34387eee5fae48c896163cc2f7c4a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691700"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>CLI gebruiken om één Azure SQL-database te maken en een firewallregel te configureren

Met dit Azure CLI-voorbeeldscript wordt een Azure SQL database gemaakt en wordt een firewallregel op serverniveau geconfigureerd. Nadat het script is uitgevoerd, is de SQL Database toegankelijk via alle Azure-services en het geconfigureerde IP-adres.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een SQL Database-server die een individuele database of elastische pool host. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Hiermee wordt een firewall-regel gemaakt om toegang te verlenen tot alle individuele databases en elastische pools op de SQL Database-server vanuit het ingevoerde IP-adresbereik. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | Maakt een individuele database of elastische pool. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle ingesloten resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
