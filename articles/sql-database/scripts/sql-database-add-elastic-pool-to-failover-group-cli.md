---
title: CLI-voor beeld-Failovergroep Azure SQL Database elastische pool
description: Azure CLI-voorbeeld script om een Azure SQL Database elastische pool te maken, deze toe te voegen aan een failovergroep en failover te testen.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061942"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>CLI gebruiken om een Azure SQL Database elastische pool toe te voegen aan een failovergroep

In dit voor beeld van Azure CLI-script wordt één data base gemaakt, toegevoegd aan een elastische pool, een failovergroep gemaakt en failover getest.

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

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [AZ SQL Elastic-pool](/cli/azure/sql/elastic-pool) | Elastische pool-opdrachten. |
| [AZ SQL failover-Group](/cli/azure/sql/failover-group) | Failover-groeps opdrachten. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure/overview) voor meer informatie over de Azure CLI.

Aanvullende SQL Database voor beelden van Azure CLI-scripts vindt u in de [Azure SQL database Azure CLI-scripts](../sql-database-cli-samples.md).
