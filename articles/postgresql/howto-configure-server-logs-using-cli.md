---
title: Logboeken beheren - Azure CLI - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u de serverlogboeken (.logbestanden) configureert en opent in Azure Database voor PostgreSQL - Single Server met behulp van de Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763569"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Serverlogboeken configureren en openen met Azure CLI
U de PostgreSQL-serverfoutlogboeken downloaden met behulp van de command-line interface (Azure CLI). Toegang tot transactielogboeken wordt echter niet ondersteund. 

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- [Azure-database voor PostgreSQL-server](quickstart-create-server-database-azure-cli.md)
- Het [Azure CLI](/cli/azure/install-azure-cli) azure CLI-opdrachtregelhulpprogramma of Azure Cloud Shell in de browser

## <a name="configure-logging"></a>Logboekregistratie configureren
U de server configureren om toegang te krijgen tot querylogboeken en foutlogboeken. Foutlogboeken kunnen informatie over automatisch vacuüm, verbinding en controlepunt bevatten.
1. Schakel logboekregistratie in.
2. Als u querylogboekregistratie wilt inschakelen, werkt u **logboekinstructie\_** en **de\_\_instructie voor de duur van\_het logboek.**
3. Bewaarperiode bijwerken.

Zie [Serverconfiguratieparameters aanpassen voor](howto-configure-server-parameters-using-cli.md)meer informatie .

## <a name="list-logs"></a>Lijstlogboeken
Voer de [lijstmetopdracht az-postgres serverlogs](/cli/azure/postgres/server-logs) uit om de beschikbare logboekbestanden voor uw server aan te geven.

U de logboekbestanden voor **servermydemoserver.postgres.database.azure.com** aanbieden onder de **groep myresourcegroep van**de brongroep. Stuur vervolgens de lijst met logboekbestanden naar een tekstbestand genaamd **logbestanden\_\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Logboeken lokaal downloaden van de server
Met de az [postgres server-logs download](/cli/azure/postgres/server-logs) opdracht, u downloaden van individuele log bestanden voor uw server. 

Gebruik het volgende voorbeeld om het specifieke logboekbestand voor de server te downloaden **mydemoserver.postgres.database.azure.com** onder de brongroep **myresourcegroup** naar uw lokale omgeving.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Volgende stappen
- Zie [Serverlogboeken in Azure Database voor PostgreSQL voor](concepts-server-logs.md)meer informatie over serverlogboeken.
- Zie [Serverconfiguratieparameters aanpassen met Azure CLI](howto-configure-server-parameters-using-cli.md)voor meer informatie over serverparameters.
