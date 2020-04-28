---
title: Server opnieuw starten-Azure Portal-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL-één server opnieuw kunt opstarten met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770081"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Start Azure Database for PostgreSQL-één server opnieuw op met behulp van de Azure Portal
In dit onderwerp wordt beschreven hoe u een Azure Database for PostgreSQL server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.
 
De tijd die nodig is om opnieuw op te starten, is afhankelijk van het PostgreSQL-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for postgresql server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Server opnieuw opstarten uitvoeren

Met de volgende stappen wordt de PostgreSQL-server opnieuw gestart:

1. Selecteer uw Azure Database for PostgreSQL server in het [Azure Portal](https://portal.azure.com/).

2. Klik op de werk balk van de pagina **overzicht** van de server op **opnieuw opstarten**.

   ![Azure Database for PostgreSQL-overzicht-knop opnieuw opstarten](./media/howto-restart-server-portal/2-server.png)

3. Klik op **Ja** om te bevestigen dat de server opnieuw wordt opgestart.

   ![Azure Database for PostgreSQL-opnieuw opstarten bevestigen](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Houd er rekening mee dat de server status wordt gewijzigd in opnieuw opstarten.

   ![Azure Database for PostgreSQL-start status](./media/howto-restart-server-portal/4-restarting-status.png)

5. Het opnieuw opstarten van de server is voltooid.

   ![Azure Database for PostgreSQL: opnieuw opstarten geslaagd](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van para meters in azure database for PostgreSQL](howto-configure-server-parameters-using-portal.md)