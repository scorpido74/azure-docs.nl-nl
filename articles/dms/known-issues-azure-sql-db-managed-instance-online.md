---
title: Bekende problemen en beperkingen met online migraties naar azure SQL Database beheerde instantie
description: Meer informatie over bekende problemen/migratiebeperkingen in verband met onlinemigraties naar azure SQL Database beheerde instantie.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648662"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Bekende problemen/migratiebeperkingen met online migraties naar azure SQL Database-beheerde instantie

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van SQL Server naar Azure SQL Database beheerde instantie worden hieronder beschreven.

> [!IMPORTANT]
> Met online migraties van SQL Server naar Azure SQL Database wordt migratie van SQL_variant gegevenstypen niet ondersteund.

## <a name="backup-requirements"></a>Back-upvereisten

- **Back-ups met checksum**

    Azure Database Migration Service gebruikt de back-up- en herstelmethode om uw on-premises databases te migreren naar sql-databasebeheerde instantie. Azure Database Migration Service ondersteunt alleen back-ups die zijn gemaakt met behulp van checksum.

    [Back-upcontrolesommen in- of uitschakelen tijdens back-up of herstel (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Als u de databaseback-ups met compressie neemt, is de checksum een standaardgedrag, tenzij deze expliciet is uitgeschakeld.

    Bij offline migraties laat ik Azure **Database Migration Service...**, dan neemt Azure Database Migration Service de databaseback-up met de optie checksum ingeschakeld.

- **Back-upmedia**

    Zorg ervoor dat u elke back-up op een aparte back-up media (back-up bestanden). Azure Database Migration Service biedt geen ondersteuning voor back-ups die zijn toegevoegd aan één back-upbestand. Neem volledige back-up sbackup sen en log back-ups om back-upbestanden te scheiden.

## <a name="data-and-log-file-layout"></a>Indeling van gegevens en logboekbestanden

- **Aantal logbestanden**

    Azure Database Migration Service ondersteunt geen databases met meerdere logboekbestanden. Als u meerdere logboekbestanden hebt, u deze verkleinen en reorganiseren in één transactielogboekbestand. Omdat u niet worden logboeken die niet leeg zijn, moet u eerst een back-up maken van het logboekbestand.

## <a name="sql-server-features"></a>SQL Server-functies

- **FileStream/Bestandstabellen**

    SQL Database-beheerde instantie biedt momenteel geen ondersteuning voor FileStream en FileTables. Voor workloads die afhankelijk zijn van deze functies, raden we u aan te kiezen voor SQL Servers die worden uitgevoerd op Azure VM's als Azure-doel.

- **In-memory tabellen**

    In-memory OLTP is beschikbaar in de niveaus Premium en Business Critical voor SQL Database managed instance; de laag Algemeen doel biedt geen ondersteuning voor OLTP in het geheugen.

## <a name="migration-resets"></a>Migratieresets

- **Implementaties**

    SQL Database managed instance is een PaaS-service met automatische patching en versie-updates. Tijdens de migratie van uw SQL Database-beheerde exemplaar worden niet-kritieke updates tot 36 uur geholpen. Daarna (en voor kritieke updates), als de migratie wordt verstoord, wordt het proces teruggezet naar een volledige herstelstatus.

    Migratie cutover kan alleen worden aangeroepen nadat de volledige back-up is hersteld en haalt alle logboekback-ups in. Als uw productiemigratiecutovers worden beïnvloed, neemt u contact op met de [azure DMS Feedback-alias](mailto:dmsfeedback@microsoft.com).
