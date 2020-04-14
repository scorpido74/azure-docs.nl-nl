---
title: Toegang tot slow query logs - Azure CLI - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u de logboeken voor trage query's in Azure Database voor MySQL openen met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 87db1a2af0bfdc854c909ef4221a3d97f9bf10d5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270669"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Langzame querylogboeken configureren en openen met Azure CLI
U de Azure Database for MySQL slow query logs downloaden met Azure CLI, het Azure-opdrachtregelhulpprogramma.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- [Azure-database voor MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- De [Azure CLI](/cli/azure/install-azure-cli) of Azure Cloud Shell in de browser

## <a name="configure-logging"></a>Logboekregistratie configureren
U de server configureren om toegang te krijgen tot het Slow Query-logboek van MySQL door de volgende stappen uit te voeren:
1. Schakel langzame querylogboekregistratie in door de parameter **Slow\_Query\_Log** in te stellen op AAN.
2. Selecteer waar u de logboeken wilt uitvoeren naar het gebruik van **logboekuitvoer\_**. Als u logboeken wilt verzenden naar zowel lokale opslag als diagnostische logboeken voor Azure-monitor, selecteert u **Bestand**. Als u logboeken alleen naar Azure Monitor Logs wilt verzenden, selecteert u **Geen**
3. Pas andere parameters aan, zoals **lange\_querytijd\_** en langzame **\_\_beheerdersinstructies\_bijhouden.**

Zie [Serverparameters configureren](howto-configure-server-parameters-using-cli.md)voor meer informatie over het instellen van de waarde van deze parameters via Azure CLI.

De volgende opdracht CLI schakelt bijvoorbeeld het langzame querylogboek in, stelt de lange querytijd in op 10 seconden en schakelt vervolgens de logboekregistratie van de langzame beheerinstructie uit. Ten slotte worden de configuratieopties voor uw beoordeling weergegeven.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lijstlogboeken voor Azure Database voor MySQL-server
Als **log_output** is geconfigureerd in 'Bestand', hebt u rechtstreeks toegang tot logboeken vanuit de lokale opslag van de server. Als u de beschikbare slow query-logboekbestanden voor uw server wilt weergeven, voert u de [opdracht lijst met lijst met az mysql-serverlogboeken](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) uit.

U de logboekbestanden voor **servermydemoserver.mysql.database.azure.com** aanbieden onder de **groep myresourcegroep van**de brongroep. Stuur vervolgens de lijst met logboekbestanden naar een tekstbestand genaamd **logbestanden\_\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken downloaden van de server
Als **log_output** is geconfigureerd in 'Bestand', u afzonderlijke logbestanden van uw server downloaden met de [downloadopdracht az mysql-serverlogs.](/cli/azure/mysql/server-logs#az-mysql-server-logs-download)

Gebruik het volgende voorbeeld om het specifieke logboekbestand voor de server te downloaden **mydemoserver.mysql.database.azure.com** onder de brongroep **myresourcegroup** naar uw lokale omgeving.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboeken met langzame query's in Azure Database voor MySQL](concepts-server-logs.md).
