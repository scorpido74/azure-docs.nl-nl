---
title: Serverparameters configureren - Azure CLI - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u de serviceparameters in Azure Database voor MariaDB configureert met behulp van het hulpprogramma voor de opdracht Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 3ba06ea592d51eedbe827e1ab6418f65722d579c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632308"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Serverconfiguratieparameters aanpassen met Azure CLI
U configuratieparameters voor een Azure Database voor MariaDB-server aanbieden, weergeven en bijwerken met Azure CLI, het Azure-opdrachtregelhulpprogramma. Een subset van motorconfiguraties wordt op serverniveau weergegeven en kan worden gewijzigd.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- [Een Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI-opdrachtregelhulpprogramma](/cli/azure/install-azure-cli) of gebruik de Azure Cloud Shell in de browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Serverconfiguratieparameters voor Azure Database voor MariaDB-server weergeven
Voer de opdracht voor de configuratielijst van de [AZ Mariadb-server](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) uit om alle aanpasbare parameters in een server en hun waarden op te sommen.

U de serverconfiguratieparameters voor de server **mydemoserver.mariadb.database.azure.com** onder resourcegroep **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Zie de referentiesectie MariaDB op variabelen voor [serversystemen](https://mariadb.com/kb/en/library/server-system-variables/)voor de definitie van elk van de vermelde parameters.

## <a name="show-server-configuration-parameter-details"></a>Details van de parameter van serverconfiguratie weergeven
Voer de opdracht az [mariadb-serverconfiguratie uit](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) om details over een bepaalde configuratieparameter voor een server weer te geven.

In dit voorbeeld worden details weergegeven van de parameter **\_slow querylogserverconfiguratie\_** voor **servermydemoserver.mariadb.database.azure.com** onder de myresourcegroep van de **brongroep.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Een parameterwaarde voor serverconfiguratie wijzigen
U ook de waarde wijzigen van een bepaalde parameter voor serverconfiguratie, waarmee de onderliggende configuratiewaarde voor de MariaDB-serverengine wordt bijgewerkt. Als u de configuratie wilt bijwerken, gebruikt u de opdracht voor de configuratieset van de [AZ Mariadb-server.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 

De parameter **\_slow\_querylogserver** configuratie van server **mydemoserver.mariadb.database.azure.com** bijwerken onder de **myresourcegroep van de brongroep.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Als u de waarde van een configuratieparameter opnieuw `--value` wilt instellen, laat u de optionele parameter weg en past de service de standaardwaarde toe. Voor het bovenstaande voorbeeld, zou het eruit zien als:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Met deze code wordt de configuratie van het **logboek voor langzame\_\_query's** gereset naar de standaardwaarde **UIT**. 

## <a name="working-with-the-time-zone-parameter"></a>Werken met de parameter tijdzone

### <a name="populating-the-time-zone-tables"></a>De tijdzonetabellen vullen

De tijdzonetabellen op uw server kunnen worden `mysql.az_load_timezone` ingevuld door de opgeslagen procedure aan te roepen vanuit een tool zoals de MariaDB-opdrachtregel of MariaDB Workbench.

> [!NOTE]
> Als u de `mysql.az_load_timezone` opdracht van MariaDB Workbench uitvoert, moet u `SET SQL_SAFE_UPDATES=0;`mogelijk eerst de veilige updatemodus uitschakelen met behulp van .

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

De tijdzone op mondiaal niveau kan worden ingesteld met de opdracht [az mariadb-serverconfiguratieset.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set)

Met de volgende opdracht wordt de parameter **\_tijdzoneserverconfiguratie** van server **mydemoserver.mariadb.database.azure.com** onder resourcegroep **myresourcegroup** naar **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>De tijdzone op sessieniveau instellen

De tijdzone op sessieniveau kan `SET time_zone` worden ingesteld door de opdracht uit te voeren vanuit een gereedschap zoals de MariaDB-opdrachtregel of MariaDB Workbench. In het onderstaande voorbeeld wordt de tijdzone ingesteld op de tijdzone **VS/Pacific.**  

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MariaDB-documentatie voor [datum- en tijdfuncties.](https://mariadb.com/kb/en/library/date-time-functions/)

## <a name="next-steps"></a>Volgende stappen

- Serverparameters configureren [in Azure-portal](howto-server-parameters.md)
