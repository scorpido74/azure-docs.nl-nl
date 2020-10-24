---
title: Logs-grootschalige (Citus)-Azure Database for PostgreSQL
description: Toegang krijgen tot database logboeken voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: fac0db7f8c94113ff87d1c0a3091e4c0cbd5ed2b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489843"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Meldt zich aan Azure Database for PostgreSQL-grootschalige (Citus)

PostgreSQL-logboeken zijn beschikbaar op elk knoop punt van een grootschalige (Citus)-Server groep. U kunt Logboeken verzenden naar een opslag server of naar een analyse service. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen.

## <a name="accessing-logs"></a>Logboeken openen

Om toegang te krijgen tot PostgreSQL-logboeken voor een grootschalige (Citus)-coördinator of worker-knoop punt, opent u het knoop punt in de Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lijst met knoop punten":::

Open voor het geselecteerde knoop punt **Diagnostische instellingen**en klik op **+ Diagnostische instelling toevoegen**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="lijst met knoop punten":::

Kies een naam voor de nieuwe diagnostische instellingen en schakel het selectie vakje **PostgreSQLLogs** in.  Kies de doel (en) die de logboeken moeten ontvangen.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="lijst met knoop punten":::

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met log Analytics-query's](../azure-monitor/log-query/get-started-portal.md)
- Meer informatie over [Azure Event hubs](../event-hubs/event-hubs-about.md)