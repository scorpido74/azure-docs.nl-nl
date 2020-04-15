---
title: Access audit logs - Azure CLI - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de controlelogboeken in Azure Database voor MySQL configureert en opent vanuit azure cli.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384166"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Controlelogboeken configureren en openen in azure cli

U de [Azure Database for MySQL-controlelogboeken](concepts-audit-logs.md) configureren vanuit de Azure CLI.

> [!IMPORTANT]
> De functionaliteit van het controlelogboek is momenteel in preview.

## <a name="prerequisites"></a>Vereisten

Om deze handleiding door te nemen, hebt u het volgende nodig:

- [Azure-database voor MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Deze handleiding vereist dat u Azure CLI-versie 2.0 of hoger gebruikt. Als u de versie wilt bevestigen, `az --version`voert u bij de opdrachtprompt azure CLI de opdrachtprompt in. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Controlelogboekregistratie configureren

Controlelogboekregistratie inschakelen en configureren met de volgende stappen:

1. Schakel controlelogboeken in door de **parameter audit_logs_enabled** in te stellen op 'AAN'. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecteer de [gebeurtenistypen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **parameter audit_log_events** bij te werken.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Voeg mysql-gebruikers toe die moeten worden uitgesloten van logboekregistratie door de **parameter audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikersnaam op te geven.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Voeg specifieke MySQL-gebruikers toe die moeten worden opgenomen voor logboekregistratie door de **parameter audit_log_include_users** bij te werken. Geef gebruikers op door hun MySQL-gebruikersnaam op te geven.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [controlelogboeken](concepts-audit-logs.md) in Azure Database voor MySQL
- Meer informatie over het configureren van controlelogboeken in de [Azure-portal](howto-configure-audit-logs-portal.md)