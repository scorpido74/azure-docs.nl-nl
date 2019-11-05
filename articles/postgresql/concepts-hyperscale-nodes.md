---
title: Knoop punten in Azure Database for PostgreSQL – grootschalige (Citus)
description: Meer informatie over de twee typen knoop punten, coördinator en werk nemers, in een server groep in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 5348c8bbe432cd024a8da93ca866ae4458f956e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468090"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Knoop punten in Azure Database for PostgreSQL – grootschalige (Citus)

Met het hosting type grootschalige (Citus) kunnen Azure Database for PostgreSQL-servers (knoop punten) worden gecoördineerd met elkaar in een architectuur met het gedeelde Nothing. De knoop punten in een server groep bevatten gezamenlijk meer gegevens en gebruiken meer CPU-kernen dan op één server mogelijk zijn. Met de architectuur kan de data base ook worden geschaald door meer knoop punten toe te voegen aan de Server groep.

## <a name="coordinator-and-workers"></a>Coördinator en werk nemers

Elke server groep heeft een coördinator knooppunt en meerdere werk rollen. Toepassingen sturen hun query's naar het coördinator knooppunt, die het doorstuurt naar de relevante werk nemers en de resultaten samenvoegt. Toepassingen kunnen niet rechtstreeks verbinding maken met werk nemers.

Voor elke query stuurt de coördinator deze om naar één worker-knoop punt, of parallelizes deze op verschillende manieren, afhankelijk van het feit of de vereiste gegevens zich op één knoop punt of meerdere bevinden. De coördinator beslist wat er moet gebeuren met tabellen met advies voor de meta gegevens. Deze tabellen volgen de DNS-namen en de status van worker-knoop punten en de distributie van gegevens tussen knoop punten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over hoe knoop punten [gedistribueerde gegevens](concepts-hyperscale-distributed-data.md) opslaan
