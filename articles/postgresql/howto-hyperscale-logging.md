---
title: Logs-grootschalige (Citus)-Azure Database for PostgreSQL
description: Toegang krijgen tot database logboeken voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097271"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Meldt zich aan Azure Database for PostgreSQL-grootschalige (Citus)

PostgreSQL-logboeken zijn beschikbaar op elk knoop punt van een grootschalige (Citus)-Server groep. U kunt Logboeken verzenden naar een opslag server of naar een analyse service. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen.

## <a name="accessing-logs"></a>Logboeken openen

Om toegang te krijgen tot PostgreSQL-logboeken voor een grootschalige (Citus)-coördinator of worker-knoop punt, opent u het knoop punt in de Azure Portal:

![lijst met knoop punten](media/howto-hyperscale-logging/choose-node.png)

Open voor het geselecteerde knoop punt **Diagnostische instellingen**en klik op **+ Diagnostische instelling toevoegen**.

![Knop Diagnostische instellingen toevoegen](media/howto-hyperscale-logging/diagnostic-settings.png)

Kies een naam voor de nieuwe diagnostische instellingen en schakel het selectie vakje **PostgreSQLLogs** in.  Kies de doel (en) die de logboeken moeten ontvangen.

![PostgreSQL-logboeken kiezen](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met log Analytics-query's](/azure/azure-monitor/log-query/get-started-portal)
- Meer informatie over [Azure Event hubs](/azure/event-hubs/event-hubs-about)
