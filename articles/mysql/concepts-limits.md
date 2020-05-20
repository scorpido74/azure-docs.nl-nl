---
title: Beperkingen-Azure Database for MySQL
description: In dit artikel worden beperkingen beschreven in Azure Database for MySQL, zoals het aantal opties voor de verbinding en de opslag engine.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9cf5c958a0dd9a19e6b976ff36a18c45e062f604
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659923"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Beperkingen in Azure Database for MySQL
In de volgende secties worden capaciteit, ondersteuning voor opslag-engine, ondersteuning van bevoegdheden, ondersteuning voor gegevens manipulatie en functionele limieten in de database service beschreven. Zie ook [algemene beperkingen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) die van toepassing zijn op de MySQL-data base-engine.

## <a name="server-parameters"></a>Serverparameters

De minimum-en maximum waarden van verschillende populaire server parameters worden bepaald door de prijs categorie en vCores. Raadpleeg de onderstaande tabellen voor limieten.

### <a name="max_connections"></a>max_connections

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Algemeen gebruik|2|300|10|600|
|Algemeen gebruik|4|625|10|1250|
|Algemeen gebruik|8|1250|10|2500|
|Algemeen gebruik|16|2500|10|5000|
|Algemeen gebruik|32|5000|10|10.000|
|Algemeen gebruik|64|10.000|10|20.000|
|Geoptimaliseerd geheugen|2|600|10|800|
|Geoptimaliseerd geheugen|4|1250|10|2500|
|Geoptimaliseerd geheugen|8|2500|10|5000|
|Geoptimaliseerd geheugen|16|5000|10|10.000|
|Geoptimaliseerd geheugen|32|10.000|10|20.000|

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> FOUT 1040 (08004): te veel verbindingen

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals ProxySQL om verbindingen efficiënt te beheren.

Het maken van nieuwe client verbindingen met MySQL vergt tijd en deze verbindingen nemen database bronnen in beslag, zelfs wanneer ze niet actief zijn. De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)voor meer informatie over het instellen van ProxySQL.

### <a name="query_cache_size"></a>query_cache_size

De query cache is standaard uitgeschakeld. Configureer de para meter om de query-cache in te scha kelen `query_cache_type` . 

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) voor meer informatie over deze para meter.

> [!NOTE]
> De query cache is afgeschaft vanaf MySQL 5.7.20 installeren en is verwijderd in MySQL 8,0

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|0|0|16777216|
|Algemeen gebruik|4|0|0|33554432|
|Algemeen gebruik|8|0|0|67108864|
|Algemeen gebruik|16|0|0|134217728|
|Algemeen gebruik|32|0|0|134217728|
|Algemeen gebruik|64|0|0|134217728|
|Geoptimaliseerd geheugen|2|0|0|33554432|
|Geoptimaliseerd geheugen|4|0|0|67108864|
|Geoptimaliseerd geheugen|8|0|0|134217728|
|Geoptimaliseerd geheugen|16|0|0|134217728|
|Geoptimaliseerd geheugen|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) voor meer informatie over deze para meter.

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|524288|32768|4194304|
|Algemeen gebruik|4|524288|32768|8388608|
|Algemeen gebruik|8|524288|32768|16777216|
|Algemeen gebruik|16|524288|32768|33554432|
|Algemeen gebruik|32|524288|32768|33554432|
|Algemeen gebruik|64|524288|32768|33554432|
|Geoptimaliseerd geheugen|2|524288|32768|8388608|
|Geoptimaliseerd geheugen|4|524288|32768|16777216|
|Geoptimaliseerd geheugen|8|524288|32768|33554432|
|Geoptimaliseerd geheugen|16|524288|32768|33554432|
|Geoptimaliseerd geheugen|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) voor meer informatie over deze para meter.

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|262144|128|268435455|
|Algemeen gebruik|4|262144|128|536870912|
|Algemeen gebruik|8|262144|128|1073741824|
|Algemeen gebruik|16|262144|128|2147483648|
|Algemeen gebruik|32|262144|128|4294967295|
|Algemeen gebruik|64|262144|128|4294967295|
|Geoptimaliseerd geheugen|2|262144|128|536870912|
|Geoptimaliseerd geheugen|4|262144|128|1073741824|
|Geoptimaliseerd geheugen|8|262144|128|2147483648|
|Geoptimaliseerd geheugen|16|262144|128|4294967295|
|Geoptimaliseerd geheugen|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) voor meer informatie over deze para meter.

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|16777216|16384|268435455|
|Algemeen gebruik|4|16777216|16384|536870912|
|Algemeen gebruik|8|16777216|16384|1073741824|
|Algemeen gebruik|16|16777216|16384|2147483648|
|Algemeen gebruik|32|16777216|16384|4294967295|
|Algemeen gebruik|64|16777216|16384|4294967295|
|Geoptimaliseerd geheugen|2|16777216|16384|536870912|
|Geoptimaliseerd geheugen|4|16777216|16384|1073741824|
|Geoptimaliseerd geheugen|8|16777216|16384|2147483648|
|Geoptimaliseerd geheugen|16|16777216|16384|4294967295|
|Geoptimaliseerd geheugen|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) voor meer informatie over deze para meter.

|**Prijs categorie**|**vCore (s)**|**Standaard waarde**|**Minimum waarde**|**Maximum waarde**|
|---|---|---|---|---|
|Basic|1|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Basic|2|Kan niet worden geconfigureerd in de Basic-laag|N.v.t.|N.v.t.|
|Algemeen gebruik|2|16777216|1024|67108864|
|Algemeen gebruik|4|16777216|1024|134217728|
|Algemeen gebruik|8|16777216|1024|268435456|
|Algemeen gebruik|16|16777216|1024|536870912|
|Algemeen gebruik|32|16777216|1024|1073741824|
|Algemeen gebruik|64|16777216|1024|1073741824|
|Geoptimaliseerd geheugen|2|16777216|1024|134217728|
|Geoptimaliseerd geheugen|4|16777216|1024|268435456|
|Geoptimaliseerd geheugen|8|16777216|1024|536870912|
|Geoptimaliseerd geheugen|16|16777216|1024|1073741824|
|Geoptimaliseerd geheugen|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

De tijdzone tabellen kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. Raadpleeg de [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) -of [Azure cli](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -artikelen voor informatie over het aanroepen van de opgeslagen procedure en het instellen van de tijd zones globaal of sessie niveau.

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL slaat de tabel InnoDB op in verschillende tablespaces op basis van de configuratie die u hebt ingevoerd tijdens het maken van de tabel. De [systeem-tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) is het opslag gebied voor de InnoDB-data dictionary. Een [bestand-per-tabel tabel ruimte](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) bevat gegevens en indexen voor één InnoDB-tabel en wordt opgeslagen in het bestands systeem in een eigen gegevens bestand. Dit gedrag wordt bepaald door de `innodb_file_per_table` para meter server. Instelling `innodb_file_per_table` om `OFF` ervoor te zorgen dat InnoDB tabellen maakt in de ruimte van het systeem. Anders maakt InnoDB tabellen in file-per-tabel tablespaces.

Azure Database for MySQL ondersteunt Maxi maal **1 TB**in één gegevens bestand. Als de grootte van de data base groter is dan 1 TB, maakt u de tabel in [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabel ruimte. Als u één tabel grootte hebt die groter is dan 1 TB, moet u de partitie tabel gebruiken.

## <a name="storage-engine-support"></a>Ondersteuning voor opslag engine

### <a name="supported"></a>Ondersteund
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [GEHEUGENMETABASE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Niet ondersteund
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [FAXBERICHTEN](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federatie](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Ondersteuning van bevoegdheden

### <a name="unsupported"></a>Niet ondersteund
- DBA-rol: veel server parameters en instellingen kunnen per ongeluk de prestaties van de server afnemen of ACID-eigenschappen van het DBMS ontzeggen. Als zodanig, voor het onderhouden van de service-integriteit en SLA op een product niveau, geeft deze service de rol van DBA niet weer. Het standaard gebruikers account, dat wordt gebouwd wanneer een nieuwe data base-instantie wordt gemaakt, stelt die gebruiker in staat om het meren deel van de DDL-en DML-instructies in het beheerde data base-exemplaar uit te voeren. 
- SUPER bevoegdheid: een soort gelijke [Super bevoegdheid](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is ook beperkt.
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of gebruikt u de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.

## <a name="data-manipulation-statement-support"></a>Ondersteuning voor gegevens manipulatie-instructies

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE`wordt ondersteund, maar de `[LOCAL]` para meter moet worden opgegeven en worden omgeleid naar een UNC-pad (Azure-opslag gekoppeld aan SMB).

### <a name="unsupported"></a>Niet ondersteund
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="scale-operations"></a>Schaal bewerkingen
- Dynamisch schalen naar en van de basis prijs categorieën wordt momenteel niet ondersteund.
- Het verminderen van de grootte van de server opslag wordt niet ondersteund.

### <a name="server-version-upgrades"></a>Server versie-upgrades
- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund. Als u wilt upgraden naar de volgende primaire versie, neemt u een [dump op en herstelt](./concepts-migrate-dump-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Wanneer u de functie PITR gebruikt, wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- Het MySQL-Server exemplaar geeft de verkeerde Server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht om de juiste engine versie van Server exemplaar op te halen `select version();` .

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde MySQL-database versies](concepts-supported-versions.md)
