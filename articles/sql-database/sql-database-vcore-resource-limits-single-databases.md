---
title: Azure SQL Database resource limieten op basis van vCore-één data base | Microsoft Docs
description: Op deze pagina worden enkele algemene vCore bronnen voor één data base in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/22/2019
ms.openlocfilehash: 661ac9ea3fd87268c43bf0a0eba66e30f636fc77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566214"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Resource limieten voor afzonderlijke data bases met behulp van het op vCore gebaseerde aankoop model

In dit artikel vindt u gedetailleerde resource limieten voor Azure SQL Database afzonderlijke data bases met behulp van het op vCore gebaseerde aankoop model.

Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md)voor het op DTU gebaseerde aankoop model limieten voor afzonderlijke data bases op een SQL database server.

> [!IMPORTANT]
> In sommige gevallen is het wellicht voor het verkleinen van een database voor het vrijmaken van ongebruikte ruimte. Zie voor meer informatie, [bestandsruimte in Azure SQL Database beheren](sql-database-file-space-management.md).

U kunt de servicelaag, de reken grootte en de opslag hoeveelheid voor één data base instellen met behulp van de [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [Power shell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), de [Azure cli](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)of de [rest API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Zie [Eén data base schalen](sql-database-single-database-scale.md)voor richt lijnen en overwegingen voor schalen.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Algemeen servicelaag: Opslag grootten en berekenings grootten

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de AustraliaEast-regio.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Algemeen servicelaag: Generatie 4 Compute platform (deel 1)

|Reken grootte|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generatie|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximale gegevens grootte (GB)|1024|1024|1024|1536|1536|1536|
|Maximale logboek grootte (GB)|307|307|307|461|461|461|
|TempDB-grootte (GB)|32|64|96|128|160|192|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Frequentie limieten voor logboeken (MBps)|3.75|7.5|11.25|15|18,75|22.5|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Uitschalen lezen|N/A|N/A|N/A|N/A|N/A|N/A|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Algemeen servicelaag: Generatie 4 Compute platform (deel 2)

|Reken grootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generatie|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|168|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximale gegevens grootte (GB)|1536|3072|3072|3072|4096|4096|
|Maximale logboek grootte (GB)|461|922|922|922|1229|1229|
|TempDB-grootte (GB)|224|256|288|320|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)
|Doel-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Frequentie limieten voor logboeken (MBps)|26,25|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Uitschalen lezen|N/A|N/A|N/A|N/A|N/A|N/A|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Algemeen servicelaag: Generatie 5 Compute platform (deel 1)

|Reken grootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generatie|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|307|461|461|461|461|
|TempDB-grootte (GB)|64|128|192|256|320|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Frequentie limieten voor logboeken (MBps)|3.75|7.5|11.25|15|18,75|22.5|26,25|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|30000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Uitschalen lezen|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Algemeen servicelaag: Generatie 5 Compute platform (deel 2)

|Reken grootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generatie|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-grootte (GB)|384|384|384|384|384|384|384|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Frequentie limieten voor logboeken (MBps)|30|30|30|30|30|30|30|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|30000|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Uitschalen lezen|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="serverless-compute-tier"></a>Serverloze compute-laag

De [Compute-laag zonder server](sql-database-serverless.md) is in Preview en is alleen voor afzonderlijke data bases die gebruikmaken van het vCore-aankoop model.

#### <a name="generation-5-compute-platform"></a>Generatie 5 Compute platform

|Reken grootte|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|H/W-generatie|5|5|5|
|Min-maximale vCores|0,5-1|0,5-2|0,5-4|
|Min-Maxi maal geheugen (GB)|2.02-3|2.05-6|2.10-12|
|Minimale automatische onderbrekings vertraging (uren)|6|6|6|
|Column Store-ondersteuning|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|
|Maximale gegevens grootte (GB)|512|1024|1024|
|Maximale logboek grootte (GB)|12|24|48|
|TempDB-grootte (GB)|32|64|128|
|Opslagtype|Premium-opslag (extern)|Premium-opslag (extern)|Premium-opslag (extern)|
|I/o-latentie (bij benadering)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|5-7 MS (schrijven)<br>5-10 MS (lezen)|
|Doel-IOPS (64 KB)|500|1000|2000|
|Frequentie limieten voor logboeken (MBps)|2.5|5.6|10|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|75|150|300|
|Maxi maal toegestane sessies|30000|30000|30000|
|Aantal replica's|1|1|1|
|Multi-AZ|N/A|N/A|N/A|
|Uitschalen lezen|N/A|N/A|N/A|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>De servicelaag Bedrijfskritiek voor de ingerichte Compute-laag

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de AustraliaEast-regio.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Bedrijfskritiek servicelaag: Generatie 4 Compute platform (deel 1)

|Reken grootte|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generatie|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|650|650|650|650|650|650|
|Maximale logboek grootte (GB)|195|195|195|195|195|195|
|TempDB-grootte (GB)|32|64|96|128|160|192|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|5000|10.000|15.000|20000|25000|30000|
|Frequentie limieten voor logboeken (MBps)|8|16|24|32|40|48|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Bedrijfskritiek servicelaag: Generatie 4 Compute platform (deel 2)

|Reken grootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generatie|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|168|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|7|8|9,5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevens grootte (GB)|650|650|650|650|1024|1024|
|Maximale logboek grootte (GB)|195|195|195|195|307|307|
|TempDB-grootte (GB)|224|256|288|320|384|384|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Frequentie limieten voor logboeken (MBps)|56|64|64|64|64|64|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal aantal gelijktijdige aanmeldingen (aanvragen)|1400|1600|1800|2000|3200|4800|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Bedrijfskritiek servicelaag: Generatie 5 Compute platform (deel 1)

|Reken grootte|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generatie|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximale gegevens grootte (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximale logboek grootte (GB)|307|307|307|461|461|922|922|
|TempDB-grootte (GB)|64|128|192|256|320|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Frequentie limieten voor logboeken (MBps)|12|24|36|48|60|72|84|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|600|800|1000|1200|1400|
|Maximaal aantal gelijktijdige aanmeldingen|200|400|600|800|1000|1200|1400|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|30000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Bedrijfskritiek servicelaag: Generatie 5 Compute platform (deel 2)

|Reken grootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generatie|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximale gegevens grootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboek grootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-grootte (GB)|384|384|384|384|384|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|I/o-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|1-2 ms (schrijven)<br>1-2 ms (lezen)|
|Doel-IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Frequentie limieten voor logboeken (MBps)|96|96|96|96|96|96|96|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|1600|1800|2000|2400|3200|4000|8000|
|Maximaal aantal gelijktijdige aanmeldingen|1600|1800|2000|2400|3200|4000|8000|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|30000|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|1X-DB-grootte|

## <a name="hyperscale-service-tier"></a>Hyperscale-servicelaag

### <a name="generation-5-compute-platform"></a>Generatie 5 Compute platform

|Prestatieniveau|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W-generatie|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Geheugen (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Column Store-ondersteuning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-opslag in het geheugen (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximale gegevens grootte (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximale logboek grootte (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB-grootte (GB)|64|128|256|384|384|384|384|384|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Doel-IOPS (64 KB)| [Opmerking 1](#note-1) |[Opmerking 1](#note-1)|[Opmerking 1](#note-1) |[Opmerking 1](#note-1) |[Opmerking 1](#note-1) |[Opmerking 1](#note-1) |[Opmerking 1](#note-1) | [Opmerking 1](#note-1) |
|I/o-latentie (bij benadering)|Te bepalen|Te bepalen|Te bepalen|Te bepalen|Te bepalen|Te bepalen|Te bepalen|Te bepalen|
|Maxi maal aantal gelijktijdige werk nemers (aanvragen)|200|400|800|1600|2400|3200|4000|8000|
|Maxi maal toegestane sessies|30000|30000|30000|30000|30000|30000|30000|30000|
|Aantal replica's|2|2|2|2|2|2|2|2|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Uitschalen lezen|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Opgenomen back-upopslag |7|7|7|7|7|7|7|7|
|||

### <a name="note-1"></a>Opmerking 1

Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPS is afhankelijk van de werk belasting.

### <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het op DTU gebaseerde aankoop model](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het op vCore gebaseerde inkoop model](sql-database-vcore-resource-limits-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Zie resource limieten voor elastische Pools [met behulp van het op DTU gebaseerde aankoop model voor de](sql-database-dtu-resource-limits-elastic-pools.md) limieten voor DTU-bronnen voor elastische Pools
- Zie [resource limieten voor beheerde](sql-database-managed-instance-resource-limits.md)exemplaren voor resource limieten voor beheerde instanties.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een database server.
