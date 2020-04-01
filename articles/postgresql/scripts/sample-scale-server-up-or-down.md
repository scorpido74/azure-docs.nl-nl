---
title: Azure CLI-script - Azure Database schalen en bewaken voor PostgreSQL
description: "Azure CLI-voorbeeldscript: de schaal van een Azure Database for PostgreSQL-server aanpassen naar een ander prestatieniveau na het uitvoeren van query's op de metrische gegevens."
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 24aaf461576e6e043979660f9de968358763e003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68882992"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Eén PostgreSQL-server bewaken en de schaal ervan aanpassen met Azure CLI
Dit voorbeeld CLI-script schaalt rekenkracht en opslag voor één Azure-database voor PostgreSQL-server na het opvragen van de statistieken. Compute kan omhoog of omlaag schalen. Opslag kan alleen worden opgeschaald. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal uit te voeren, moet u voor dit artikel gebruikmaken van Azure CLI-versie 2.0 of hoger. Controleer de versie door `az --version` uit te voeren. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) voor het installeren of upgraden van uw versie van Azure CLI.

## <a name="sample-script"></a>Voorbeeldscript
Werk het script bij met uw abonnements-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Hiermee wordt een PostgreSQL-server gemaakt waar de SQL-database wordt gehost. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Hiermee worden de eigenschappen van de PostgreSQL-server bijgewerkt. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Geeft de metrische waarde weer voor de resources. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Database voor PostgreSQL compute and storage](../concepts-pricing-tiers.md)
- Meer scripts om te proberen: [Azure CLI-voorbeelden voor Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Meer informatie over de [Azure CLI](/cli/azure)
