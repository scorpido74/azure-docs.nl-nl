---
title: Server opnieuw opstarten - Azure portal - Azure Database for PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u een Azure Database voor PostgreSQL - Single Server opnieuw starten met behulp van de Azure-portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770081"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure Database opnieuw starten voor PostgreSQL - Eén server met de Azure-portal
In dit onderwerp wordt beschreven hoe u een Azure Database voor PostgreSQL-server opnieuw starten. Mogelijk moet u uw server opnieuw opstarten om onderhoudsredenen, wat een korte storing veroorzaakt wanneer de server de bewerking uitvoert.

De herstart van de server wordt geblokkeerd als de service bezet is. De service verwerkt bijvoorbeeld mogelijk een eerder aangevraagde bewerking, zoals het schalen van vCores.
 
De tijd die nodig is om een herstart te voltooien, is afhankelijk van het PostgreSQL-herstelproces. Om de herstarttijd te verminderen, raden we u aan de hoeveelheid activiteit die zich op de server voor de herstart voordoet te minimaliseren.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding te voltooien, moet u het:
- Een [Azure-database voor PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Server opnieuw opstarten uitvoeren

De volgende stappen starten de PostgreSQL-server:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw Azure-database voor PostgreSQL-server.

2. Klik op de werkbalk van de **pagina Overzicht** van de server op **Opnieuw starten**.

   ![Azure Database voor PostgreSQL - Overzicht - Knop Opnieuw opstarten](./media/howto-restart-server-portal/2-server.png)

3. Klik **op Ja** om het opnieuw opstarten van de server te bevestigen.

   ![Azure Database voor PostgreSQL - Opnieuw starten bevestigen](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Houd er rekening mee dat de serverstatus wordt gewijzigd in 'Opnieuw opstarten'.

   ![Azure Database voor PostgreSQL - Status opnieuw opstarten](./media/howto-restart-server-portal/4-restarting-status.png)

5. Controleer of het opnieuw opstarten van de server is gelukt.

   ![Azure Database voor PostgreSQL - Succes opnieuw opstarten](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van parameters in Azure Database voor PostgreSQL](howto-configure-server-parameters-using-portal.md)