---
title: 'CLI-voor beeld: een beheerd exemplaar maken in Azure SQL Database'
description: Azure CLI-voorbeeld script voor het maken van een beheerd exemplaar in Azure SQL Database
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067449"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>CLI gebruiken om een door Azure SQL Database beheerd exemplaar te maken

In dit voor beeld van Azure CLI-script wordt een Azure SQL Database beheerd exemplaar gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Er worden ook een route tabel en een netwerk beveiligings groep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Configure Azure VM to Connect to a Azure SQL database Managed instance](../sql-database-managed-instance-configure-vm.md) (Engelstalig) en [Configureer een punt-naar-site-verbinding met een Azure SQL database beheerde instantie van on-premises](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnements typen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [AZ Network vnet](/cli/azure/network/vnet) | Virtuele-netwerk opdrachten. |
| [AZ Network vnet subnet](/cli/azure/network/vnet/subnet) | Subnetten van het virtuele netwerk. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Opdrachten voor de netwerk route tabel. |
| [AZ SQL mi](/cli/azure/sql/mi) | Managed instance-opdrachten. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
