---
title: Opslag automatisch uitbreiden-Azure CLI-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de automatische groei van opslag kunt configureren met behulp van de Azure CLI in Azure Database for PostgreSQL-één server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.openlocfilehash: 2f04bbf052716b32c012222d4c5dbdcd8f4571bd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119698"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL storage automatisch uitbreiden-één server met behulp van de Azure CLI
In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

De server die [de opslag limiet bereikt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit), wordt ingesteld op alleen-lezen. Als automatisch verg Roten 100 van de opslag is ingeschakeld, wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag ruimte groter is dan 1 GB of 10% van de ingerichte opslag ruimte. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for postgresql server](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt u bij de opdracht prompt van Azure CLI `az --version` . Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatisch verg Roten van PostgreSQL-Server opslag inschakelen

Schakel de automatische groei van de server in op een bestaande server met de volgende opdracht:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

De automatische groei van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).