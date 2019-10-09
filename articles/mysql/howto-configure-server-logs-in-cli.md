---
title: Toegang tot langzame query Logboeken in Azure Database for MySQL met behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u de logboeken voor trage query's in Azure Database for MySQL kunt openen met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030633"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Langzame query logboeken configureren en openen met behulp van Azure CLI
U kunt de Azure Database for MySQL langzame query logboeken downloaden met behulp van Azure CLI, het opdracht regel programma van Azure.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-cli.md)
- De [Azure cli](/cli/azure/install-azure-cli) of Azure Cloud shell in de browser

## <a name="configure-logging"></a>Logboek registratie configureren
U kunt de server configureren voor toegang tot het langzame Slow-query logboek door de volgende stappen uit te voeren:
1. Schakel logboek registratie voor langzame query's in door de para meter **Slow @ no__t-1query @ no__t-2log** in te stellen op on.
2. Pas andere para meters aan, zoals **lang @ no__t-1query @ no__t-2time** en **log @ no__t-4slow @ no__t-5admin @ no__t-6statements**.

Zie [server parameters configureren](howto-configure-server-parameters-using-cli.md)voor meer informatie over het instellen van de waarde van deze para meters via Azure cli.

Met de volgende CLI-opdracht wordt bijvoorbeeld het langzame query logboek ingeschakeld, wordt de lange query tijd ingesteld op 10 seconden, waarna de logboek registratie van de langzame beheer instructie wordt uitgeschakeld. Ten slotte worden de configuratie opties voor uw beoordeling weer gegeven.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Logboeken voor Azure Database for MySQL server weer geven
Als u de beschik bare langzame query logboek bestanden voor uw server wilt weer geven, voert u de opdracht [AZ mysql server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) uit.

U kunt de logboek bestanden voor server **mydemoserver.mysql.database.Azure.com** weer geven onder de resource groep **myresourcegroup**. Ga vervolgens naar de lijst met logboek bestanden naar een tekst bestand met de naam **log @ no__t-1files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Logboeken van de server downloaden
Met de opdracht [AZ mysql server-logs-logboeken](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) kunt u afzonderlijke logboek bestanden voor uw server downloaden. 

Gebruik het volgende voor beeld om het specifieke logboek bestand voor de server **mydemoserver.mysql.database.Azure.com** onder de resource groep **myresourcegroup** te downloaden naar uw lokale omgeving.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [trage query Logboeken in azure database for MySQL](concepts-server-logs.md).
