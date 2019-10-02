---
title: Artikel over bekende problemen/migratie beperkingen met online migraties van Oracle naar Azure Database for PostgreSQL-één server | Microsoft Docs
description: Meer informatie over bekende problemen/migratie beperkingen met online migraties van Oracle naar Azure Database for PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: bc37c7cb11b973f89695b5631ec9569ff6b94608
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71801786"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Bekende problemen/migratie beperkingen met online migraties van Oracle naar Azure DB voor PostgreSQL-één server

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van Oracle naar Azure Database for PostgreSQL-Eén server worden in de volgende secties beschreven.

## <a name="oracle-versions-supported-as-a-source-database"></a>Oracle-versies worden ondersteund als een bron database

Azure Database Migration Service ondersteunt het maken van verbinding met:

- Oracle versie 10g, 11c en 12c.
- Oracle Enter prise, Standard, Express en Personal Edition.

Azure Database Migration Service biedt geen ondersteuning voor het maken van verbinding met de container databases met meerdere tenants (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>PostgreSQL-versies worden ondersteund als doel database

Azure Database Migration Service ondersteunt migraties naar Azure Database for PostgreSQL-één server versie 9,5, 9,6, 10 en 11. Zie het artikel [ondersteunde PostgreSQL-database versies](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) voor actuele informatie over versie ondersteuning in azure database for PostgreSQL-één server.

## <a name="datatype-limitations"></a>Beperkingen van gegevens typen

De volgende gegevens typen worden **niet** gemigreerd:

- BFILE
- ROWID
- VERWIJZING
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Geneste tabellen
- Door de gebruiker gedefinieerde gegevens typen
- Opmerkingen
- Virtuele kolommen
- Gerealiseerde weer gaven op basis van de kolom ROWID

Ook lege BLOB-CLOB-kolommen worden toegewezen aan NULL op het doel.

## <a name="lob-limitations"></a>LOB-beperkingen

- Wanneer de LOB-modus met beperkte grootte is ingeschakeld, worden lege LOBs op de Oracle-bron gerepliceerd als NULL-waarden.
- Lange object namen (meer dan 30 bytes) worden niet ondersteund.
- Gegevens in een lange en lange onbewerkte kolom mogen niet groter zijn dan 64 kB. Alle gegevens die groter zijn dan 64 KB, worden afgekapt.
- In alleen Oracle 12 worden wijzigingen in LOB-kolommen niet ondersteund (gemigreerd).
- UPDATEs voor XMLTYPE en LOB-kolommen worden niet ondersteund (gemigreerd).

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

- Klanten moeten SYSDB gebruiken om verbinding te maken met Oracle.
- Gegevens wijzigingen die voortkomen uit partitie-sub-partitie bewerkingen (toevoegen, NEERZETten, EXCHANGE en TRUNCATe) worden niet gemigreerd en kunnen de volgende fouten veroorzaken:
  - Voor het toevoegen van bewerkingen, kunnen updates en verwijderingen op de toegevoegde gegevens een waarschuwing ' 0 rijen die worden beïnvloed ' retour neren.
  - Voor DROP-en TRUNCATe-bewerkingen kunnen nieuwe toevoegingen leiden tot fouten met ' duplicaten '.
  - Voor EXCHANGE-bewerkingen kunnen zowel een ' 0 rijen waarin wordt beschreven ' waarschuwing als ' duplicaten ' worden weer gegeven.
- Tabellen waarvan de namen apostrofs bevatten, kunnen niet worden gerepliceerd.
