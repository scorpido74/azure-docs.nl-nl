---
title: IN CLI-voorbeeld actieve Azure SQL-database met georeplicatie
description: Azure CLI-voorbeeldscript voor het instellen van actieve georeplicatie voor een gepoolde database in Azure SQL Database en mislukt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 2646ed98f4a73c69d339df0134e8a565c958c514
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067364"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>CLI gebruiken om actieve georeplicatie te configureren voor een gepoolde database in Azure SQL Database

In dit voorbeeld van het Azure CLI-script wordt actieve georeplicatie geconfigureerd voor een samengevoegde database in Azure SQL Database en wordt deze niet overgezet naar de secundaire replica van de database.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.sh "Set up active geo-replication for elastic pool")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
az group delete --name $secondaryResource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | Opdrachten voor elastische zwembaden |
| [az sql db replica](/cli/azure/sql/db/replica) | Opdrachten voor databasereplicatie. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
