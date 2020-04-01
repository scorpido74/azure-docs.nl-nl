---
title: Server opnieuw opstarten - Azure CLI - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u een Azure Database voor MySQL-server opnieuw starten met de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063504"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Azure Database opnieuw starten voor MySQL-server met azure cli
In dit onderwerp wordt beschreven hoe u een Azure Database voor MySQL-server opnieuw starten. Mogelijk moet u uw server opnieuw opstarten om onderhoudsredenen, wat een korte storing veroorzaakt wanneer de server de bewerking uitvoert.

De herstart van de server wordt geblokkeerd als de service bezet is. De service verwerkt bijvoorbeeld mogelijk een eerder aangevraagde bewerking, zoals het schalen van vCores.

De tijd die nodig is om een herstart te voltooien, is afhankelijk van het MySQL-herstelproces. Om de herstarttijd te verminderen, raden we u aan de hoeveelheid activiteit die zich op de server voor de herstart voordoet te minimaliseren.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MySQL-server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Deze handleiding vereist dat u Azure CLI-versie 2.0 of hoger gebruikt. Als u de versie wilt bevestigen, `az --version`voert u bij de opdrachtprompt azure CLI de opdrachtprompt in. Zie Azure CLI [installeren]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw met de volgende opdracht:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van parameters in Azure Database voor MySQL](howto-configure-server-parameters-using-cli.md)