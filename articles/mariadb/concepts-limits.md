---
title: Beperkingen-Azure Database for MariaDB
description: In dit artikel worden beperkingen beschreven in Azure Database for MariaDB, zoals het aantal opties voor de verbinding en de opslag engine.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc89b6233602c81ea622a528c223adf2003f0f68
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772493"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Beperkingen in Azure Database for MariaDB
In de volgende secties worden capaciteit, ondersteuning voor opslag-engine, ondersteuning van bevoegdheden, ondersteuning voor gegevens manipulatie en functionele limieten in de database service beschreven.

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
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [GEHEUGENMETABASE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Niet ondersteund
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FAXBERICHTEN](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Ondersteuning van bevoegdheden

### <a name="unsupported"></a>Niet ondersteund
- DBA-rol: veel server parameters en instellingen kunnen per ongeluk de prestaties van de server afnemen of ACID-eigenschappen van het DBMS ontzeggen. Als zodanig, voor het onderhouden van de service-integriteit en SLA op een product niveau, geeft deze service de rol van DBA niet weer. Het standaard gebruikers account, dat wordt gebouwd wanneer een nieuwe data base-instantie wordt gemaakt, stelt die gebruiker in staat om het meren deel van de DDL-en DML-instructies in het beheerde data base-exemplaar uit te voeren.
- SUPER bevoegdheid: een soort gelijke [Super bevoegdheid](https://mariadb.com/kb/en/library/grant/#global-privileges) is ook beperkt.
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
- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Wanneer u de functie PITR gebruikt, wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="subscription-management"></a>Abonnementsbeheer
- Het dynamisch verplaatsen van vooraf gemaakte servers over het abonnement en de resource groep wordt momenteel niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eind punten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MariaDB-Server exemplaar geeft de onjuiste server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht `select version();` om de juiste engine versie van het Server exemplaar op te halen.

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde versies van MariaDB-data base](concepts-supported-versions.md)
