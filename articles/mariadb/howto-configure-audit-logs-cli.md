---
title: Access-controle logboeken-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u de audit Logboeken in Azure Database for MariaDB kunt configureren en openen vanuit de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384192"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Controle logboeken configureren en openen in de Azure CLI

U kunt de [Azure database for MariaDB audit logboeken](concepts-audit-logs.md) configureren vanuit de Azure cli.

> [!IMPORTANT]
> De functionaliteit van het controle logboek is momenteel beschikbaar als preview-versie.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:

- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt `az --version`u bij de opdracht prompt van Azure cli. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

Schakel logboek registratie voor de controle in en configureer deze met de volgende stappen: 

1. Schakel audit Logboeken in door de para meter **audit_logs_enabled** in te stellen op on. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecteer de [gebeurtenis typen](concepts-audit-logs.md#configure-audit-logging) die moeten worden geregistreerd door de **audit_log_egitvents** -para meter bij te werken.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Voeg MariaDB-gebruikers toe die moeten worden uitgesloten van logboek registratie door de para meter **audit_log_exclude_users** bij te werken. Geef gebruikers op door hun MariaDB-gebruikers naam op te geven.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Voeg alle specifieke MariaDB-gebruikers toe die moeten worden opgenomen voor logboek registratie door de **audit_log_include_users** -para meter bij te werken. Geef gebruikers op door hun MariaDB-gebruikers naam op te geven.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [audit logboeken](concepts-audit-logs.md) in azure database for MariaDB
- Meer informatie over het configureren van audit Logboeken in de [Azure Portal](howto-configure-audit-logs-portal.md)