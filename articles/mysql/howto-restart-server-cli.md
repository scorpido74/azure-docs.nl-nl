---
title: Server opnieuw opstarten-Azure CLI-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een Azure Database for MySQL server opnieuw kunt opstarten met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9707aaa911fc8ceb8c03b369bd6479646ca3418c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500722"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Azure Database for MySQL server opnieuw opstarten met behulp van de Azure CLI
In dit onderwerp wordt beschreven hoe u een Azure Database for MySQL server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De tijd die nodig is om opnieuw op te starten, is afhankelijk van het MySQL-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for mysql server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Voor deze hand leiding moet u Azure CLI versie 2,0 of hoger gebruiken. Als u de versie wilt bevestigen, typt u bij de opdracht prompt van Azure CLI `az --version` . Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.


## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van para meters in azure database for MySQL](howto-configure-server-parameters-using-cli.md)
