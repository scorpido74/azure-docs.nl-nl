---
title: Opslag automatisch uitbreiden-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u automatische groei opslag kunt inschakelen met behulp van de Azure CLI in Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 39b07e313213be2645d211f292d49d9fa1a8d6bc
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120157"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure Database for MariaDB opslag automatisch uitbreiden met behulp van de Azure CLI
In dit artikel wordt beschreven hoe u een Azure Database for MariaDB Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

De server die [de opslag limiet bereikt](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit), wordt ingesteld op alleen-lezen. Als automatisch verg Roten 100 van de opslag is ingeschakeld, wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag ruimte groter is dan 1 GB of 10% van de ingerichte opslag ruimte. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt u bij de opdracht prompt van Azure CLI `az --version` . Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Automatisch verg Roten van MariaDB-Server opslag inschakelen

Schakel de automatische groei van de server in op een bestaande server met de volgende opdracht:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

De automatische groei van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-metric.md).