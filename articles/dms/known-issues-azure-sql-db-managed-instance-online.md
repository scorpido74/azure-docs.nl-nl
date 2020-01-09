---
title: Bekende problemen en beperkingen met online migraties voor het Azure SQL Database van het beheerde exemplaar
description: Meer informatie over bekende problemen/migratie beperkingen die zijn gekoppeld aan online migraties naar Azure SQL Database Managed instance.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: bc1cbfc1e86db758a4f4d0240f81f5363f817312
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483139"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Bekende problemen/migratie beperkingen met online migraties naar Azure SQL Database beheerde instantie

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van SQL Server naar Azure SQL Database beheerde instantie worden hieronder beschreven.

> [!IMPORTANT]
> Met online migraties van SQL Server naar Azure SQL Database wordt de migratie van SQL_variant gegevens typen niet ondersteund.

## <a name="backup-requirements"></a>Back-upvereisten

- **Back-ups met controlesom**

    Azure Database Migration Service maakt gebruik van de methode Backup en Restore om uw on-premises data bases te migreren naar SQL Database Managed instance. Azure Database Migration Service ondersteunt alleen back-ups die zijn gemaakt met behulp van een controlesom.

    [Back-upcontrolesoms tijdens het maken of herstellen van back-ups in-of uitschakelen (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Als u de database back-ups met compressie uitvoert, is de controlesom een standaard gedrag, tenzij expliciet is uitgeschakeld.

    Met offline migraties kunt u, als u kiest voor **ik Azure database Migration service...** , Azure database Migration service dan zal de back-up van de Data Base worden gemaakt met de optie controlesom ingeschakeld.

- **Back-upmedia**

    Zorg ervoor dat elke back-up op een afzonderlijk back-upmedium (back-upbestanden) wordt uitgevoerd. Azure Database Migration Service biedt geen ondersteuning voor back-ups die worden toegevoegd aan één back-upbestand. Maak volledige back-ups en logboek back-ups om back-upbestanden te scheiden.

## <a name="data-and-log-file-layout"></a>Indeling van gegevens en logboek bestanden

- **Aantal logboek bestanden**

    Azure Database Migration Service biedt geen ondersteuning voor data bases met meerdere logboek bestanden. Als u meerdere logboek bestanden hebt, moet u ze verkleinen en opnieuw indelen in één transactie logboek bestand. U moet eerst een back-up maken van het logboek bestand omdat u niet extern bestanden kunt registreren die niet leeg zijn.

## <a name="sql-server-features"></a>SQL Server functies

- **FileStream-FileTables**

    SQL Database beheerde instantie biedt momenteel geen ondersteuning voor FileStream en FileTables. Voor werk belastingen die afhankelijk zijn van deze functies, raden we u aan om te kiezen voor SQL-servers die worden uitgevoerd op virtuele Azure-machines als uw Azure-doel.

- **In-Memory tabellen**

    OLTP in het geheugen is beschikbaar in de Premium-en Bedrijfskritiek-laag voor SQL Database beheerde instantie; de laag Algemeen biedt geen ondersteuning voor in-Memory OLTP.

## <a name="migration-resets"></a>Migratie opnieuw instellen

- **Implementaties**

    SQL Database Managed instance is een PaaS-service met automatische patches en versie-updates. Tijdens de migratie van uw SQL Database beheerde exemplaar zijn niet-essentiële updates 36 uur. Daarna (en voor essentiële updates), als de migratie wordt onderbroken, wordt het proces opnieuw ingesteld op een volledige herstel status.

    Migratie cutover kan alleen worden aangeroepen nadat de volledige back-up is hersteld en de back-ups van alle logboeken worden onderschept. Als uw productie migratie cutovers wordt beïnvloed, neemt u contact op met de [Azure DMS-feedback alias](mailto:dmsfeedback@microsoft.com).
