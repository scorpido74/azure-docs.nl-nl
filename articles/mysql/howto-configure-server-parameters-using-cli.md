---
title: Server parameters configureren-Azure CLI-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u de service parameters in Azure Database for MySQL kunt configureren met behulp van het Azure CLI-opdracht regel programma.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 2a53debb72cfd5da73c2bceb7993288eb828237a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770523"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Server parameters aanpassen met behulp van Azure CLI
U kunt configuratie parameters voor een Azure Database for MySQL server weer geven, tonen en bijwerken met behulp van Azure CLI, het opdracht regel programma van Azure. Een subset van de engine configuraties wordt weer gegeven op server niveau en kan worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Een Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure cli](/cli/azure/install-azure-cli) -opdracht regel programma of gebruik de Azure Cloud shell in de browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Server configuratie parameters voor Azure Database for MySQL server weer geven
Voer de opdracht [AZ mysql server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) uit om alle para meters die kunnen worden gewijzigd op een server en hun waarden weer te geven.

U kunt de server configuratie parameters weer geven voor de server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Zie het gedeelte MySQL-verwijzing in [Server systeem variabelen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)voor de definitie van elk van de vermelde para meters.

## <a name="show-server-configuration-parameter-details"></a>Details van server configuratie parameters weer geven
Als u details over een bepaalde configuratie parameter voor een server wilt weer geven, voert u de opdracht [AZ mysql server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) uit.

In dit voor beeld worden details weer gegeven van de **langzame\_query\_** de configuratie parameter van de logboek server voor server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Een waarde voor de para meter server configuratie wijzigen
U kunt ook de waarde van een bepaalde server configuratie parameter wijzigen, waarmee de onderliggende configuratie waarde wordt bijgewerkt voor de MySQL-server engine. Als u de configuratie wilt bijwerken, gebruikt u de opdracht [AZ mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Voor het bijwerken van de **langzame\_query\_** configuratie parameter van de logboek server van server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Als u de waarde van een configuratie parameter opnieuw wilt instellen, laat u de para meter optionele `--value` weg en past de service de standaard waarde toe. In het bovenstaande voor beeld zou er als volgt uitzien:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Met deze code wordt de **langzame\_query opnieuw ingesteld\_logboek** configuratie naar de standaard **waarde.** 

## <a name="working-with-the-time-zone-parameter"></a>Werken met de para meter tijd zone

### <a name="populating-the-time-zone-tables"></a>De tijd zone tabellen worden gevuld

De tijd zone tabellen op uw server kunnen worden gevuld door de `az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de `az_load_timezone`-opdracht uit MySQL Workbench uitvoert, moet u de veilige update modus mogelijk eerst uitschakelen met behulp van `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Voer de volgende opdracht uit om de beschik bare tijd zone waarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijd zone van het globale niveau instellen

De tijd zone globaal niveau kan worden ingesteld met behulp van de opdracht [AZ mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Met de volgende opdracht wordt de tijd van de **\_zone** server configuratie para meter van server **mydemoserver.mysql.database.Azure.com** onder resource groep **myresourcegroup** naar **VS/Pacific**bijgewerkt.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>De tijd zone op sessie niveau instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.  

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum-en tijd functies](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Volgende stappen

- [Server parameters configureren in azure Portal](howto-server-parameters.md)