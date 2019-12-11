---
title: Hoge Beschik baarheid – grootschalige (Citus)-Azure Database for PostgreSQL
description: Concepten voor hoge Beschik baarheid en herstel na nood gevallen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975530"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hoge Beschik baarheid in Azure Database for PostgreSQL – grootschalige (Citus)

Hoge beschikbaarheid vermijdt downtime van databases omdat voor elk knooppunt in een servergroep stand-byreplica's worden onderhouden. Als een knooppunt uitvalt, worden met Hyperscale de binnenkomende verbindingen van het foutieve knooppunt overgeschakeld naar het bijbehorende stand-byknooppunt. Failover wordt binnen enkele minuten uitgevoerd en knooppunten die een niveau zijn verhoogd, beschikken altijd over de nieuwste gegevens dankzij synchrone streamingreplicatie van PostgreSQL.

Om te profiteren van HA op het coördinator knooppunt, moeten database toepassingen de verbindingen en mislukte trans acties detecteren en opnieuw proberen. De zojuist gepromoveerde coördinator is toegankelijk met dezelfde connection string.

Herstel kan worden onderverdeeld in drie fasen: detectie, failover en volledig herstel.  Grootschalige voert periodieke status controles uit op elk knoop punt en na vier mislukte controles wordt vastgesteld dat een knoop punt niet beschikbaar is. Grootschalige bevordert vervolgens een stand-by-status voor primaire knoop punt (failover) en richt een nieuwe stand-by-to--uit.
Streaming-replicatie begint, waardoor het nieuwe knoop punt up-to-date is.  Wanneer alle gegevens zijn gerepliceerd, is het knoop punt volledig herstel bereikt.

### <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van hoge Beschik baarheid](howto-hyperscale-high-availability.md) in een grootschalige-Server groep.
