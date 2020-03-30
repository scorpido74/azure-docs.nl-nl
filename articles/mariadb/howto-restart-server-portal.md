---
title: Server opnieuw opstarten - Azure portal - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database voor MariaDB-server opnieuw starten met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534708"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Azure-database opnieuw starten voor MariaDB-server met Azure-portal
In dit onderwerp wordt beschreven hoe u een Azure Database voor MariaDB-server opnieuw starten. Mogelijk moet u uw server opnieuw opstarten om onderhoudsredenen, wat een korte storing veroorzaakt wanneer de server de bewerking uitvoert.

De herstart van de server wordt geblokkeerd als de service bezet is. De service verwerkt bijvoorbeeld mogelijk een eerder aangevraagde bewerking, zoals het schalen van vCores.

De tijd die nodig is om een herstart te voltooien, is afhankelijk van het herstelproces van MariaDB. Om de herstarttijd te verminderen, raden we u aan de hoeveelheid activiteit die zich op de server voor de herstart voordoet te minimaliseren.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor MariaDB-server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Server opnieuw opstarten uitvoeren

De volgende stappen starten de MariaDB-server:

1. Selecteer in de Azure-portal uw Azure-database voor MariaDB-server.

2. Klik op de werkbalk van de **pagina Overzicht** van de server op **Opnieuw starten**.

   ![Azure Database voor MariaDB - Overzicht - Knop Opnieuw opstarten](./media/howto-restart-server-portal/2-server.png)

3. Klik **op Ja** om het opnieuw opstarten van de server te bevestigen.

   ![Azure Database voor MariaDB - Opnieuw starten bevestigen](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Houd er rekening mee dat de serverstatus wordt gewijzigd in 'Opnieuw opstarten'.

   ![Azure Database voor MariaDB - Status opnieuw opstarten](./media/howto-restart-server-portal/4-restarting-status.png)

5. Controleer of het opnieuw opstarten van de server is gelukt.

   ![Azure Database voor MariaDB - Succes opnieuw opstarten](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Volgende stappen

[Snelstart: Azure-database maken voor MariaDB-server met Azure-portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)