---
title: Knoop punten – grootschalige (Citus)-Azure Database for PostgreSQL
description: Meer informatie over de twee typen knoop punten, coördinator en werk nemers, in een server groep in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382794"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Knoop punten in Azure Database for PostgreSQL – grootschalige (Citus)

Met het hosting type grootschalige (Citus) kunnen Azure Database for PostgreSQL-servers (knoop punten) worden gecoördineerd met elkaar in een architectuur met het gedeelde Nothing. De knoop punten in een server groep bevatten gezamenlijk meer gegevens en gebruiken meer CPU-kernen dan op één server mogelijk zijn. Met de architectuur kan de data base ook worden geschaald door meer knoop punten toe te voegen aan de Server groep.

## <a name="coordinator-and-workers"></a>Coördinator en werk nemers

Elke server groep heeft een coördinator knooppunt en meerdere werk rollen. Toepassingen sturen hun query's naar het coördinator knooppunt, die het doorstuurt naar de relevante werk nemers en de resultaten samenvoegt. Toepassingen kunnen niet rechtstreeks verbinding maken met werk nemers.

Met grootschalige (Citus) kan de database beheerder tabellen *distribueren* , waarbij verschillende rijen op verschillende werk knooppunten worden opgeslagen. Gedistribueerde tabellen zijn de belangrijkste voor het grootschalige van de prestaties. Als het niet lukt om tabellen te distribueren, worden ze volledig op het coördinator knooppunt uitgevoerd en kunnen ze niet profiteren van parallellisme op meerdere machines.

Voor elke query op gedistribueerde tabellen stuurt de coördinator deze om naar één worker-knoop punt, of parallelizes deze op verschillende manieren, afhankelijk van het feit of de vereiste gegevens zich op één knoop punt of meerdere hebben voordoen. De coördinator beslist wat er moet gebeuren met tabellen met advies voor de meta gegevens. Deze tabellen volgen de DNS-namen en de status van worker-knoop punten en de distributie van gegevens tussen knoop punten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [gedistribueerde tabellen](concepts-hyperscale-distributed-data.md)
