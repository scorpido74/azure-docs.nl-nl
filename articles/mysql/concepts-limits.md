---
title: Beperkingen - Azure Database voor MySQL
description: In dit artikel worden beperkingen beschreven in Azure Database voor MySQL, zoals het aantal verbindings- en opslagengineopties.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 6ca09ab0578fb88e443d6e9e1f920c22457eb042
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548474"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Beperkingen in Azure Database voor MySQL
In de volgende secties worden capaciteit, ondersteuning van de opslagengine, ondersteuning voor bevoegdheden, ondersteuning voor gegevensmanipulatie-instructieen en functionele limieten in de databaseservice beschreven. Zie ook [algemene beperkingen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) die van toepassing zijn op de MySQL-databaseengine.

## <a name="server-parameters"></a>Serverparameters

De minimum- en maximumwaarden van verschillende populaire serverparameters worden bepaald door de prijscategorie en vCores. Raadpleeg de onderstaande tabellen voor limieten.

### <a name="max_connections"></a>max_connections

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
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

Wanneer verbindingen de limiet overschrijden, ontvangt u mogelijk de volgende fout:
> FOUT 1040 (08004): Te veel verbindingen

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een verbindingspooler zoals ProxySQL te gebruiken om verbindingen efficiënt te beheren.

Het maken van nieuwe clientverbindingen met MySQL kost tijd en eenmaal tot stand gebracht, bezetten deze verbindingen databasebronnen, zelfs wanneer deze niet actief zijn. De meeste toepassingen vragen om veel kortstondige verbindingen, die deze situatie verbindingen. Het resultaat is minder resources beschikbaar voor uw werkelijke werklast, wat leidt tot verminderde prestaties. Een verbindingspooler die niet-actieve verbindingen vermindert en bestaande verbindingen opnieuw gebruikt, helpt dit te voorkomen. Ga voor meer informatie over het instellen van ProxySQL naar onze [blogpost.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

De querycache is standaard uitgeschakeld. Als u de querycache `query_cache_type` wilt inschakelen, configureert u de parameter. 

Bekijk de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) voor meer informatie over deze parameter.

> [!NOTE]
> De querycache is afgeschaft vanaf MySQL 5.7.20 en is verwijderd in MySQL 8.0

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
|---|---|---|---|---|
|Basic|1|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
|Basic|2|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
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

Bekijk de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) voor meer informatie over deze parameter.

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
|---|---|---|---|---|
|Basic|1|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
|Basic|2|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
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

Bekijk de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) voor meer informatie over deze parameter.

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
|---|---|---|---|---|
|Basic|1|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
|Basic|2|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
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

Bekijk de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) voor meer informatie over deze parameter.

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
|---|---|---|---|---|
|Basic|1|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
|Basic|2|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
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

Bekijk de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) voor meer informatie over deze parameter.

|**Prijsniveau**|**vCore(s)**|**Standaardwaarde**|**Min waarde**|**Maximale waarde**|
|---|---|---|---|---|
|Basic|1|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
|Basic|2|Niet configureerbaar in basislaag|N.v.t.|N.v.t.|
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

De tijdzonetabellen kunnen worden ingevuld `mysql.az_load_timezone` door de opgeslagen procedure aan te roepen vanuit een hulpprogramma zoals de MySQL-opdrachtregel of MySQL Workbench. Raadpleeg de [Azure-portal-](howto-server-parameters.md#working-with-the-time-zone-parameter) of [Azure CLI-artikelen](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) voor het aanroepen van de opgeslagen procedure en het instellen van de tijdzones op globaal of sessieniveau.

## <a name="storage-engine-support"></a>Ondersteuning voor opslagmotoren

### <a name="supported"></a>Ondersteund
- [Innodb](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [Geheugen](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Niet ondersteund
- [Myisam](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [ZWARTGAT](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archief](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federatieve](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Ondersteuning voor bevoegdheden

### <a name="unsupported"></a>Niet ondersteund
- DBA-rol: Veel serverparameters en -instellingen kunnen per ongeluk de serverprestaties degraderen of zuureigenschappen van de DBMS tenietdoen. Als zodanig, om de service-integriteit en SLA op productniveau te behouden, legt deze service de Rol van DBA niet bloot. Het standaardgebruikersaccount, dat wordt opgebouwd wanneer een nieuwe database-instantie wordt gemaakt, stelt die gebruiker in staat om de meeste DDL- en DML-instructies uit te voeren in de beheerde database-instantie. 
- SUPER privilege: Ook [SUPER privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is ook beperkt.
- DEFINER: Vereist superprivileges om te maken en is beperkt. Als u gegevens importeert met `CREATE DEFINER` een back-up, `--skip-definer` verwijdert u de opdrachten handmatig of gebruikt u de opdracht bij het uitvoeren van een mysqldump.

## <a name="data-manipulation-statement-support"></a>Ondersteuning voor gegevensmanipulatie-instructie

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE`wordt ondersteund, maar `[LOCAL]` de parameter moet worden opgegeven en naar een UNC-pad worden gericht (Azure-opslag die is gemonteerd via SMB).

### <a name="unsupported"></a>Niet ondersteund
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="scale-operations"></a>Schaalbewerkingen
- Dynamische schaling van en naar de basisprijsniveaus wordt momenteel niet ondersteund.
- De kleinerwordende serveropslaggrootte wordt niet ondersteund.

### <a name="server-version-upgrades"></a>Serverversie-upgrades
- Geautomatiseerde migratie tussen belangrijke database-engineversies wordt momenteel niet ondersteund. Als u wilt upgraden naar de volgende grote versie, neem een [dump en het te herstellen](./concepts-migrate-dump-restore.md) naar een server die is gemaakt met de nieuwe engine versie.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Bij het gebruik van de PITR-functie wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.

### <a name="storage-size"></a>Opslaggrootte
- Raadpleeg [prijsniveaus](concepts-pricing-tiers.md) voor de opslaggroottelimieten per prijscategorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MySQL-serverinstantie geeft de verkeerde serverversie weer nadat de verbinding is gemaakt. Als u de juiste engineversie `select version();` van de serverinstantie wilt krijgen, gebruikt u de opdracht.

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde MySQL-databaseversies](concepts-supported-versions.md)
