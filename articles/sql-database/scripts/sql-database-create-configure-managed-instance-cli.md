---
title: 'CLI-voorbeeld: een beheerd exemplaar maken in Azure SQL Database'
description: Azure CLI-voorbeeldscript voor het maken van een beheerd exemplaar in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772621"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>CLI gebruiken om een door Azure SQL Database beheerd exemplaar te maken

In dit voorbeeld van Azure CLI-script wordt een Azure SQL Database beheerd exemplaar gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Er wordt ook een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een Azure SQL Database beheerd exemplaar](../sql-database-managed-instance-configure-vm.md) en [On-premises een punt-naar-site-verbinding met een Azure SQL Database beheerd exemplaar configureren](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types) voor beperkingen.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Opdrachten virtueel netwerk. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Opdrachten virtueel netwerk subnet. |
| [az network routetabel](/cli/azure/network/route-table) | Opdrachten voor netwerkroutetabel. |
| [az sql mi](/cli/azure/sql/mi) | Opdrachten voor beheerd exemplaar. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
