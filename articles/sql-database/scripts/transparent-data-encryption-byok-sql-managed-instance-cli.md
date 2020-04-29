---
title: CLI-voor beeld-BYOK TDE-Azure SQL Database beheerd exemplaar inschakelen
description: Meer informatie over het configureren van een Azure SQL Managed instance om te beginnen met het gebruik van BYOK Transparent Data Encryption (TDE) voor versleuteling-at-rest met behulp van Power shell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061738"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault

In dit voor beeld van Azure CLI-script wordt Transparent Data Encryption (TDE) met door de klant beheerde sleutel geconfigureerd voor Azure SQL Managed instance, met behulp van een sleutel van Azure Key Vault. Dit wordt vaak een Bring Your Own Key scenario genoemd voor TDE. Zie [TDE Bring your own Key naar Azure SQL](../transparent-data-encryption-byok-azure-sql.md)voor meer informatie over de TDe met door de klant beheerde sleutels.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="prerequisites"></a>Vereisten

Een bestaand beheerd exemplaar, Zie [Azure CLI gebruiken om een Azure SQL database beheerd exemplaar te maken](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeld verwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| | |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Data base-opdrachten. |
| [AZ SQL failover-Group](/cli/azure/sql/failover-group) | Failover-groeps opdrachten. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../sql-database-cli-samples.md).
