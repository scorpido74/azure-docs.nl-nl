---
title: 'Bekende problemen: Migreren van Oracle naar Azure Database voor PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratiebeperkingen met online migraties van Oracle naar Azure Database voor PostgreSQL-Single server met behulp van de Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235248"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Bekende problemen/migratiebeperkingen met online migraties van Oracle naar Azure DB voor PostgreSQL-Single server

Bekende problemen en beperkingen in verband met online migraties van Oracle naar Azure Database voor PostgreSQL-Single server worden beschreven in de volgende secties.

## <a name="oracle-versions-supported-as-a-source-database"></a>Oracle-versies ondersteund als brondatabase

Azure Database Migration Service ondersteunt het verbinden met:

- Oracle versie 10g, 11g en 12c.
- Oracle Enterprise, Standard, Express en Personal Edition.

Azure Database Migration Service biedt geen ondersteuning voor het maken van verbinding met cdb's (multi-tenant containerdatabases).

## <a name="postgresql-versions-supported-as-a-target-database"></a>PostgreSQL-versies ondersteund als een doeldatabase

Azure Database Migration Service ondersteunt migraties naar Azure Database voor PostgreSQL-Single server versie 9.5, 9.6, 10 en 11. Zie het artikel [Ondersteunde PostgreSQL-databaseversies](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) voor actuele informatie over versieondersteuning in Azure Database voor PostgreSQL-Single-server.

## <a name="datatype-limitations"></a>Beperkingen voor gegevenstype

De volgende gegevenstypen worden **niet** gemigreerd:

- BFILE
- RIJ-ID
- Ref
- UROWID
- Anydata
- SDO_GEOMETRY
- Geneste tabellen
- Door de gebruiker gedefinieerde gegevenstypen
- Opmerkingen
- Virtuele kolommen
- Gematerialiseerde weergaven op basis van de ROWID-kolom

Lege BLOB/CLOB-kolommen worden ook toegewezen aan NULL op het doel.

## <a name="lob-limitations"></a>LOB-beperkingen

- Wanneer de LOB-modus met beperkte grootte is ingeschakeld, worden lege LOB's op de Oracle-bron gerepliceerd als NULL-waarden.
- Lange objectnamen (meer dan 30 bytes) worden niet ondersteund.
- Gegevens in lange en lange RAW-kolom mogen niet hoger zijn dan 64k. Alle gegevens na 64k worden afgekapt.
- Alleen in Oracle 12 worden wijzigingen in LOB-kolommen niet ondersteund (gemigreerd).
- UPDATE's naar XMLTYPE- en LOB-kolommen worden niet ondersteund (gemigreerd).

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

- Klanten moeten SYSDBA gebruiken om verbinding te maken met Oracle.
- Gegevenswijzigingen als gevolg van partitie-/subpartitiebewerkingen (ADD, DROP, EXCHANGE en TRUNCATE) worden niet gemigreerd en kunnen de volgende fouten veroorzaken:
  - Voor ADD-bewerkingen kunnen updates en verwijderingen op de toegevoegde gegevens een waarschuwing '0 rijen getroffen' retourneren.
  - Voor DROP- en TRUNCATE-bewerkingen kunnen nieuwe inserts leiden tot fouten in duplicaten.
  - Voor EXCHANGE-bewerkingen kunnen zowel een waarschuwing voor '0 rijen getroffen' als fouten in 'duplicaten' optreden.
- Tabellen waarvan de namen apostrofs bevatten, kunnen niet worden gerepliceerd.
