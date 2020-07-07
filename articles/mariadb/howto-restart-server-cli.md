---
title: Server opnieuw opstarten-Azure CLI-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt opstarten met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79530645"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Azure Database for MariaDB server opnieuw opstarten met behulp van de Azure CLI
In dit onderwerp wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De tijd die nodig is om opnieuw op te starten, is afhankelijk van het MariaDB-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt u bij de opdracht prompt van Azure CLI `az --version` . Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.


## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van para meters in azure database for MariaDB](howto-configure-server-parameters-cli.md)