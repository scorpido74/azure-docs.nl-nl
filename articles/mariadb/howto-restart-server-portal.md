---
title: Server opnieuw starten-Azure Portal-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt opstarten met behulp van Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 369d19d98946f8309c7f2053f4453e09a7ed902f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121058"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Azure Database for MariaDB server opnieuw opstarten met Azure Portal
In dit onderwerp wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De tijd die nodig is om opnieuw op te starten, is afhankelijk van het MariaDB-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Server opnieuw opstarten uitvoeren

Met de volgende stappen wordt de MariaDB-server opnieuw gestart:

1. Selecteer uw Azure Database for MariaDB server in het Azure Portal.

2. Klik op de werk balk van de pagina **overzicht** van de server op **opnieuw opstarten**.

   ![Azure Database for MariaDB-overzicht-knop opnieuw opstarten](./media/howto-restart-server-portal/2-server.png)

3. Klik op **Ja** om te bevestigen dat de server opnieuw wordt opgestart.

   ![Azure Database for MariaDB-opnieuw opstarten bevestigen](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Houd er rekening mee dat de server status wordt gewijzigd in opnieuw opstarten.

   ![Azure Database for MariaDB-start status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Het opnieuw opstarten van de server is voltooid.

   ![Azure Database for MariaDB: opnieuw opstarten geslaagd](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Volgende stappen

[Snelstartgids: Azure Database for MariaDB-server maken met behulp van Azure Portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)