---
title: vCore-resource limieten-één data base
description: Op deze pagina worden enkele veelvoorkomende limieten voor vCore bronnen voor één data base in Azure SQL Database beschreven.
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
ms.openlocfilehash: a8f62a24ff2c6571b5267fdbf4f23bd9e05ee499
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128679"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model

In dit artikel vindt u gedetailleerde resource limieten voor Azure SQL Database afzonderlijke data bases met behulp van het vCore-aankoop model.

Zie [overzicht van resource limieten op een SQL database-server](sql-database-resource-limits-database-server.md)voor de limieten voor DTU-aankoop modellen voor afzonderlijke data bases op een SQL database server.

U kunt de servicelaag, de reken grootte en de opslag hoeveelheid voor één data base instellen met behulp van de [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [Power shell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), de [Azure cli](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)of de [rest API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Zie [Eén data base schalen](sql-database-single-database-scale.md)voor richt lijnen en overwegingen voor schalen.

## <a name="general-purpose---serverless-compute---gen5"></a>Algemeen gebruik-serverloze Compute-GEN5

De [Compute-laag zonder server](sql-database-serverless.md) is momenteel alleen beschikbaar op GEN5-hardware.

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Reken grootte|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|
|Min-maximale vCores|0,5-1|0,5-2|0,5-4|0,75-6|1.0-8|
|Min-Maxi maal geheugen (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Minimale automatische onderbrekings vertraging (minuten)|60|60|60|60|60|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|512|1024|1024|1024|1536|
|Maximale logboek grootte (GB)|154|307|307|307|461|
|Maximale gegevens grootte TempDB (GB)|32|64|128|192|256|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|320|640|1280|1920|2560|
|Maximale logboek frequentie (MBps)|3.8|7.5|15|22.5|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|75|150|300|450|600|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|
|Min-maximale vCores|1,25-10|1,50-12|1,75-14|2.00-16|
|Min-Maxi maal geheugen (GB)|3,75-30|4.50-36|5,25-42|6,00-48|
|Minimale automatische onderbrekings vertraging (minuten)|60|60|60|60|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1536|3072|3072|3072|
|Maximale logboek grootte (GB)|461|461|461|922|
|Maximale gegevens grootte TempDB (GB)|320|384|448|512|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|3200|3840|4480|5120|
|Maximale logboek frequentie (MBps)|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|750|900|1050|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="hyperscale---provisioned-compute---gen4"></a>Grootschalige-ingerichte Compute-Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Prestatie niveau|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100|
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. gegevensiops *|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maximale logboek frequentie (MBps)|105 |105 |105 |105 |105 |105 |
|I/o-latentie (bij benadering)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Prestatie niveau|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. gegevensiops *|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maximale logboek frequentie (MBps)|105 |105 |105 |105 |105 |105 |
|I/o-latentie (bij benadering)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="hyperscale---provisioned-compute---gen5"></a>Grootschalige-ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Prestatie niveau|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|448|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. gegevensiops *|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maximale logboek frequentie (MBps)|105 |105 |105 |105 |105 |105 |105 |
|I/o-latentie (bij benadering)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Prestatie niveau|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |
|Max. gegevensiops *|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|Maximale logboek frequentie (MBps)|105 |105 |105 |105 |105 |105 |105 |
|I/o-latentie (bij benadering)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

#### <a name="notes"></a>Opmerkingen

**Opmerking 1**: grootschalige is een architectuur met meerdere lagen met afzonderlijke Compute-en opslag onderdelen: [grootschalige service tier-architectuur](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Opmerking 2**: grootschalige-architectuur met meerdere lagen heeft caching op meerdere niveaus. Effectief IOPS is afhankelijk van de werk belasting.

**Opmerking 3**: de latentie is 1-2 MS voor gegevens in de RBPEX-cache op basis van SSD op reken replica's, waarmee de meeste gebruikte gegevens pagina's in de cache worden opgeslagen. Hogere latentie voor gegevens die zijn opgehaald van pagina servers.

## <a name="general-purpose---provisioned-compute---gen4"></a>Algemeen beoogde, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Reken grootte|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|320|640|960|1280|1600|1920|
|Maximale logboek frequentie (MBps)|3.75|7.5|11.25|15|18,75|22.5|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Reken grootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|3072|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|922|1229|1229|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)
|Max. gegevensiops *|2240|2560|2880|3200|5120|7680|
|Maximale logboek frequentie (MBps)|26,3|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen beoogde, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Reken grootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|640|1280|1920|2560|3200|3840|4480|
|Maximale logboek frequentie (MBps)|7.5|15|22.5|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|5120|5760|6400|7680|10240|12800|25600|
|Maximale logboek frequentie (MBps)|30|30|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen beoogde, ingerichte Compute-Fsv2-serie

### <a name="fsv2-series-compute-generation-preview"></a>Generatie van Fsv2-Series (preview-versie)

|Reken grootte|GP_Fsv2_72|
|:--- | --: |
|Compute genereren|Fsv2-serie|
|vCores|72|
|Geheugen (GB)|136,2|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|1024|
|Maximale gegevens grootte TempDB (GB)|333|
|Opslagtype|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Max. gegevensiops *|12,800|
|Maximale logboek frequentie (MBps)|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|3600|
|Maximaal aantal gelijktijdige aanmeldingen|3600|
|Maximaal aantal gelijktijdige sessies|30,000|
|Aantal replica's|1|
|Multi-AZ|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen4"></a>Bedrijfs kritieke, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="gen4-compute-generation-part-1"></a>Gen4 Compute Generation (deel 1)

|Reken grootte|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|Maximale gegevens grootte TempDB (GB)|32|64|96|128|160|192|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|4,000|8,000|12,000|16,000|20,000|24,000|
|Maximale logboek frequentie (MBps)|8|16|24|32|40|48|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Reken grootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159,5|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|512|768|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS |28.000|32,000|36.000|40,000|64,000|76.800|
|Maximale logboek frequentie (MBps)|56|64|64|64|64|64|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal aantal gelijktijdige aanmeldingen (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfs kritieke, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Reken grootte|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|448|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|8000|16,000|24,000|32,000|40,000|48.000|56.000|
|Maximale logboek frequentie (MBps)|24|48|72|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|GEN5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|Maximale gegevens grootte TempDB (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|64,000|72.000|80,000|96.000|128.000|160.000|204.800|
|Maximale logboek frequentie (MBps)|96|96|96|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige aanmeldingen|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische-ingerichte Compute-M-serie

### <a name="m-series-compute-generation-preview"></a>Generatie van d-Series Compute (preview-versie)

|Reken grootte|BC_M_128|
|:--- | --: |
|Compute genereren|M-serie|
|vCores|128|
|Geheugen (GB)|3767,1|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|1768|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|2048|
|Maximale gegevens grootte TempDB (GB)|4096|
|Opslagtype|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Max. gegevensiops *|160.000|
|Maximale logboek frequentie (MBps)|264|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|12,800|
|Maximaal aantal gelijktijdige aanmeldingen|12,800|
|Maximaal aantal gelijktijdige sessies|30.000|
|Aantal replica's|4|
|Multi-AZ|Ja|
|Uitschalen voor leesbewerking|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|

\* de maximum waarde voor i/o-grootten tussen 8 KB en 64 KB. Werkelijke IOPS zijn werk belasting afhankelijk. Zie [Data io governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

> [!IMPORTANT]
> In sommige gevallen is het wellicht voor het verkleinen van een database voor het vrijmaken van ongebruikte ruimte. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](sql-database-vcore-resource-limits-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Voor DTU-resource limieten voor elastische Pools raadpleegt [u resource limieten voor elastische Pools met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [resource limieten voor beheerde](sql-database-managed-instance-resource-limits.md)exemplaren voor resource limieten voor beheerde instanties.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een database server.
