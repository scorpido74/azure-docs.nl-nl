---
title: Stoppen/starten-Azure Portal-Azure Database for MySQL flexibele server
description: In dit artikel wordt beschreven hoe u bewerkingen in Azure Database for MySQL kunt stoppen/starten via de Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567477"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Een Azure Database for MySQL-flexibele server stoppen/starten (preview-versie)

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel wordt stapsgewijs beschreven hoe u de flexibele server kunt stoppen en starten.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for MySQL flexibele server hebben.

## <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Flexibele server stoppen."::: 

3.  Klik op **Ja** om het stoppen van de server te bevestigen.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Flexibele server stoppen."::: 

> [!NOTE]
> Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de flexibele server.

## <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Flexibele server stoppen.":::  

> [!NOTE]
> Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor de flexibele server.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in azure database for MySQL flexibele server](./concepts-networking.md)
- [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-virtual-network-portal.md).

