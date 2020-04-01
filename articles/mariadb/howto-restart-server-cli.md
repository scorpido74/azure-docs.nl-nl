---
title: Server opnieuw opstarten - Azure CLI - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database voor MariaDB-server opnieuw starten met de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530645"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Azure Database opnieuw starten voor MariaDB-server met de Azure CLI
In dit onderwerp wordt beschreven hoe u een Azure Database voor MariaDB-server opnieuw starten. Mogelijk moet u uw server opnieuw opstarten om onderhoudsredenen, wat een korte storing veroorzaakt wanneer de server de bewerking uitvoert.

De herstart van de server wordt geblokkeerd als de service bezet is. De service verwerkt bijvoorbeeld mogelijk een eerder aangevraagde bewerking, zoals het schalen van vCores.

De tijd die nodig is om een herstart te voltooien, is afhankelijk van het herstelproces van MariaDB. Om de herstarttijd te verminderen, raden we u aan de hoeveelheid activiteit die zich op de server voor de herstart voordoet te minimaliseren.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Deze handleiding vereist dat u Azure CLI-versie 2.0 of hoger gebruikt. Als u de versie wilt bevestigen, `az --version`voert u bij de opdrachtprompt azure CLI de opdrachtprompt in. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw met de volgende opdracht:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van parameters in Azure Database voor MariaDB](howto-configure-server-parameters-cli.md)