---
title: 'Azure CLI-script: serverlogboeken downloaden in Azure Database for PostgreSQL'
description: Dit voorbeeld Azure CLI script laat zien hoe u de serverlogboeken van een Azure Database voor PostgreSQL server kunt activeren en downloaden.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 107c844051ab396fe467a1a629883746b2903125
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "66154514"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Langzame-serverquerylogboeken van een Azure Database voor PostgreSQL-server activeren en downloaden met behulp van Azure CLI
Met dit CLI-voorbeeldscript worden de langzame-querylogboeken van één Azure Database for PostgreSQL server geactiveerd en gedownload.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal uit te voeren, moet u voor dit artikel gebruikmaken van Azure CLI-versie 2.0 of hoger. Controleer de versie door `az --version` uit te voeren. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) voor het installeren of upgraden van uw versie van Azure CLI.

## <a name="sample-script"></a>Voorbeeldscript
Bewerk in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder naar uw eigen bij te werken. Vervang &lt;log_file_name&gt; in de `az monitor`-opdrachten door de naam van uw eigen serverlogboekbestand.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [az postgres server create](/cli/azure/postgres/server) | Hiermee wordt een PostgreSQL-server gemaakt waar de SQL-database wordt gehost. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Hiermee maakt u een lijst van de configuratiewaarden voor een server. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Hiermee werkt u de configuratie van een server bij. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Hiermee maakt u een lijst met de logboekbestanden van een server. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Hiermee kunt u logboekgegevens downloaden. |
| [az group delete](/cli/azure/group) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Lees de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
- Meer scripts om te proberen: [Azure CLI-voorbeelden voor Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- [Serverlogboeken in de Azure-portal configureren en benaderen](../howto-configure-server-logs-in-portal.md)
