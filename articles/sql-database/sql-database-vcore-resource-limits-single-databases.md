---
title: vCore-resourcelimieten - enkele database
description: Op deze pagina worden enkele veelvoorkomende vCore-bronlimieten beschreven voor één database in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481062"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resourcelimieten voor afzonderlijke databases met behulp van het vCore-inkoopmodel

In dit artikel vindt u de gedetailleerde resourcelimieten voor afzonderlijke databases van Azure SQL Database met behulp van het vCore-inkoopmodel.

Zie [Overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md)voor DTU-aankoopmodellimieten voor afzonderlijke databases op een SQL-databaseserver.

U de servicelaag, de rekengrootte en het opslagbedrag voor één database instellen met behulp van de [Azure-portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases) [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)de [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)of de [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Zie [Eén database schalen](sql-database-single-database-scale.md)voor het schalen van richtlijnen en overwegingen.

## <a name="general-purpose---serverless-compute---gen5"></a>Algemeen doel - serverless compute - Gen5

De [serverloze compute-laag](sql-database-serverless.md) is momenteel alleen beschikbaar op Gen5-hardware.

### <a name="gen5-compute-generation-part-1"></a>Gen5 rekengeneratie (deel 1)

|Rekengrootte|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|Min-max vCores|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Min-max geheugen (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Min automatisch onderbreken vertraging (minuten)|60|60|60|60|60|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|512|1024|1024|1024|1536|
|Maximale logboekgrootte (GB)|154|307|307|307|461|
|TempDB max datagrootte (GB)|32|64|128|192|256|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|320|640|1280|1920|2560|
|Maximale logsnelheid (MBps)|3.8|7,5|15|22.5|30|
|Max gelijktijdige werknemers (aanvragen)|75|150|300|450|600|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>Gen5 rekengeneratie (deel 2)

|Rekengrootte|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|Min-max vCores|1.25-10|1.50-12|1.75-14|2.00-16|
|Min-max geheugen (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Min automatisch onderbreken vertraging (minuten)|60|60|60|60|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|1536|3072|3072|3072|
|Maximale logboekgrootte (GB)|461|461|461|922|
|TempDB max datagrootte (GB)|320|384|448|512|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|3200|3840|4480|5120|
|Maximale logsnelheid (MBps)|30|30|30|30|
|Max gelijktijdige werknemers (aanvragen)|750|900|1050|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="hyperscale---provisioned-compute---gen4"></a>Hyperscale - ingerichte compute - Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 rekengeneratie (deel 1)

|Prestatieniveau|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|[RBPEX RBPEX](sql-database-service-tier-hyperscale.md#compute) Grootte|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (TB)|100 |100 |100 |100 |100 |100|
|Maximale loggrootte (TB)|1 |1 |1 |1 |1 |1 |
|TempDB max datagrootte (GB)|32|64|96|128|160|192|
|Opslagtype| [Noot 1](#notes) |[Noot 1](#notes)|[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |
|Max data IOPS *|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|
|Maximale logsnelheid (MBps)|100 |100 |100 |100 |100 |100 |
|IO-latentie (bij benadering)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opslagbehoud van back-ups|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 rekengeneratie (deel 2)

|Prestatieniveau|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159.5|
|[RBPEX RBPEX](sql-database-service-tier-hyperscale.md#compute) Grootte|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (TB)|100 |100 |100 |100 |100 |100 |
|Maximale loggrootte (TB)|1 |1 |1 |1 |1 |1 |
|TempDB max datagrootte (GB)|224|256|288|320|512|768|
|Opslagtype| [Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |
|Max data IOPS *|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|
|Maximale logsnelheid (MBps)|100 |100 |100 |100 |100 |100 |
|IO-latentie (bij benadering)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|
|Max gelijktijdige werknemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opslagbehoud van back-ups|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="hyperscale---provisioned-compute---gen5"></a>Hyperscale - ingerichte compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 rekengeneratie (deel 1)

|Prestatieniveau|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX RBPEX](sql-database-service-tier-hyperscale.md#compute) Grootte|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximale loggrootte (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB max datagrootte (GB)|64|128|192|256|320|384|448|
|Opslagtype| [Noot 1](#notes) |[Noot 1](#notes)|[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |
|Max data IOPS *|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|
|Maximale logsnelheid (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-latentie (bij benadering)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opslagbehoud van back-ups|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>Gen5 rekengeneratie (deel 2)

|Prestatieniveau|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX RBPEX](sql-database-service-tier-hyperscale.md#compute) Grootte|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|3x geheugen|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximale loggrootte (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB max datagrootte (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype| [Noot 1](#notes) |[Noot 1](#notes)|[Noot 1](#notes)|[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |[Noot 1](#notes) |
|Max data IOPS *|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|[Noot 2](#notes)|
|Maximale logsnelheid (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-latentie (bij benadering)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|[Noot 3](#notes)|
|Max gelijktijdige werknemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opslagbehoud van back-ups|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

#### <a name="notes"></a>Opmerkingen

**Opmerking 1**: Hyperscale is een architectuur met meerdere niveaus met afzonderlijke reken- en opslagcomponenten: [Hyperscale Service Tier Architecture](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Opmerking 2**: Hyperscale multi-tiered architectuur heeft caching op meerdere niveaus. Effectieve IOPS is afhankelijk van de werkbelasting.

**Opmerking 3**: Latentie is 1-2 ms voor gegevens in de RBPEX SSD-gebaseerde cache op compute replica's, die de meeste gebruikte gegevenspagina's in de cache smeert. Hogere latentie voor gegevens die van paginaservers worden opgehaald.

## <a name="general-purpose---provisioned-compute---gen4"></a>Algemeen doel - ingerichte compute - Gen4

> [!IMPORTANT]
> Nieuwe Gen4-databases worden niet langer ondersteund in de regio's Australië-Oost of Brazilië Zuid.

### <a name="gen4-compute-generation-part-1"></a>Gen4 rekengeneratie (deel 1)

|Rekengrootte|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|1024|1024|1536|1536|1536|3072|
|Maximale logboekgrootte (GB)|307|307|461|461|461|922|
|TempDB max datagrootte (GB)|32|64|96|128|160|192|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|320|640|960|1280|1600|1920|
|Maximale logsnelheid (MBps)|3,75|7,5|11.25|15|18.75|22.5|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 rekengeneratie (deel 2)

|Rekengrootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159.5|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|3072|3072|3072|3072|4096|4096|
|Maximale logboekgrootte (GB)|922|922|922|922|1229|1229|
|TempDB max datagrootte (GB)|224|256|288|320|512|768|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)
|Max data IOPS *|2240|2560|2880|3200|5120|7680|
|Maximale logsnelheid (MBps)|26.3|30|30|30|30|30|
|Max gelijktijdige werknemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen doel - ingerichte compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 rekengeneratie (deel 1)

|Rekengrootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboekgrootte (GB)|307|307|461|461|461|922|922|
|TempDB max datagrootte (GB)|64|128|192|256|320|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|640|1280|1920|2560|3200|3840|4480|
|Maximale logsnelheid (MBps)|7,5|15|22.5|30|30|30|30|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>Gen5 rekengeneratie (deel 2)

|Rekengrootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboekgrootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datagrootte (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|5120|5760|6400|7680|10240|12800|25600|
|Maximale logsnelheid (MBps)|30|30|30|30|30|30|30|
|Max gelijktijdige werknemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen doel - ingerichte compute - Fsv2-serie

### <a name="fsv2-series-compute-generation-preview"></a>Compute generation uit fsv2-serie (preview)

|Rekengrootte|GP_Fsv2_72|
|:--- | --: |
|Compute generatie|Fsv2-serie|
|vCores|72|
|Geheugen (GB)|136.2|
|Ondersteuning voor Columnstore|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|
|Maximale gegevensgrootte (GB)|4096|
|Maximale logboekgrootte (GB)|1024|
|TempDB max datagrootte (GB)|333|
|Opslagtype|Externe SSD|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS *|12,800|
|Maximale logsnelheid (MBps)|30|
|Max gelijktijdige werknemers (aanvragen)|3600|
|Maximaal aantal gelijktijdige aanmeldingen|3600|
|Maximaal aantal gelijktijdige sessies|30,000|
|Aantal replica's|1|
|Multi-AZ|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen4"></a>Bedrijfskritische - ingerichte compute - Gen4

> [!IMPORTANT]
> Nieuwe Gen4-databases worden niet langer ondersteund in de regio's Australië-Oost of Brazilië Zuid.

### <a name="gen4-compute-generation-part-1"></a>Gen4 rekengeneratie (deel 1)

|Rekengrootte|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|1|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevensgrootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboekgrootte (GB)|307|307|307|307|307|307|
|TempDB max datagrootte (GB)|32|64|96|128|160|192|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS *|4000|8,000|12,000|16.000|20.000|24,000|
|Maximale logsnelheid (MBps)|8|16|24|32|40|48|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 rekengeneratie (deel 2)

|Rekengrootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159.5|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevensgrootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboekgrootte (GB)|307|307|307|307|307|307|
|TempDB max datagrootte (GB)|224|256|288|320|512|768|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS |28,000|32,000|36,000|40,000|64,000|76.800|
|Maximale logsnelheid (MBps)|56|64|64|64|64|64|
|Max gelijktijdige werknemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Max gelijktijdige aanmeldingen (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfskritische - ingerichte compute - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 rekengeneratie (deel 1)

|Rekengrootte|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximale gegevensgrootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboekgrootte (GB)|307|307|461|461|461|922|922|
|TempDB max datagrootte (GB)|64|128|192|256|320|384|448|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS *|8000|16.000|24,000|32,000|40,000|48,000|56,000|
|Maximale logsnelheid (MBps)|24|48|72|96|96|96|96|
|Max gelijktijdige werknemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>Gen5 rekengeneratie (deel 2)

|Rekengrootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.64|
|Maximale gegevensgrootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboekgrootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datagrootte (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS *|64,000|72,000|80,000|96,000|128,000|160.000|204,800|
|Maximale logsnelheid (MBps)|96|96|96|96|96|96|96|
|Max gelijktijdige werknemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige aanmeldingen|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische - ingerichte compute - M-serie

### <a name="m-series-compute-generation-preview"></a>Rekengeneratie uit de M-serie (preview)

|Rekengrootte|BC_M_128|
|:--- | --: |
|Compute generatie|M-serie|
|vCores|128|
|Geheugen (GB)|3767.1|
|Ondersteuning voor Columnstore|Ja|
|In-memory OLTP-opslag (GB)|1768|
|Maximale gegevensgrootte (GB)|4096|
|Maximale logboekgrootte (GB)|2048|
|TempDB max datagrootte (GB)|4096|
|Opslagtype|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS *|160.000|
|Maximale logsnelheid (MBps)|264|
|Max gelijktijdige werknemers (aanvragen)|12,800|
|Maximaal aantal gelijktijdige aanmeldingen|12,800|
|Maximaal aantal gelijktijdige sessies|30.000|
|Aantal replica's|4|
|Multi-AZ|Ja|
|Uitschalen voor leesbewerking|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|

\*De maximale waarde voor IO-formaten variërend van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [resourcelimieten voor afzonderlijke databases met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resourcelimieten voor één database
- Zie resourcelimieten voor elastische pools voor vCore-resourcelimieten [met behulp van het vCore-inkoopmodel](sql-database-vcore-resource-limits-elastic-pools.md)
- Zie resourcelimieten voor elastische pools voor DTU-resourcelimieten [met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md)voor resourcelimieten voor beheerde instanties .
- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over algemene Azure-limieten.
- Zie [overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md) voor informatie over limieten op de server en het abonnement voor informatie over resourcelimieten op een databaseserver.
