---
title: vCore-resourcelimieten - elastische pools
description: Op deze pagina worden enkele veelvoorkomende vCore-bronlimieten beschreven voor elastische groepen in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: b3c5594b8eef76dcb57903408dd1e77c96890eab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346266"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Resourcelimieten voor elastische pools met het vCore-inkoopmodel

In dit artikel vindt u de gedetailleerde resourcelimieten voor elastische groepen en gepoolde databases van Azure SQL Database met behulp van het vCore-inkoopmodel.

Zie [SQL Database DTU-resourcelimieten voor](sql-database-dtu-resource-limits-elastic-pools.md)DTU-inkoopmodellen - elastische pools .

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

U de servicelaag, de rekengrootte en het opslagbedrag instellen met behulp van de [Azure-portal,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)de [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)of de [REST-API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Zie [Een elastische pool schalen](sql-database-elastic-pool-scale.md) voor schaalrichtlijnen en overwegingen

## <a name="general-purpose---provisioned-compute---gen4"></a>Algemeen doel - ingerichte compute - Gen4

> [!IMPORTANT]
> Nieuwe Gen4-databases worden niet langer ondersteund in de regio's Australië-Oost of Brazilië Zuid.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Servicelaag voor algemeen gebruik: Generatie 4 rekenplatform (deel 1)

|Rekengrootte|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|1|2|3|4|5|6|
|Geheugen (GB)|7|14|21|28|35|42|
|Maximum aantal DBs per poule <sup>1</sup>|100|200|500|500|500|500|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|512|756|1536|1536|1536|2048|
|Maximale logboekgrootte|154|227|461|461|461|614|
|TempDB max datagrootte (GB)|32|64|96|128|160|192|
|Opslagtype|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS per pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maximale logsnelheid per groep (MBps)|4.7|9.4|14.1|18.8|23.4|28.1|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup> |210|420|630|840|1050|1260|
|Maximale gelijktijdige aanmeldingen per groep <sup>3</sup> |210|420|630|840|1050|1260|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Servicetier voor algemeen gebruik: Generatie 4 rekenplatform (deel 2)

|Rekengrootte|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159.5|
|Maximum aantal DBs per poule <sup>1</sup>|500|500|500|500|500|500|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|2048|2048|2048|2048|3584|4096|
|Maximale logboekgrootte (GB)|614|614|614|614|1075|1229|
|TempDB max datagrootte (GB)|224|256|288|320|512|768|
|Opslagtype|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maximale logsnelheid per groep (MBps)|32.8|37.5|37.5|37.5|37.5|37.5|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximale gelijktijdige aanmeldingen pool (aanvragen) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Aantal replica's|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.    

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

## <a name="general-purpose---provisioned-compute---gen5"></a>Algemeen doel - ingerichte compute - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Servicelaag voor algemeen gebruik: Generatie 5 rekenplatform (deel 1)

|Rekengrootte|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|2|4|6|8|10|12|14|
|Geheugen (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximum aantal DBs per poule <sup>1</sup>|100|200|500|500|500|500|500|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|512|756|1536|1536|1536|2048|2048|
|Maximale logboekgrootte (GB)|154|227|461|461|461|614|614|
|TempDB max datagrootte (GB)|64|128|192|256|320|384|448|
|Opslagtype|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximale logsnelheid per groep (MBps)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Servicelaag voor algemeen gebruik: Generatie 5 rekenplatform (deel 2)

|Rekengrootte|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximum aantal DBs per poule <sup>1</sup>|500|500|500|500|500|500|500|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Maximale gegevensgrootte (GB)|2048|3072|3072|3072|4096|4096|4096|
|Maximale logboekgrootte (GB)|614|922|922|922|1229|1229|1229|
|TempDB max datagrootte (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|Premium (externe) opslag|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS per pool <sup>2</sup> |6,400|7,200|8,000|9600|12,800|16.000|32,000|
|Maximale logsnelheid per groep (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Aantal replica's|1|1|1|1|1|1|1|
|Multi-AZ|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Algemeen doel - ingerichte compute - Fsv2-serie

### <a name="fsv2-series-compute-generation-preview"></a>Compute generation uit fsv2-serie (preview)

|Rekengrootte|GP_Fsv2_72|
|:--- | --: |
|Compute generatie|Fsv2-serie|
|vCores|72|
|Geheugen (GB)|136.2|
|Maximum aantal DBs per poule <sup>1</sup>|500|
|Ondersteuning voor Columnstore|Ja|
|In-memory OLTP-opslag (GB)|N.v.t.|
|Maximale gegevensgrootte (GB)|4096|
|Maximale logboekgrootte (GB)|1024|
|TempDB max datagrootte (GB)|333|
|Opslagtype|Premium (externe) opslag|
|IO-latentie (bij benadering)|5-7 ms (schrijven)<br>5-10 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|16.000|
|Maximale logsnelheid per groep (MBps)|37.5|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|3780|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|3780|
|Maximaal aantal gelijktijdige sessies|30,000|
|Min/max elastic pool vCore-keuzes per database|0-72|
|Aantal replica's|1|
|Multi-AZ|N.v.t.|
|Uitschalen voor leesbewerking|N.v.t.|
|Inbegrepen back-upopslag|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

## <a name="business-critical---provisioned-compute---gen4"></a>Bedrijfskritische - ingerichte compute - Gen4

> [!IMPORTANT]
> Nieuwe Gen4-databases worden niet langer ondersteund in de regio's Australië-Oost of Brazilië Zuid.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Business critical service tier: Generation 4 compute platform (deel 1)

|Rekengrootte|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|2|3|4|5|6|
|Geheugen (GB)|14|21|28|35|42|
|Maximum aantal DBs per poule <sup>1</sup>|50|100|100|100|100|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|2|3|4|5|6|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevensgrootte (GB)|1024|1024|1024|1024|1024|
|Maximale logboekgrootte (GB)|307|307|307|307|307|
|TempDB max datagrootte (GB)|64|96|128|160|192|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|9000|13,500|18.000|22,500|27,000|
|Maximale logsnelheid per groep (MBps)|20|30|40|50|60|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|420|630|840|1050|1260|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|420|630|840|1050|1260|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Aantal replica's|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Business critical service tier: Generation 4 compute platform (deel 2)

|Rekengrootte|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Compute generatie|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|Gen4 Gen4|
|vCores|7|8|9|10|16|24|
|Geheugen (GB)|49|56|63|70|112|159.5|
|Maximum aantal DBs per poule <sup>1</sup>|100|100|100|100|100|100|
|Ondersteuning voor Columnstore|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|N.v.t.|
|In-memory OLTP-opslag (GB)|7|8|9.5|11|20|36|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|Maximale gegevensgrootte (GB)|1024|1024|1024|1024|1024|1024|
|Maximale logboekgrootte (GB)|307|307|307|307|307|307|
|TempDB max datagrootte (GB)|224|256|288|320|512|768|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|31,500|36,000|40,500|45.000|72,000|96,000|
|Maximale logsnelheid per groep (MBps)|70|80|80|80|80|80|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

## <a name="business-critical---provisioned-compute---gen5"></a>Bedrijfskritische - ingerichte compute - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Business critical service tier: Generation 5 compute platform (deel 1)

|Rekengrootte|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|4|6|8|10|12|14|
|Geheugen (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximum aantal DBs per poule <sup>1</sup>|50|100|100|100|100|100|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximale gegevensgrootte (GB)|1024|1536|1536|1536|3072|3072|
|Maximale logboekgrootte (GB)|307|307|461|461|922|922|
|TempDB max datagrootte (GB)|128|192|256|320|384|448|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|18.000|27,000|36,000|45.000|54,000|63,000|
|Maximale logsnelheid per groep (MBps)|60|90|120|120|120|120|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Aantal replica's|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Business critical service tier: Generation 5 compute platform (deel 2)

|Rekengrootte|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Compute generatie|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|Gen5 Gen5|
|vCores|16|18|20|24|32|40|80|
|Geheugen (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximum aantal DBs per poule <sup>1</sup>|100|100|100|100|100|100|100|
|Ondersteuning voor Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|In-memory OLTP-opslag (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.68|
|Maximale gegevensgrootte (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximale logboekgrootte (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datagrootte (GB)|512|576|640|768|1024|1280|2560|
|Opslagtype|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|72,000|81,000|90,000|108,000|144,000|180,000|256,000|
|Maximale logsnelheid per groep (MBps)|120|120|120|120|120|120|120|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximaal aantal gelijktijdige sessies|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastic pool vCore-keuzes per database|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Aantal replica's|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Uitschalen voor leesbewerking|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

## <a name="business-critical---provisioned-compute---m-series"></a>Bedrijfskritische - ingerichte compute - M-serie

### <a name="m-series-compute-generation-preview"></a>Rekengeneratie uit de M-serie (preview)

|Rekengrootte|BC_M_128|
|:--- | --: |
|Compute generatie|M-serie|
|vCores|128|
|Geheugen (GB)|3767.1|
|Maximum aantal DBs per poule <sup>1</sup>|100|
|Ondersteuning voor Columnstore|Ja|
|In-memory OLTP-opslag (GB)|1768|
|Maximale gegevensgrootte (GB)|4096|
|Maximale logboekgrootte (GB)|2048|
|TempDB max datagrootte (GB)|4096|
|Opslagtype|Lokale SSD|
|IO-latentie (bij benadering)|1-2 ms (schrijven)<br>1-2 ms (lees)|
|Max data IOPS per pool <sup>2</sup>|200.000|
|Maximale logsnelheid per groep (MBps)|333|
|Max gelijktijdige werknemers per groep (aanvragen) <sup>3</sup>|13,440|
|Maximale gelijktijdige aanmeldingen per groep (aanvragen) <sup>3</sup>|13,440|
|Maximaal aantal gelijktijdige sessies|30,000|
|Min/max elastic pool vCore-keuzes per database|0-128|
|Aantal replica's|4|
|Multi-AZ|Ja|
|Uitschalen voor leesbewerking|Ja|
|Inbegrepen back-upopslag|1x DB-formaat|

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> De maximale waarde voor IO-formaten varieert van 8 KB tot 64 KB. Werkelijke IOPS zijn afhankelijk van de werkbelasting. Zie Data [IO Governance](sql-database-resource-limits-database-server.md#resource-governance)voor meer informatie.

<sup>3</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

Als alle vCores van een elastische groep bezet zijn, ontvangt elke database in de groep een gelijke hoeveelheid rekenresources om query's te verwerken. De service SQL Database verdeelt resources eerlijk over databases door gelijke hoeveelheden rekentijd te garanderen. De eerlijkheid van elastic pool resource sharing is een aanvulling op elke hoeveelheid resource die anders aan elke database wordt gegarandeerd wanneer de vCore min per database is ingesteld op een niet-nulwaarde.

## <a name="database-properties-for-pooled-databases"></a>Database-eigenschappen voor gepoolde databases

In de volgende tabel worden de eigenschappen voor samengevoegde databases beschreven.

> [!NOTE]
> De resourcelimieten van afzonderlijke databases in elastische pools zijn over het algemeen hetzelfde als voor afzonderlijke databases buiten groepen met dezelfde rekengrootte. De maximale gelijktijdige werknemers voor een GP_Gen4_1 database zijn bijvoorbeeld 200 werknemers. Dus, de max gelijktijdige werknemers voor een database in een GP_Gen4_1 pool is ook 200 werknemers. Let op, het totale aantal gelijktijdige werknemers in GP_Gen4_1 pool is 210.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Max. vCores per database |Het maximum aantal vCores dat een database in de groep kan gebruiken, indien beschikbaar op basis van het gebruik door andere databases in de groep. Max vCores per database is geen brongarantie voor een database. Het is een algemene instelling voor alle databases in de groep. Stel max vCores per database hoog genoeg in om pieken in databasegebruik te verwerken. Er wordt een zekere mate van overcommitting verwacht, omdat de groep over het algemeen uitgaat van warme en koude gebruikspatronen voor databases waar niet alle databases tegelijkertijd pieken.|
| Min vCores per database |Het minimum aantal vCores dat elke database in de pool is gegarandeerd. Het is een algemene instelling voor alle databases in de groep. De min vCores per database kunnen worden ingesteld op 0 en is ook de standaardwaarde. Deze eigenschap is ingesteld op ergens tussen 0 en het gemiddelde vCores-gebruik per database. Het product van het aantal databases in de groep en de min vCores per database mag niet hoger zijn dan de vCores per groep.|
| Maximale opslag per database |De maximale databasegrootte die door de gebruiker is ingesteld voor een database in een groep. Gepoolde databases delen toegewezen poolopslag, zodat de grootte die een database kan bereiken beperkt is tot de kleinere van de resterende opslag van de groep en de databasegrootte. Maximale databasegrootte verwijst naar de maximale grootte van de gegevensbestanden en omvat niet de ruimte die wordt gebruikt door logbestanden. |
|||

## <a name="next-steps"></a>Volgende stappen

- Zie [resourcelimieten voor afzonderlijke databases met het vCore-inkoopmodel](sql-database-vcore-resource-limits-single-databases.md) voor vCore-resourcelimieten voor één database
- Zie [resourcelimieten voor afzonderlijke databases met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resourcelimieten voor één database
- Zie resourcelimieten voor elastische pools voor DTU-resourcelimieten [met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md)voor resourcelimieten voor beheerde instanties .
- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over algemene Azure-limieten.
- Zie [overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md) voor informatie over limieten op de server en het abonnement voor informatie over resourcelimieten op een databaseserver.
