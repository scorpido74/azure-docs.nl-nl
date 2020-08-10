---
title: 'CLI-script: de schaal van een Azure Database for MySQL-server aanpassen'
description: Met dit CLI-voorbeeldscript wordt de schaal van een Azure Database for MySQL-server aangepast naar een ander prestatieniveau na het uitvoeren van query's op de metrische gegevens.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 318e41089f096f22d17faaa77863fe6af54a3553
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87483736"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Een Azure Database for MySQL-server bewaken en de schaal ervan aanpassen met Azure CLI
Met dit CLI-voorbeeldscript worden de compute- en opslagservices van een Azure Database for MySQL-server aangepast nadat er query's zijn uitgevoerd op de metrische gegevens. Compute kan omhoog of omlaag worden geschaald. Opslag kan alleen omhoog worden geschaald.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal uit te voeren, moet u voor dit artikel gebruikmaken van Azure CLI-versie 2.0 of hoger. Controleer de versie door `az --version` uit te voeren. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) voor het installeren of upgraden van uw versie van Azure CLI. 

## <a name="sample-script"></a>Voorbeeldscript
Werk het script bij met uw abonnements-id.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen nadat het script is uitgevoerd. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Uitleg van het script
Dit script maakt gebruik van de opdrachten die in de volgende tabel worden weergegeven:

| **Opdracht** | **Opmerkingen** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Hiermee wordt een MySQL-server gemaakt waar de databases worden gehost. |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | Hiermee werkt u eigenschappen van de MySQL-server bij. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Geeft de metrische waarde weer voor de resources. |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen
- Lees meer over [compute en opslag voor Azure Database for MySQL](../concepts-pricing-tiers.md)
- Aanvullende scripts proberen: [Azure CLI-voorbeelden voor Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Lees meer over de [Azure CLI](/cli/azure).
