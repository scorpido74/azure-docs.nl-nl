---
title: Beperkingen-Azure Database for MariaDB
description: In dit artikel worden beperkingen beschreven in Azure Database for MariaDB, zoals het aantal opties voor de verbinding en de opslag engine.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 11b28acfbda8b2760f19aa130373ba0f24f94db2
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636594"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Beperkingen in Azure Database for MariaDB
In de volgende secties worden capaciteit, ondersteuning voor opslag-engine, ondersteuning van bevoegdheden, ondersteuning voor gegevens manipulatie en functionele limieten in de database service beschreven.

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
- DEFINE: vereist Super privileges om te maken en beperkt. Als u gegevens importeert met behulp van een back-up, verwijdert u de `CREATE DEFINER` opdrachten hand matig of gebruikt u de `--skip-definer` opdracht bij het uitvoeren van een mysqldump.

## <a name="server-parameters"></a>Serverparameters
Azure Database for MariaDB biedt ondersteuning voor het afstemmen van de waarden van server parameters. De minimum-en maximum waarde van sommige para meters worden bepaald door de prijs categorie en de vCores van de server. Raadpleeg [server parameters](./concepts-server-parameters.md) voor meer informatie over deze limieten. 

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
- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund.

### <a name="point-in-time-restore"></a>Een punt in de tijd herstellen
- Wanneer u de functie PITR gebruikt, wordt de nieuwe server gemaakt met dezelfde configuraties als de server waarop deze is gebaseerd.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="subscription-management"></a>Abonnementsbeheer
- Het dynamisch verplaatsen van vooraf gemaakte servers over het abonnement en de resource groep wordt momenteel niet ondersteund.

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="storage-size"></a>Opslag grootte
- Raadpleeg de [prijs categorie](concepts-pricing-tiers.md) voor de limieten voor opslag grootte per prijs categorie.

## <a name="current-known-issues"></a>Huidige bekende problemen
- MariaDB-Server exemplaar geeft de onjuiste server versie weer nadat de verbinding tot stand is gebracht. Gebruik de opdracht om de juiste engine versie van Server exemplaar op te halen `select version();` .

## <a name="next-steps"></a>Volgende stappen
- [Wat is er beschikbaar in elke servicelaag](concepts-pricing-tiers.md)
- [Ondersteunde versies van MariaDB-data base](concepts-supported-versions.md)
