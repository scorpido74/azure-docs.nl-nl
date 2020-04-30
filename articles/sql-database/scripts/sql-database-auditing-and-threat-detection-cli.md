---
title: CLI-voor beeld van controle en geavanceerde beveiliging tegen bedreigingen-Azure SQL Database
description: Azure CLI-voorbeeld script voor het configureren van controle en geavanceerde beveiliging tegen bedreigingen in een Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: 9f15c4a8b0410003bccd9fb42aa2c34c42d9b56b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061891"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>CLI gebruiken voor het configureren van SQL Database controle en geavanceerde bedreigingen beveiliging

In dit voor beeld van Azure CLI-script worden SQL Database controle en geavanceerde beveiliging tegen bedreigingen geconfigureerd.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [AZ SQL DB audit-policy](/cli/azure/sql/db/audit-policy) | Hiermee wordt het controlebeleid voor een database ingesteld. |
| [AZ SQL DB Threat-Policy](/cli/azure/sql/db/threat-policy) | Hiermee stelt u een geavanceerd beveiligings beleid voor bedreigingen in voor een Data Base. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
