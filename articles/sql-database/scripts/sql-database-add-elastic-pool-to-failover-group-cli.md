---
title: CLI-voorbeeld- Failovergroep - Elastische pool azure SQL Database
description: Azure CLI-voorbeeldscript om een elastische groep azure SQL Database te maken, deze toe te voegen aan een failovergroep en failover te testen.
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
ms.openlocfilehash: 2d6f18e373327b758e766dffba341c080622301f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061942"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Cli gebruiken om een elastische groep van Azure SQL Database toe te voegen aan een failovergroep

Met dit voorbeeld van het Azure CLI-script wordt één database gemaakt, wordt deze toegevoegd aan een elastische groep, wordt een failovergroep gemaakt en wordt failover getest.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | Elastische poolopdrachten. |
| [az sql failover-group](/cli/azure/sql/failover-group) | Opdrachten voor failovergroepen. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure/overview) voor meer informatie over de Azure CLI.

Aanvullende SQL Database Azure CLI-scriptvoorbeelden zijn te vinden in de [Azure SQL Database Azure CLI-scripts.](../sql-database-cli-samples.md)
