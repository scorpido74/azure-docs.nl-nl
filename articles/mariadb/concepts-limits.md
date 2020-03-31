---
title: Beperkingen - Azure-database voor MariaDB
description: In dit artikel worden beperkingen beschreven in Azure Database voor MariaDB, zoals het aantal verbindings- en opslagengineopties.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: bb907ee59891e5a9a1ffc9c8c6eee34d3e71ad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531937"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Beperkingen in Azure-database voor MariaDB
In de volgende secties worden capaciteit, ondersteuning van de opslagengine, ondersteuning voor bevoegdheden, ondersteuning voor gegevensmanipulatie-instructieen en functionele limieten in de databaseservice beschreven.

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

Het maken van nieuwe clientverbindingen met MariaDB kost tijd en eenmaal tot stand gebracht, deze verbindingen bezetten database bronnen, zelfs wanneer idle. De meeste toepassingen vragen om veel kortstondige verbindingen, die deze situatie verbindingen. Het resultaat is minder resources beschikbaar voor uw werkelijke werklast, wat leidt tot verminderde prestaties. Een verbindingspooler die niet-actieve verbindingen vermindert en bestaande verbindingen opnieuw gebruikt, helpt dit te voorkomen. Ga voor meer informatie over het instellen van ProxySQL naar onze [blogpost.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

De querycache is standaard uitgeschakeld. Als u de querycache `query_cache_type` wilt inschakelen, configureert u de parameter. 

Bekijk de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) voor meer informatie over deze parameter.

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

Bekijk de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) voor meer informatie over deze parameter.

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

Bekijk de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) voor meer informatie over deze parameter.

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

Bekijk de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) voor meer informatie over deze parameter.

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

Bekijk de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) voor meer informatie over deze parameter.

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

## <a name="storage-engine-support"></a>Ondersteuning voor opslagmotoren

### <a name="supported"></a>Ondersteund
- [Innodb](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [Geheugen](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Niet ondersteund
- [Myisam](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [ZWARTGAT](https://mariadb.com/kb/en/library/blackhole/)
- [Archief](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Ondersteuning voor bevoegdheden

### <a name="unsupported"></a>Niet ondersteund
- DBA-rol: Veel serverparameters en -instellingen kunnen per ongeluk de serverprestaties degraderen of zuureigenschappen van de DBMS tenietdoen. Als zodanig, om de service-integriteit en SLA op productniveau te behouden, legt deze service de Rol van DBA niet bloot. Het standaardgebruikersaccount, dat wordt opgebouwd wanneer een nieuwe database-instantie wordt gemaakt, stelt die gebruiker in staat om de meeste DDL- en DML-instructies uit te voeren in de beheerde database-instantie.
- SUPER privilege: Ook [SUPER privilege](https://mariadb.com/kb/en/library/grant/#global-privileges) is ook beperkt.
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
- Geautomatiseerde migratie tussen belangrijke database-engineversies wordt momenteel niet ondersteund.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Bij het gebruik van de PITR-functie wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="subscription-management"></a>Abonnementsbeheer
- Het dynamisch verplaatsen van vooraf gemaakte servers in een abonnements- en resourcegroep wordt momenteel niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.

### <a name="storage-size"></a>Opslaggrootte
- Raadpleeg [prijsniveaus](concepts-pricing-tiers.md) voor de opslaggroottelimieten per prijscategorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MariaDB-serverinstantie geeft de onjuiste serverversie weer nadat de verbinding is gemaakt. Als u de juiste engineversie `select version();` van de serverinstantie wilt krijgen, gebruikt u de opdracht.

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde MariaDB-databaseversies](concepts-supported-versions.md)
