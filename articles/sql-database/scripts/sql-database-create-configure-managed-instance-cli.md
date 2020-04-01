---
title: CLI-voorbeeld - een beheerde instantie maken in Azure SQL Database
description: Azure CLI-voorbeeldscript om een beheerde instantie in Azure SQL-database te maken
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067449"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>CLI gebruiken om een door Azure SQL Database beheerde instantie te maken

Met dit voorbeeld van Azure CLI-script wordt een Azure SQL Database-exemplaar gemaakt in een speciaal subnet binnen een nieuw virtueel netwerk. Het configureert ook een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk. Zodra het script is uitgevoerd, kan de beheerde instantie worden geopend vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een Azure SQL Database Managed Instance](../sql-database-managed-instance-configure-vm.md) en [Configureer een point-to-site verbinding met een Azure SQL Database Managed Instance vanuit on-premises](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie ondersteunde [regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [az-netwerk vnet](/cli/azure/network/vnet) | Virtuele netwerkopdrachten. |
| [az-netwerk vnet subnet](/cli/azure/network/vnet/subnet) | Virtuele netwerksubnetopdrachten. |
| [az-netwerk route-tabel](/cli/azure/network/route-table) | Opdrachten voor netwerkroutetabel. |
| [az sql mi](/cli/azure/sql/mi) | Beheerde instantieopdrachten. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
