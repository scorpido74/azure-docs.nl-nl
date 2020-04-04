---
title: Geheugen- en gelijktijdigheidslimieten
description: Bekijk de geheugen- en gelijktijdigheidslimieten die zijn toegewezen aan de verschillende prestatieniveaus en resourceklassen in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 56ab49949b4ea2a92bc591042b2d43a7f7b2dc63
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632668"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Geheugen- en gelijktijdigheidslimieten voor Azure Synapse Analytics

Bekijk de geheugen- en gelijktijdigheidslimieten die zijn toegewezen aan de verschillende prestatieniveaus en resourceklassen in Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Capaciteitsinstellingen voor gegevensmagazijnen

In de volgende tabellen wordt de maximale capaciteit voor het gegevensmagazijn op verschillende prestatieniveaus weergegeven. Zie [Schaalgegevensberekenen - portal](quickstart-scale-compute-portal.md)als u het prestatieniveau wilt wijzigen.

### <a name="service-levels"></a>Serviceniveaus

De serviceniveaus variëren van DW100c tot DW30000c.

| Prestatieniveau | Rekenknooppunten | Distributies per Compute-knooppunt | Geheugen per gegevensmagazijn (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Het maximale serviceniveau is DW30000c, dat 60 Compute-knooppunten en één distributie per Compute-knooppunt heeft. Een gegevensmagazijn van 600 TB bij DW30000c verwerkt bijvoorbeeld ongeveer 10 TB per Compute-knooppunt.

## <a name="concurrency-maximums-for-workload-groups"></a>Gelijktijdigheidsmaxima voor werkbelastingsgroepen

Met de invoering van [werkbelastingsgroepen](sql-data-warehouse-workload-isolation.md)is het concept van gelijktijdigheidssleuven niet langer van toepassing.  Resources per aanvraag worden toegewezen op basis van een percentage en gespecificeerd in de definitie van werkbelastinggroep.  Echter, zelfs met het verwijderen van gelijktijdigheid slots, zijn er minimale hoeveelheden resources nodig per query's op basis van het serviceniveau.  In de onderstaande tabel wordt de minimale hoeveelheid resources gedefinieerd die nodig zijn per query tussen serviceniveaus en de bijbehorende gelijktijdigheid die kan worden bereikt.

|Serviceniveau|Maximale gelijktijdige query's|Min % ondersteund voor REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12.5%|
|DW300c|12|8%|
|DW400c|16|6.25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Gelijktijdigheidsmaxima voor resourceklassen

Om ervoor te zorgen dat elke query voldoende resources heeft om efficiënt uit te voeren, houdt SQL Analytics in Azure Synapse het gebruik van resources bij door gelijktijdigheidssleuven toe te kennen aan elke query. Het systeem plaatst query's in een wachtrij op basis van belang en gelijktijdige sleuven. Query's wachten in de wachtrij tot er voldoende gelijktijdige sleuven beschikbaar zijn. [Belang](sql-data-warehouse-workload-importance.md) en gelijktijdigheid slots bepalen CPU prioritering. Zie [Uw werkbelasting analyseren](analyze-your-workload.md) voor meer informatie

**Statische resourceklassen**

In de volgende tabel worden de maximale gelijktijdige query's en gelijktijdige sleuven voor elke [statische resourceklasse weergegeven](resource-classes-for-workload-management.md).  

| Serviceniveau | Maximale gelijktijdige query's | Gelijktijdigheidslots beschikbaar | Sleuven gebruikt door staticrc10 | Sleuven gebruikt door staticrc20 | Sleuven gebruikt door staticrc30 | Sleuven gebruikt door staticrc40 | Sleuven gebruikt door staticrc50 | Sleuven gebruikt door staticrc60 | Sleuven gebruikt door staticrc70 | Sleuven gebruikt door staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamische resourceklassen**

In de volgende tabel worden de maximale gelijktijdige query's en gelijktijdige sleuven voor elke [dynamische resourceklasse weergegeven](resource-classes-for-workload-management.md). Dynamische resourceklassen gebruiken een toewijzing van 3-10-22-70 geheugenpercentages voor kleine-middelgrote-grote resourceklassen voor alle serviceniveaus.

| Serviceniveau | Maximale gelijktijdige query's | Gelijktijdigheidslots beschikbaar | Slots gebruikt door smallrc | Sleuven gebruikt door mediumrc | Sleuven gebruikt door biggerc | Sleuven gebruikt door xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Als er niet genoeg gelijktijdige sleuven zijn om query-uitvoering te starten, worden query's in de wachtrij geplaatst en uitgevoerd op basis van het belang.  Als er een gelijkwaardig belang is, worden query's uitgevoerd op een first-in, first-out basis.  Als query's zijn voltooid en het aantal query's en sleuven onder de limieten valt, brengt SQL Data Warehouse wachtrijquery's uit.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen voor meer informatie over het gebruik van resourceklassen om uw werklast verder te optimaliseren:

* [Resourceklassen voor werkbelastingbeheer](resource-classes-for-workload-management.md)
* [Uw werkbelasting analyseren](analyze-your-workload.md)
