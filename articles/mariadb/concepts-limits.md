---
title: Beperkingen-Azure Database for MariaDB
description: In dit artikel worden beperkingen beschreven in Azure Database for MariaDB, zoals het aantal opties voor de verbinding en de opslag engine.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: c982181dee34a7eb0715d5e1271ef5ed794f3809
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296741"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Beperkingen in Azure Database for MariaDB
De volgende secties beschrijven capaciteit, ondersteuning voor de opslag-engine, bevoegdheden ondersteuning, gegevens manipuleren instructie ondersteuning en functionele limieten in de database-service.

## <a name="server-parameters"></a>Server parameters

De minimum-en maximum waarden van verschillende populaire server parameters worden bepaald door de prijs categorie en vCores. Raadpleeg de onderstaande tabellen voor limieten.

### <a name="max_connections"></a>max_connections

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

Wanneer verbindingen de limiet overschrijdt, wordt de volgende fout:
> Fout 1040 (08004): Te veel verbindingen

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals ProxySQL om verbindingen efficiënt te beheren.

Het maken van nieuwe client verbindingen met MariaDB kost tijd en wanneer de verbinding tot stand is gebracht, nemen deze verbindingen database bronnen in beslag, zelfs wanneer ze niet actief zijn De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)voor meer informatie over het instellen van ProxySQL.

## <a name="query_cache_size"></a>query_cache_size

De query cache is standaard uitgeschakeld. Configureer de para meter `query_cache_type` om de query-cache in te scha kelen. 

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

## <a name="sort_buffer_size"></a>sort_buffer_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

## <a name="join_buffer_size"></a>join_buffer_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

## <a name="max_heap_table_size"></a>max_heap_table_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

## <a name="tmp_table_size"></a>tmp_table_size

Raadpleeg de [MariaDB-documentatie](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) voor meer informatie over deze para meter.

|**Prijscategorie**|**vCore (s)**|**Standaardwaarde**|**Minimum waarde**|**Maximum waarde**|
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

## <a name="storage-engine-support"></a>Ondersteuning voor de opslag-engine

### <a name="supported"></a>Ondersteund
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [GEHEUGENMETABASE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Niet ondersteund
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FAXBERICHTEN](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Ondersteuning van bevoegdheden

### <a name="unsupported"></a>Niet ondersteund
- DBA rol: veel parameters van de server en instellingen kunnen per ongeluk serverprestaties slechter of ACID-eigenschappen van de DBMS negatief moet worden gemaakt. Als zodanig wilt behouden de integriteit van de service en SLA op het productniveau van een, wordt deze service niet weergegeven de DBA-rol. De standaard-gebruikersaccount, die is gemaakt wanneer een nieuwe database-exemplaar wordt gemaakt, kan die gebruiker voor het uitvoeren van de meeste DDL en DML-instructies in de beheerde database-instantie.
- SUPER bevoegdheid: een soort gelijke [Super bevoegdheid](https://mariadb.com/kb/en/library/grant/#global-privileges) is ook beperkt.
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of met behulp van de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.

## <a name="data-manipulation-statement-support"></a>Beheerondersteuning-instructie bewerken

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE` wordt ondersteund, maar de para meter `[LOCAL]` moet worden opgegeven en worden omgeleid naar een UNC-pad (Azure-opslag gekoppeld aan SMB).

### <a name="unsupported"></a>Niet ondersteund
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="scale-operations"></a>Schaalbewerkingen
- Dynamische schaling naar en van de Basic Prijscategorieën wordt momenteel niet ondersteund.
- Waardoor de opslaggrootte van de server wordt niet ondersteund.

### <a name="server-version-upgrades"></a>Server-versie-upgrades
- Automatische migratie tussen versies van de primaire database-engine wordt momenteel niet ondersteund.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Wanneer u de functie PITR, wordt de nieuwe server gemaakt met dezelfde configuratie als de server die is gebaseerd op.
- Een verwijderde server herstelt, wordt niet ondersteund.

### <a name="subscription-management"></a>Abonnementsbeheer
- Het dynamisch verplaatsen van vooraf gemaakte servers over het abonnement en de resource groep wordt momenteel niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-service-eindpunten is alleen voor algemeen gebruik en geoptimaliseerd voor geheugen-servers.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MariaDB-Server exemplaar geeft de onjuiste server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht `select version();` om de juiste engine versie van het Server exemplaar op te halen.

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde versies van MariaDB-data base](concepts-supported-versions.md)
