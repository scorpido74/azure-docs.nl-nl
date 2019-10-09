---
title: Access Server meldt zich aan bij Azure Database for MariaDB met behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u toegang krijgt tot de server Logboeken in Azure Database for MariaDB met behulp van het opdracht regel hulpprogramma van Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023610"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Server logboeken configureren en openen met behulp van Azure CLI
U kunt de logboeken van de Azure Database for MariaDB-server downloaden met behulp van Azure CLI, het opdracht regel programma van Azure.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- De [Azure cli](/cli/azure/install-azure-cli) of Azure Cloud shell in de browser

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Logboek registratie voor Azure Database for MariaDB configureren
U kunt de server configureren voor toegang tot het MariaDB langzame query logboek door de volgende stappen te nemen:
1. Schakel logboek registratie in door de **langzame para meter @ no__t-1query @ no__t-2log** in te stellen op on.
2. Pas andere para meters aan, zoals **lang @ no__t-1query @ no__t-2time** en **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Zie [server parameters configureren](howto-configure-server-parameters-cli.md)voor meer informatie over het instellen van de waarde van deze para meters via Azure cli.

Met de volgende CLI-opdracht wordt bijvoorbeeld het langzame query logboek ingeschakeld, wordt de lange query tijd ingesteld op 10 seconden, waarna de logboek registratie van de langzame beheer instructie wordt uitgeschakeld. Ten slotte worden de configuratie opties voor uw beoordeling weer gegeven.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Logboeken voor Azure Database for MariaDB server weer geven
Als u de beschik bare langzame query logboek bestanden voor uw server wilt weer geven, voert u de opdracht [AZ mariadb server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) uit.

U kunt de logboek bestanden voor server **mydemoserver.mariadb.database.Azure.com** weer geven onder de resource groep **myresourcegroup**. Ga vervolgens naar de lijst met logboek bestanden naar een tekst bestand met de naam **log @ no__t-1files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken van de server downloaden
Met de opdracht [AZ mariadb server-logs down](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) load kunt u afzonderlijke logboek bestanden downloaden voor uw server.

Gebruik het volgende voor beeld om het specifieke logboek bestand voor de server **mydemoserver.mariadb.database.Azure.com** onder de resource groep **myresourcegroup** te downloaden naar uw lokale omgeving.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [trage query Logboeken in azure database for MariaDB](concepts-server-logs.md).
