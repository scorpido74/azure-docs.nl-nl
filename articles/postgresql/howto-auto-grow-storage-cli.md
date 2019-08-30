---
title: Azure Database for PostgreSQL storage automatisch uitbreiden-één server met behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u automatische groei opslag kunt inschakelen met behulp van de Azure CLI in Azure Database for PostgreSQL-één server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b51158ca9ca54f5157e47e2fcbbada8890104105
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143750"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL storage automatisch uitbreiden-één server met behulp van de Azure CLI
In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

De server die [de opslag limiet bereikt](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit), wordt ingesteld op alleen-lezen. Als automatisch verg Roten 100 van de opslag is ingeschakeld, wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag ruimte groter is dan 1 GB of 10% van de ingerichte opslag ruimte. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
Voor deze handleiding, hebt u het volgende nodig:
- Een [Azure database for postgresql server](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> In deze gebruiksaanwijzing vereist het gebruik van Azure CLI versie 2.0 of hoger. Als u wilt controleren welke versie, bij de Azure CLI-opdrachtprompt, voer `az --version`. Als u wilt installeren of upgraden, Zie [Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatisch verg Roten van PostgreSQL-Server opslag inschakelen

Schakel de automatische groei van de server in op een bestaande server met de volgende opdracht:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

De automatische groei van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).