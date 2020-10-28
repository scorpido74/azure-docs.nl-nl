---
title: Server opnieuw starten-Azure Portal-Azure Database for MySQL-flexibele server
description: In dit artikel wordt beschreven hoe u een Azure Database for MySQL flexibele server opnieuw kunt opstarten met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: c44e1da46b969b2d359a225e9d310160ce2092ce
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681559"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Azure Database for MySQL flexibele server opnieuw opstarten met Azure Portal
In dit onderwerp wordt beschreven hoe u een Azure Database for MySQL flexibele server opnieuw kunt opstarten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor er een korte storing optreedt terwijl de server de bewerking uitvoert.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De tijd die nodig is om opnieuw op te starten, is afhankelijk van het MySQL-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for MySQL flexibele server](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Server opnieuw opstarten uitvoeren

De MySQL-server wordt opnieuw gestart met de volgende stappen:

1. Selecteer uw Azure Database for MySQL flexibele server in de Azure Portal.

2. Klik op de werk balk van de pagina **overzicht** van de server op **opnieuw opstarten** .

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL-overzicht-knop opnieuw opstarten":::

3. Klik op **Ja** om te bevestigen dat de server opnieuw wordt opgestart.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL-overzicht-knop opnieuw opstarten":::

4. Houd er rekening mee dat de server status wordt gewijzigd in opnieuw opstarten.

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL-overzicht-knop opnieuw opstarten":::

5. Het opnieuw opstarten van de server is voltooid.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL-overzicht-knop opnieuw opstarten":::

## <a name="next-steps"></a>Volgende stappen

[Snelstartgids: Azure Database for MySQL flexibele server maken met behulp van Azure Portal](quickstart-create-server-portal.md)
