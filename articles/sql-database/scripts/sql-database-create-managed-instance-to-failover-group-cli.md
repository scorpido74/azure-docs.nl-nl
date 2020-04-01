---
title: CLI-voorbeeld- Failovergroep - Azure SQL Database-beheerde instantie
description: Azure CLI-voorbeeldscript om een azure SQL Database-beheerde instantie te maken, deze toe te voegen aan een failovergroep en failover te testen.
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
ms.openlocfilehash: 792746ec3bfcf429afb7919458b9ac7ec8446b60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061851"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>CLI gebruiken om een Azure SQL Database-beheerde instantie toe te voegen aan een failovergroep

In dit voorbeeld van Azure CLI worden twee beheerde exemplaren gemaakt, worden deze toegevoegd aan een failovergroep en wordt de failover van de primaire beheerde instantie naar de secundaire beheerde instantie getest.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Voorbeeldscripts

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen. U moet de brongroep twee keer verwijderen. Als u de resourcegroep de eerste keer verwijdert, worden de beheerde instantie en virtuele clusters verwijderd, maar wordt deze vervolgens mislukt met het foutbericht `az group delete : Long running operation failed with status 'Conflict'.`. Voer de opdracht verwijderen van de AZ-groep een tweede keer uit om resterende resources en de resourcegroep te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [az-netwerk vnet](/cli/azure/network/vnet) | Virtuele netwerkopdrachten.  |
| [az-netwerk vnet subnet](/cli/azure/network/vnet/subnet) | Virtuele netwerksubnetopdrachten. |
| [az netwerk nsg](/cli/azure/network/nsg) | Opdrachten voor netwerkbeveiligingsgroepen. |
| [az netwerk nsg regel](/cli/azure/network/nsg/rule)| Opdrachten voor netwerkbeveiligingsregels. |
| [az-netwerk route-tabel](/cli/azure/network/route-table) | Opdrachten voor routetabel. |
| [az sql mi](/cli/azure/sql/mi) | Beheerde instantieopdrachten. |
| [az-netwerk public-ip](/cli/azure/network/public-ip) | Opdrachten voor openbare IP-adressen in het netwerk. |
| [az-netwerk vnet-gateway](/cli/azure/network/vnet-gateway) | Opdrachten voor virtuele netwerkgateway |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Beheerde failovergroepopdrachten voor beheerde instantie. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
