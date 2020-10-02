---
title: CLI-script - Serverparameters wijzigen - Azure Database for MariaDB
description: Dit CLI-voorbeeldscript maakt een lijst met alle beschikbare serverconfiguratie en updates van een Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: ce27dc3008803faf548c10f4fc2ab3b579af2ba0
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90525028"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Configuraties van een Azure Database for MariaDB-server opsommen en bijwerken met behulp van Azure CLI
Met dit CLI-voorbeeldscript wordt een lijst gemaakt van alle beschikbare configuratieparameters en de toegestane waarden ervan voor een Azure Database for MariaDB-server, en wordt *innodb_lock_wait_timeout* ingesteld op een andere waarde dan de standaardwaarde.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal uit te voeren, moet u voor dit artikel gebruikmaken van Azure CLI-versie 2.0 of hoger. Controleer de versie door `az --version` uit te voeren. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) voor het installeren of upgraden van uw versie van Azure CLI. 

## <a name="sample-script"></a>Voorbeeldscript
Bewerk in dit voorbeeldscript de gemarkeerde regels om de gebruikersnaam en het wachtwoord van de beheerder naar uw eigen bij te werken.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Hiermee wordt een MariaDB-server gemaakt waarop de databases worden gehost. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Hiermee wordt een lijst gemaakt van de configuraties van een Azure Database for MariaDB-server. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Hiermee wordt de configuratie van een Azure Database for MariaDB-server bijgewerkt. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Hiermee wordt de configuratie van een Azure Database for MariaDB-server weergegeven. |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over Azure CLI: [Azure CLI-documentatie](/cli/azure).
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Zie [How To Configure Server Parameters in Azure Database for MariaDB](../howto-server-parameters.md) (Serverparameters configureren in Azure Database for MariaDB) voor meer informatie over serverparameters.
