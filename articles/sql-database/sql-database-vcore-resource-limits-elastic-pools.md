---
title: 'vCore-resource limieten: elastische Pools'
description: Op deze pagina worden enkele algemene limieten voor vCore bronnen voor elastische Pools in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 11/25/2019
ms.openlocfilehash: 74cc13386befa5cd97900b6b36d07d3144d9b727
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534209"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Resource limieten voor elastische Pools met behulp van het vCore-aankoop model

In dit artikel vindt u gedetailleerde resource limieten voor Azure SQL Database elastische Pools en gepoolde data bases met behulp van het vCore-aankoop model.

Zie [SQL database DTU-resource limieten-elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md)voor meer informatie over de limieten voor DTU-aankoop modellen.

> [!IMPORTANT]
> In sommige gevallen is het wellicht voor het verkleinen van een database voor het vrijmaken van ongebruikte ruimte. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

U kunt de servicelaag, de reken grootte en de opslag hoeveelheid instellen met behulp van de [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [Power shell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), de [Azure cli](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)of de [rest API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Zie [een elastische pool schalen](sql-database-elastic-pool-scale.md) voor schaal baarheid en overwegingen

## <a name="general-purpose---provisioned-compute---gen4"></a>Algemeen beoogde, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Service tier voor algemeen gebruik: generatie 4 Compute platform (deel 1)

|Reken grootte|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Maximumaantal databases per pool|100|200|500|500|500|500|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|512|756|1536|1536|1536|2048|
|Maximale logboek grootte|154|227|461|461|461|614|
|TempDB-grootte (GB)|32|64|96|128|160|192|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Frequentie limieten voor logboeken (MBps)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) * |210|420|630|840|1050|1260|
|Maxi maal aantal gelijktijdige aanmeldingen per pool * |210|420|630|840|1050|1260|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Service tier voor algemeen gebruik: generatie 4 Compute platform (deel 2)

|Reken grootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|168|
|Maximumaantal databases per pool|500|500|500|500|500|500|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|2048|2048|2048|2048|3584|4096|
|Maximale logboek grootte (GB)|614|614|614|614|1075|1229|
|TempDB-grootte (GB)|224|256|288|320|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Frequentie limieten voor logboeken (MBps)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1470|1680|1890|2100|3360|5040|
|Maximum aantal gelijktijdige aanmeldings groepen (aanvragen) *|1470|1680|1890|2100|3360|5040|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen beoogde, ingerichte Compute-GEN5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Service tier voor algemeen gebruik: generatie 5 Compute platform (deel 1)

|Reken grootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maximumaantal databases per pool|100|200|500|500|500|500|500|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|512|756|1536|1536|1536|2048|2048|
|Maximale logboek grootte (GB)|154|227|461|461|461|614|614|
|TempDB-grootte (GB)|64|128|192|256|320|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Frequentie limieten voor logboeken (MBps)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|210|420|630|840|1050|1260|1470|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|210|420|630|840|1050|1260|1470|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Service tier voor algemeen gebruik: generatie 5 Compute platform (deel 2)

|Reken grootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximumaantal databases per pool|500|500|500|500|500|500|500|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevens grootte (GB)|2048|3072|3072|3072|4096|4096|4096|
|Maximale logboek grootte (GB)|614|922|922|922|1229|1229|1229|
|TempDB-grootte (GB)|384|384|384|384|384|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Frequentie limieten voor logboeken (MBps)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1680|1890|2100|2520|3360|4200|8400|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|1680|1890|2100|2520|3360|4200|8400|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen beoogde, ingerichte Compute-Fsv2-serie

### <a name="fsv2-series-compute-generation-preview"></a>Generatie van Fsv2-Series (preview-versie)

|Reken grootte|GP_Fsv2_72|
|:--- | --: |
|Compute genereren|Fsv2-serie|
|vCores|72|
|Geheugen (GB)|136|
|Maximumaantal databases per pool|500|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|N.v.t.|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|1024|
|Maximale gegevens grootte TempDB (GB)|333|
|Opslagtype|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|36000|
|Frequentie limieten voor logboeken (MBps)|37,5|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1680|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|1680|
|Min/max vCore keuzen voor elastische pool per data base|0-72|
|Aantal replica's|1|
|Multi-AZ|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|
|Opgenomen back-upopslag|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

## <a name="business-critical---provisioned-compute---gen4"></a>Bedrijfs kritieke, ingerichte Compute-Gen4

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Bedrijfskritische servicelaag: generatie 4 Compute platform (deel 1)

|Reken grootte|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|2|3|4|5|6|
|Geheugen (GB)|14|21|28|35|42|
|Maximumaantal databases per pool|100|100|100|100|100|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|
|TempDB-grootte (GB)|64|96|128|160|192|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|10.000|15.000|20000|25000|30.000|
|Frequentie limieten voor logboeken (MBps)|20|30|40|50|60|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|420|630|840|1050|1260|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|420|630|840|1050|1260|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Aantal replica's|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Bedrijfskritische servicelaag: generatie 4 Compute platform (deel 2)

|Reken grootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute genereren|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|
|Maximumaantal databases per pool|100|100|100|100|100|100|
|Column Store-ondersteuning|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|OLTP-opslag in het geheugen (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboek grootte (GB)|307|307|307|307|307|307|
|TempDB-grootte (GB)|224|256|288|320|384|384|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Frequentie limieten voor logboeken (MBps)|70|80|80|80|80|80|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1470|1680|1890|2100|3360|5040|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|1470|1680|1890|2100|3360|5040|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfs kritieke, ingerichte Compute-GEN5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Bedrijfskritische servicelaag: generatie 5 Compute platform (deel 1)

|Reken grootte|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|4|6|8|10|12|14|
|Geheugen (GB)|20,4|30,6|40,8|51|61,2|71,4|
|Maximumaantal databases per pool|100|100|100|100|100|100|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximale gegevens grootte (GB)|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|461|461|922|922|
|TempDB-grootte (GB)|128|192|256|320|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|10.000|15.000|20000|25000|30.000|35000|
|Frequentie limieten voor logboeken (MBps)|30|45|60|75|90|105|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|420|630|840|1050|1260|1470|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|420|630|840|1050|1260|1470|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Bedrijfskritische servicelaag: generatie 5 Compute platform (deel 2)

|Reken grootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute genereren|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximumaantal databases per pool|100|100|100|100|100|100|100|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-grootte (GB)|384|384|384|384|384|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Frequentie limieten voor logboeken (MBps)|120|120|120|120|120|120|120|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1680|1890|2100|2520|3360|4200|8400|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|1680|1890|2100|2520|3360|4200|8400|
|Maxi maal toegestane sessies|30.000|30.000|30.000|30.000|30.000|30.000|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische-ingerichte Compute-M-serie

### <a name="m-series-compute-generation-preview"></a>Generatie van d-Series Compute (preview-versie)

|Reken grootte|GP_M_128|
|:--- | --: |
|Compute genereren|M-serie|
|vCores|128|
|Geheugen (GB)|3767|
|Maximumaantal databases per pool|100|
|Column Store-ondersteuning|Ja|
|OLTP-opslag in het geheugen (GB)|481|
|Maximale gegevens grootte (GB)|4096|
|Maximale logboek grootte (GB)|2048|
|Maximale gegevens grootte TempDB (GB)|4096|
|Opslagtype|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|40000|
|Frequentie limieten voor logboeken (MBps)|120|
|Maxi maal aantal gelijktijdige werk nemers per pool (aanvragen) *|1680|
|Maxi maal aantal gelijktijdige aanmeldingen per groep (aanvragen) *|1680|
|Maxi maal toegestane sessies|30.000|
|Min/max vCore keuzen voor elastische pool per data base|0-128|
|Aantal replica's|4|
|Multi-AZ|Ja|
|Uitschalen voor leesbewerking|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|



Zie de [resource limieten voor één data](sql-database-vcore-resource-limits-single-databases.md)Base voor het maximum aantal gelijktijdige werk rollen (aanvragen) voor elke afzonderlijke data base. \* Als de elastische pool bijvoorbeeld gebruikmaakt van GEN5 en het maximum aantal vCore per data base 2 is, is het maximum aantal gelijktijdige werkers 200.  Als het maximum aantal vCore per data base 0,5 is, is het maximum aantal gelijktijdige werkers 50 sinds er op GEN5 een maximum van 100 gelijktijdige werk nemers per vCore.  Voor andere maximale vCore-instellingen per data base die minder dan 1 vCore of minder zijn, is het aantal gelijktijdige werk nemers op dezelfde manier opnieuw geschaald.

Als alle vCores van een elastische pool bezet zijn, ontvangt elke data base in de pool een gelijke hoeveelheid reken bronnen om query's te verwerken. De service SQL Database verdeelt resources eerlijk over databases door gelijke hoeveelheden rekentijd te garanderen. De verdeling voor het delen van elastische pool bronnen is een aanvulling op alle bronnen, anders wordt elke Data Base gegarandeerd wanneer de vCore min per data base is ingesteld op een andere waarde dan nul.

## <a name="database-properties-for-pooled-databases"></a>Data base-eigenschappen voor gegroepeerde Data bases

De volgende tabel beschrijft de eigenschappen voor gepoolde data bases.

> [!NOTE]
> De resource limieten van afzonderlijke data bases in elastische Pools zijn in het algemeen hetzelfde als voor afzonderlijke data bases buiten Pools met dezelfde reken grootte. Zo is het maximum aantal gelijktijdige werk rollen voor een GP_Gen4_1-data base 200 werk nemers. Daarom is het maximum aantal gelijktijdige werk rollen voor een data base in een GP_Gen4_1 groep ook 200 werk nemers. Opmerking: het totale aantal gelijktijdige werk rollen in GP_Gen4_1 pool is 210.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Maximum aantal vCores per data base |Het maximum aantal vCores dat elke data base in de pool mag gebruiken, indien beschikbaar op basis van het gebruik door andere data bases in de pool. Het maximale aantal vCores per data base is geen resource garantie voor een Data Base. Het is een algemene instelling voor alle databases in de groep. Stel het maximum aantal vCores per data base in dat hoog genoeg is om pieken in het database gebruik te verwerken. Er wordt een zekere mate van het door voeren van de wijzigingen verwacht omdat de groep in het algemeen gaat uitgaan van warme en koude gebruiks patronen voor data bases waarin alle data bases niet tegelijkertijd worden gepiekd.|
| Min vCores per data base |Het minimale aantal vCores dat elke data base in de pool wordt gegarandeerd. Het is een algemene instelling voor alle databases in de groep. Het minimum aantal vCores per data base kan worden ingesteld op 0 en is ook de standaard waarde. Deze eigenschap wordt ingesteld op elke wille keurige locatie tussen 0 en het gemiddelde vCores-gebruik per data base. Het product van het aantal data bases in de pool en de minimale vCores per data base mogen niet groter zijn dan de vCores per pool.|
| Maximale opslag per data base |De maximale database grootte die is ingesteld door de gebruiker voor een data base in een pool. Gegroepeerde Data bases delen toegewezen pool opslag, zodat de grootte van een Data Base beperkt is tot de kleinere opslag ruimte van de resterende pool en de grootte van de data base. De maximale database grootte verwijst naar de maximale grootte van de gegevens bestanden en bevat niet de ruimte die wordt gebruikt door de logboek bestanden. |
|||

## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model](sql-database-vcore-resource-limits-single-databases.md) voor vCore resource limieten voor één data base
- Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resource limieten voor één data base
- Voor DTU-resource limieten voor elastische Pools raadpleegt [u resource limieten voor elastische Pools met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [resource limieten voor beheerde](sql-database-managed-instance-resource-limits.md)exemplaren voor resource limieten voor beheerde instanties.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een database server.
