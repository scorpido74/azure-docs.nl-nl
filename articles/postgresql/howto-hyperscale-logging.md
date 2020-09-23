---
title: Logs-grootschalige (Citus)-Azure Database for PostgreSQL
description: Toegang krijgen tot database logboeken voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895870"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Meldt zich aan Azure Database for PostgreSQL-grootschalige (Citus)

PostgreSQL-logboeken zijn beschikbaar op elk knoop punt van een grootschalige (Citus)-Server groep. U kunt Logboeken verzenden naar een opslag server of naar een analyse service. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen.

## <a name="accessing-logs"></a>Logboeken openen

Om toegang te krijgen tot PostgreSQL-logboeken voor een grootschalige (Citus)-coördinator of worker-knoop punt, opent u het knoop punt in de Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lijst met knoop punten":::

Open voor het geselecteerde knoop punt **Diagnostische instellingen**en klik op **+ Diagnostische instelling toevoegen**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Knop Diagnostische instellingen toevoegen":::

Kies een naam voor de nieuwe diagnostische instellingen en schakel het selectie vakje **PostgreSQLLogs** in.  Kies de doel (en) die de logboeken moeten ontvangen.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-logboeken kiezen":::

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met log Analytics-query's](/azure/azure-monitor/log-query/get-started-portal)
- Meer informatie over [Azure Event hubs](/azure/event-hubs/event-hubs-about)
