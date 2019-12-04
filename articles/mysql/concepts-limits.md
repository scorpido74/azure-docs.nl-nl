---
title: Beperkingen-Azure Database for MySQL
description: In dit artikel worden beperkingen beschreven in Azure Database for MySQL, zoals het aantal opties voor de verbinding en de opslag engine.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 07feb3ebf9720d70da441486fd0b2e6e274b68e4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770914"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Beperkingen in Azure Database for MySQL
In de volgende secties worden capaciteit, ondersteuning voor opslag-engine, ondersteuning van bevoegdheden, ondersteuning voor gegevens manipulatie en functionele limieten in de database service beschreven. Zie ook [algemene beperkingen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) die van toepassing zijn op de MySQL-data base-engine.

## <a name="maximum-connections"></a>Maximum aantal verbindingen
Het maximum aantal verbindingen per prijs categorie en vCores is als volgt: 

|**Prijscategorie**|**vCore (s)**| **Maximum aantal verbindingen**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Algemeen doel| 2| 300|
|Algemeen doel| 4| 625|
|Algemeen doel| 8| 1250|
|Algemeen doel| 16| 2500|
|Algemeen doel| 32| 5000|
|Algemeen doel| 64| 10.000|
|Geoptimaliseerd geheugen| 2| 600|
|Geoptimaliseerd geheugen| 4| 1250|
|Geoptimaliseerd geheugen| 8| 2500|
|Geoptimaliseerd geheugen| 16| 5000|
|Geoptimaliseerd geheugen| 32| 10.000|

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> FOUT 1040 (08004): te veel verbindingen

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
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of met behulp van de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.

## <a name="data-manipulation-statement-support"></a>Ondersteuning voor gegevens manipulatie-instructies

### <a name="supported"></a>Ondersteund
- `LOAD DATA INFILE` wordt ondersteund, maar de para meter `[LOCAL]` moet worden opgegeven en worden omgeleid naar een UNC-pad (Azure-opslag gekoppeld aan SMB).

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

### <a name="vnet-service-endpoints"></a>VNet-service-eind punten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- Het MySQL-Server exemplaar geeft de verkeerde Server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht `select version();` om de juiste engine versie van het Server exemplaar op te halen.

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde MySQL-database versies](concepts-supported-versions.md)
