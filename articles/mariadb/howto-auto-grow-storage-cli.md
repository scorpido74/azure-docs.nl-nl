---
title: Automatische opslag voor het automatisch laten groeien - Azure CLI - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u automatische opslag inschakelen met behulp van de Azure CLI in Azure Database voor MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4be84c750f6a3ca7a0d48aa2b98d75272c1cbadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529081"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure-database automatisch kweken voor MariaDB-opslag met de Azure CLI
In dit artikel wordt beschreven hoe u een Azure Database voor MariaDB-serveropslag configureren om te groeien zonder dat dit gevolgen heeft voor de werkbelasting.

De server [die de opslaglimiet bereikt,](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)is ingesteld op alleen-lezen. Als opslagauto's dan worden ingeschakeld voor servers met minder dan 100 GB ingerichte opslag, wordt de ingerichte opslaggrootte met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de ingerichte opslag. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5% vergroot wanneer de vrije opslagruimte minder dan 5% van de ingerichte opslaggrootte bedraagt. De maximale opslaglimieten zoals [hier](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) gespecificeerd zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Deze handleiding vereist dat u Azure CLI-versie 2.0 of hoger gebruikt. Als u de versie wilt bevestigen, `az --version`voert u bij de opdrachtprompt azure CLI de opdrachtprompt in. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Automatisch groeien van MariaDB-serveropslag inschakelen

Schakel serveropslag automatisch uit op een bestaande server met de volgende opdracht:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Schakel serveropslag automatisch uit en maak een nieuwe server met de volgende opdracht:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over statistieken](howto-alert-metric.md).