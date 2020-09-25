---
title: Stoppen/starten-Azure Portal-Azure Database for MySQL-server
description: In dit artikel wordt beschreven hoe u bewerkingen in Azure Database for MySQL kunt stoppen/starten.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: f09b6d48e8a98b0995c882769d6c978996324dad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343809"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Een Azure Database for MySQL stoppen/starten

> [!IMPORTANT]
> De functionaliteit voor stoppen/starten voor Azure Database for MySQL is momenteel beschikbaar als open bare preview.

In dit artikel wordt stapsgewijs beschreven hoe u de flexibele server kunt stoppen en starten.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for MySQL flexibele server hebben.

> [!NOTE]
> Raadpleeg de beperking van het gebruik van [stoppen/starten](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>De Azure Database for MySQL stoppen/starten met Azure Portal

### <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de mysql-server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Server Azure Database for MySQL stoppen":::

    > [!NOTE]
    > Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de flexibele server.

### <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Server Azure Database for MySQL starten":::

    > [!NOTE]
    > Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor de flexibele server.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>De Azure Database for MySQL stoppen/starten met CLI

### <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de mysql-server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de flexibele server.

### <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor de flexibele server.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
