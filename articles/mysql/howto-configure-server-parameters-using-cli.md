---
title: Serverparameters configureren - Azure CLI - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u de serviceparameters in Azure Database voor MySQL configureert met behulp van het hulpprogramma voor de opdracht Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: ca5f80e57f90e4dd26ac2e4a175998ff3de2c102
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546427"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Serverparameters aanpassen met Azure CLI
U configuratieparameters voor een Azure Database voor MySQL-server aanbieden, weergeven en bijwerken met Azure CLI, het Azure-opdrachtregelhulpprogramma. Een subset van motorconfiguraties wordt op serverniveau weergegeven en kan worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- [Een Azure-database voor MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI-opdrachtregelhulpprogramma](/cli/azure/install-azure-cli) of gebruik de Azure Cloud Shell in de browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Serverconfiguratieparameters voor Azure Database voor MySQL-server weergeven
Als u alle aanpasbare parameters in een server en hun waarden wilt weergeven, voert u de opdracht [az mysql-serverconfiguratie uit.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

U de serverconfiguratieparameters voor de server **mydemoserver.mysql.database.azure.com** onder resourcegroep **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Zie de sectie MySQL-verwijzing op [variabelen voor serversystemen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)voor de definitie van elk van de vermelde parameters.

## <a name="show-server-configuration-parameter-details"></a>Details van de parameter van serverconfiguratie weergeven
Voer de opdracht [az mysql-serverconfiguratie uit](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) om details over een bepaalde configuratieparameter voor een server weer te geven.

In dit voorbeeld worden details weergegeven van de parameter **\_slow querylogserverconfiguratie\_** voor **servermydemoserver.mysql.database.azure.com** onder de myresourcegroep van de **brongroep.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Een parameterwaarde voor serverconfiguratie wijzigen
U ook de waarde wijzigen van een bepaalde parameter voor serverconfiguratie, waarmee de onderliggende configuratiewaarde voor de MySQL-serverengine wordt bijgewerkt. Als u de configuratie wilt bijwerken, gebruikt u de opdracht [az mysql-serverconfiguratie.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

De parameter **\_slow\_querylogserver** configuratie van server **mydemoserver.mysql.database.azure.com** bijwerken onder de **myresourcegroep van de brongroep.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Als u de waarde van een configuratieparameter opnieuw `--value` wilt instellen, laat u de optionele parameter weg en past de service de standaardwaarde toe. Voor het bovenstaande voorbeeld, zou het eruit zien als:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Met deze code wordt de configuratie van het **logboek voor langzame\_\_query's** gereset naar de standaardwaarde **UIT**. 

## <a name="working-with-the-time-zone-parameter"></a>Werken met de parameter tijdzone

### <a name="populating-the-time-zone-tables"></a>De tijdzonetabellen vullen

De tijdzonetabellen op uw server kunnen worden `mysql.az_load_timezone` ingevuld door de opgeslagen procedure aan te roepen vanuit een hulpprogramma zoals de MySQL-opdrachtregel of MySQL Workbench.

> [!NOTE]
> Als u de `mysql.az_load_timezone` opdracht uitvoert vanuit MySQL Workbench, moet u `SET SQL_SAFE_UPDATES=0;`mogelijk eerst de veilige updatemodus uitschakelen met behulp van .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw starten om ervoor te zorgen dat de tijdzonetabellen goed worden gevuld. Als u de server opnieuw wilt starten, gebruikt u de [Azure-portal](howto-restart-server-portal.md) of [CLI](howto-restart-server-cli.md).

Voer de volgende opdracht uit om beschikbare tijdzonewaarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijdzone op mondiaal niveau instellen

De tijdzone op globaal niveau kan worden ingesteld met de opdracht [az mysql-serverconfiguratieset.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

Met de volgende opdracht wordt de parameter **\_tijdzoneserverconfiguratie** van **server mydemoserver.mysql.database.azure.com** onder resourcegroep **myresourcegroup** naar **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>De tijdzone op sessieniveau instellen

De tijdzone op sessieniveau kan `SET time_zone` worden ingesteld door de opdracht uit te voeren vanuit een hulpprogramma zoals de Opdrachtregel MySQL of MySQL Workbench. In het onderstaande voorbeeld wordt de tijdzone ingesteld op de tijdzone **VS/Pacific.**  

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum- en tijdfuncties](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Volgende stappen

- Serverparameters configureren [in Azure-portal](howto-server-parameters.md)