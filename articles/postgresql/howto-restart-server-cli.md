---
title: Server opnieuw opstarten-Azure CLI-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL-één-server opnieuw kunt opstarten met behulp van de Azure CLI
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647644"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Start Azure Database for PostgreSQL-één server opnieuw op met behulp van de Azure CLI
In dit onderwerp wordt beschreven hoe u een Azure Database for PostgreSQL server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.
 
De tijd die nodig is om opnieuw op te starten, is afhankelijk van het PostgreSQL-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze hand leiding:
- Een [Azure database for postgresql-server](quickstart-create-server-up-azure-cli.md)maken.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van para meters in azure database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
