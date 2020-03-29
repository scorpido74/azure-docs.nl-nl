---
title: Parameters configureren - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u Postgres-parameters configureert in Azure Database voor PostgreSQL - Single Server met de Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763620"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Serverconfiguratieparameters voor Azure Database voor PostgreSQL aanpassen - Enkele server met Azure CLI
U configuratieparameters voor een Azure PostgreSQL-server aanbieden, weergeven en bijwerken met behulp van de Command Line Interface (Azure CLI). Een subset van motorconfiguraties wordt op serverniveau weergegeven en kan worden gewijzigd. 

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een Azure-database maken voor PostgreSQL-server en -database door [een Azure-database voor PostgreSQL](quickstart-create-server-database-azure-cli.md) te maken
- Installeer [Azure CLI-opdrachtregelinterface](/cli/azure/install-azure-cli) op uw machine of gebruik de [Azure Cloud Shell](../cloud-shell/overview.md) in de Azure-portal met uw browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Serverconfiguratieparameters voor Azure Database voor PostgreSQL-server weergeven
Als u alle aanpasbare parameters in een server en hun waarden wilt weergeven, voert u de opdracht voor de configuratielijst van de [AZ-postgres-server](/cli/azure/postgres/server/configuration) uit.

U de serverconfiguratieparameters voor de server **mydemoserver.postgres.database.azure.com** onder resourcegroep **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Details van de parameter van serverconfiguratie weergeven
Voer de opdracht az [postgres server configuration show](/cli/azure/postgres/server/configuration) uit om details over een bepaalde configuratieparameter voor een server weer te geven.

In dit voorbeeld worden details weergegeven van de **mydemoserver.postgres.database.azure.com** parameter voor **myresourcegroup.** **\_\_** de configuratie van de logmin-berichtenserver voor servermydemoserver.postgres.database.azure.com onder de myresourcegroep van de brongroep.
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Parameterwaarde serverconfiguratie wijzigen
U ook de waarde wijzigen van een bepaalde parameter voor serverconfiguratie, waarmee de onderliggende configuratiewaarde voor de PostgreSQL-serverengine wordt bijgewerkt. Als u de configuratie wilt bijwerken, gebruikt u de opdracht voor de configuratieset van de [AZ-postgres-server.](/cli/azure/postgres/server/configuration) 

Als u de parameter van de **logmin-berichtenserver\_\_** van server **mydemoserver.postgres.database.azure.com** bijwerken onder de **myresourcegroep van resourcegroepen.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Als u de waarde van een configuratieparameter opnieuw wilt instellen, kiest u ervoor om de optionele `--value` parameter weg te laten en past de service de standaardwaarde toe. In het bovenstaande voorbeeld, zou het eruit zien als:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Met deze opdracht wordt de configuratie **van de\_inlogminberichten\_** ingesteld op de standaardwaardeWAARSCHUWING . **WARNING** Zie PostgreSQL-documentatie op [serverconfiguratie](https://www.postgresql.org/docs/9.6/static/runtime-config.html)voor meer informatie over serverconfiguratie en toegestane waarden.

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het opnieuw opstarten van een server](howto-restart-server-cli.md)
- Zie [Serverlogboeken in Azure Database voor PostgreSQL](concepts-server-logs.md) configureren en openen
