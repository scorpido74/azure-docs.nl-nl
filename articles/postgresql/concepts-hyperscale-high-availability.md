---
title: Hoge Beschik baarheid in Azure Database for PostgreSQL – grootschalige (Citus)
description: Concepten voor hoge Beschik baarheid en herstel na nood gevallen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514743"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hoge Beschik baarheid in Azure Database for PostgreSQL – grootschalige (Citus)

Hoge Beschik baarheid (HA) vermijdt downtime van de data base door stand-by replica's van elk knoop punt in een server groep te behouden. Als een knoop punt uitvalt, schakelt grootschalige de binnenkomende verbindingen van het knoop punt met de fout naar de stand-bymodus. Failover vindt binnen enkele minuten plaats en gepromoveerde knoop punten bevatten altijd nieuwe gegevens via synchrone replicatie van PostgreSQL.

Om te profiteren van HA op het coördinator knooppunt, moeten database toepassingen de verbindingen en mislukte trans acties detecteren en opnieuw proberen. De zojuist gepromoveerde coördinator is toegankelijk met dezelfde connection string.

Herstel kan worden onderverdeeld in drie fasen: detectie, failover en volledig herstel.  Grootschalige voert periodieke status controles uit op elk knoop punt en na vier mislukte controles wordt vastgesteld dat een knoop punt niet beschikbaar is. Grootschalige bevordert vervolgens een stand-by-status voor primaire knoop punt (failover) en richt een nieuwe stand-by-to--uit.
Streaming-replicatie begint, waardoor het nieuwe knoop punt up-to-date is.  Wanneer alle gegevens zijn gerepliceerd, is het knoop punt volledig herstel bereikt.

### <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van hoge Beschik baarheid](howto-hyperscale-high-availability.md) in een grootschalige-Server groep.
