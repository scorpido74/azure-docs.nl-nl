---
title: 'CLI-voor beeld: back-up-Azure SQL database'
description: Azure CLI-voorbeeld script voor het maken van een back-up van een enkele data base van Azure SQL naar een Azure storage-container
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: 68af42bd83565243dad5d48f227fa5cef3115cf6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061863"
---
# <a name="use-cli-to-backup-an-azure-sql-single-database-to-an-azure-storage-container"></a>CLI gebruiken om een back-up te maken van een enkele Azure SQL-Data Base naar een Azure storage-container

Dit Azure CLI-voor beeld maakt een back-up van een Azure-SQL database naar een Azure storage-container.  

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/backup-database/backup-database.sh "Restore SQL Database")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az sql server](/cli/azure/sql/server) | Server opdrachten. |
| [AZ SQL DB](/cli/azure/sql/db) | Data base-opdrachten. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
