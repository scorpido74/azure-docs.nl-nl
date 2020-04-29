---
title: CLI-voor beeld-Failovergroep-Azure SQL Database beheerd exemplaar
description: Azure CLI-voorbeeld script voor het maken van een door Azure SQL Database beheerd exemplaar, het toevoegen aan een failovergroep en failover testen.
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
ms.openlocfilehash: 8ffe40662ffaf8a1fb35a3d31acfaea78ea0fbeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061922"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>CLI gebruiken om een door Azure SQL Database beheerd exemplaar toe te voegen aan een failovergroep

Dit Azure CLI-voor beeld maakt twee beheerde instanties, voegt deze toe aan een failovergroep en test vervolgens de failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen. U moet de resource groep twee keer verwijderen. Als u de resource groep de eerste keer verwijdert, worden het beheerde exemplaar en de virtuele clusters verwijderd, maar wordt het `az group delete : Long running operation failed with status 'Conflict'.`fout bericht niet meer weer gegeven. Voer de opdracht AZ Group delete een tweede keer uit om eventuele rest resources en de resource groep te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [AZ Network vnet](/cli/azure/network/vnet) | Virtuele-netwerk opdrachten.  |
| [AZ Network vnet subnet](/cli/azure/network/vnet/subnet) | Subnetten van het virtuele netwerk. |
| [AZ Network NSG](/cli/azure/network/nsg) | Opdrachten voor netwerk beveiligings groepen. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Opdrachten van de route tabel. |
| [AZ SQL mi](/cli/azure/sql/mi) | Managed instance-opdrachten. |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Open bare IP-adres opdrachten van het netwerk. |
| [AZ Network vnet-gateway](/cli/azure/network/vnet-gateway) | Virtual Network gateway-opdrachten. |
| [AZ SQL Instance-failover-Group](/cli/azure/sql/instance-failover-group) | Opdrachten voor failover-groepen van beheerde exemplaren. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
