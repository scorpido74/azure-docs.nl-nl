---
title: 'De Azure CLI: Een database aan een groep voor failover toevoegen'
description: Gebruik het Microsoft Azure CLI-voorbeeldscript om een database in Azure SQL Database te maken, deze toe te voegen aan een groep voor automatische failover en de failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: 4e102f0161a50fe6f65558fef77549b86c5c3eae
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742246"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>De Azure CLI gebruiken om een database aan een groep voor failover toe te voegen

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Met dit Azure CLI-scriptvoorbeeld maakt u een database in Azure SQL Database en een groep voor failover, voegt u de database hier aan toe en test u de failover.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Beschrijving |
|---|---|
| [az sql db](/cli/azure/sql/db) | Databaseopdrachten. |
| [az sql failovergroep](/cli/azure/sql/failover-group) | Opdrachten voor failovergroep. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../az-cli-script-samples-content-guide.md).
