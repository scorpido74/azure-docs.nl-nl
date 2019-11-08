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
ms.reviewer: ''
ms.date: 11/04/2019
ms.openlocfilehash: 11d9115893f8d0632110d86a671a6590ca48e220
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822335"
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
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|
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
|Maximum aantal gegevens IOPS (64 KB)|500|1000|2000|3000|4000|
|Maximale logboek frequentie (MBps)|2,5|5,6|10|15|20|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|75|150|300|450|600|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|
|Min-maximale vCores|1,25-10|1,50-12|1,75-14|2.00-16|
|Min-Maxi maal geheugen (GB)|3,75-30|4.50-36|5,25-42|6,00-48|
|Minimale automatische onderbrekings vertraging (minuten)|60|60|60|60|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1536|1536|1536|3072|
|Maximale logboek grootte (GB)|461|461|461|922|
|Maximale gegevens grootte TempDB (GB)|320|384|448|512|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|5000|6000|7000|8000|
|Maximale logboek frequentie (MBps)|20|20|20|20|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|750|900|1050|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|


## <a name="hyperscale---provisioned-compute---gen5"></a>Grootschalige-ingerichte Compute-GEN5

### <a name="gen5-compute-generation"></a>GEN5 Compute genereren

|Prestatieniveau|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|8|16|24|32|40|80|
|Geheugen (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Size|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|3X-geheugen|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Maximale gegevens grootte TempDB (GB)|64|128|256|384|384|384|384|384|
|Opslagtype| [Opmerking 1](#notes) |[Opmerking 1](#notes)|[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) |[Opmerking 1](#notes) | [Opmerking 1](#notes) |
|Maximum aantal gegevens IOPS (64 KB)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|[Opmerking 2](#notes)|
|I/o-latentie (bij benadering)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|[Opmerking 3](#notes)|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|800|1600|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Secundaire replica's|0-4|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Back-upopslag bewaren|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|7 dagen|
|||

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
|Maximum aantal gegevens IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Maximale logboek frequentie (MBps)|3,75|7,5|11,25|15|18,75|22,5|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Reken grootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|168|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|3072|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|922|1229|1229|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)
|Maximum aantal gegevens IOPS (64 KB)|3500|4000|4500|5000|8000|12000|
|Maximale logboek frequentie (MBps)|26,25|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen beoogde, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Reken grootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Maximale logboek frequentie (MBps)|3,75|7,5|11,25|15|18,75|22,5|26,25|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|Maximale gegevens grootte TempDB (GB)|384|384|384|384|384|384|384|
|Opslagtype|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|8000|9000|10.000|12000|16000|20000|40000|
|Maximale logboek frequentie (MBps)|30|30|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|1|1|1|1|1|1|1|
|Meerdere AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen lezen|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen beoogde, ingerichte Compute-Fsv2-serie

### <a name="fsv2-series-compute-generation-preview"></a>Generatie van Fsv2-Series (preview-versie)

|Reken grootte|GP_Fsv2_72|
|:--- | --: |
|Compute genereren|Fsv2-serie|
|vCores|72|
|Geheugen (GB)|136|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|1024|
|Maximale gegevens grootte TempDB (GB)|333|
|Opslagtype|Externe SSD|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|36000|
|Maximale logboek frequentie (MBps)|30|
|Maxi maal aantal gelijktijdige werk nemers (vragen)|3600|
|Maximaal aantal gelijktijdige sessies|30.000|
|Aantal replica's|1|
|Meerdere AZ|N.v.t.|
|Uitschalen lezen|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|



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
|Maximum aantal gegevens IOPS (64 KB)|5000|10.000|15.000|20000|25000|30.000|
|Maximale logboek frequentie (MBps)|8|16|24|32|40|48|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="gen4-compute-generation-part-2"></a>Gen4 Compute Generation (deel 2)

|Reken grootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|168|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|Maximale gegevens grootte TempDB (GB)|224|256|288|320|384|384|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximale logboek frequentie (MBps)|56|64|64|64|64|64|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal aantal gelijktijdige aanmeldingen (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfs kritieke, ingerichte Compute-GEN5

### <a name="gen5-compute-generation-part-1"></a>GEN5 Compute Generation (deel 1)

|Reken grootte|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|461|922|922|
|Maximale gegevens grootte TempDB (GB)|64|128|192|256|320|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Maximale logboek frequentie (MBps)|12|24|36|48|60|72|84|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="gen5-compute-generation-part-2"></a>GEN5 Compute Generation (deel 2)

|Reken grootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|Maximale gegevens grootte TempDB (GB)|384|384|384|384|384|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Maximale logboek frequentie (MBps)|96|96|96|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige aanmeldingen|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Aantal replica's|4|4|4|4|4|4|4|
|Meerdere AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|


## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische-ingerichte Compute-M-serie

### <a name="m-series-compute-generation-preview"></a>Generatie van d-Series Compute (preview-versie)

|Reken grootte|GP_M_128|
|:--- | --: |
|Compute genereren|M-serie|
|vCores|128|
|Geheugen (GB)|3767|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|481|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|2048|
|Maximale gegevens grootte TempDB (GB)|4096|
|Opslagtype|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Maximum aantal gegevens IOPS (64 KB)|204800|
|Maximale logboek frequentie (MBps)|192|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|12800|
|Maximaal aantal gelijktijdige sessies|30.000|
|Aantal replica's|4|
|Meerdere AZ|Ja|
|Uitschalen lezen|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|




> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](sql-database-vcore-resource-limits-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Voor DTU-resource limieten voor elastische Pools raadpleegt [u resource limieten voor elastische Pools met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [resource limieten voor beheerde](sql-database-managed-instance-resource-limits.md)exemplaren voor resource limieten voor beheerde instanties.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een database server.
