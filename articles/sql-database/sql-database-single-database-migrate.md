---
title: Database migratie SQL Server naar één/gegroepeerde Data Base
description: Meer informatie over SQL Server database migratie naar een enkele data base of een elastische groep in Azure SQL Database.
keywords: databasemigratie, sql server-databasemigratie, hulpprogramma's voor databasemigratie, database migreren, sql-database migreren
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: df1ef21da43bc74809bd9fd71b5dde3906cdb343
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820977"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server database migratie naar Azure SQL Database

In dit artikel vindt u informatie over de primaire methoden voor het migreren van een Data Base van SQL Server 2005 of hoger naar een enkele of gegroepeerde Data base in Azure SQL Database. Voor informatie over het migreren naar een beheerd exemplaar, Zie [migreren naar SQL Server-exemplaar naar Azure SQL database beheerd exemplaar](sql-database-managed-instance-migrate.md). Raadpleeg de [migratie handleiding voor Azure data base](https://datamigration.microsoft.com/)voor migratie-informatie over het migreren van andere platforms.

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migreren naar een enkele data base of een gegroepeerde Data Base

Er zijn twee primaire methoden voor het migreren van een Data Base van SQL Server 2005 of hoger naar een enkele of gegroepeerde Data base in Azure SQL Database. De eerste methode is eenvoudiger, maar leidt wel tot enige uitvaltijd tijdens de migratie. In bepaalde gevallen kan deze uitvaltijd aanzienlijk zijn. De tweede methode is complexer, maar veroorzaakt tijdens de migratie veel minder uitvaltijd.

In beide gevallen moet u ervoor zorgen dat de bron database compatibel is met Azure SQL Database met behulp van de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database V12 is bijna gelijk aan de [functie pariteit](sql-database-features.md) met SQL Server, met uitzonde ring van problemen met betrekking tot bewerkingen op server niveau en meerdere data bases. Bij databases en toepassingen die afhankelijk zijn van [slechts gedeeltelijk of in het geheel niet ondersteunde functies](sql-database-transact-sql-information.md), is een zekere mate van [herstructurering vereist om compatibiliteitsproblemen](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) op te lossen voordat de SQL Server-database kan worden gemigreerd.

> [!NOTE]
> Voor het migreren van een SQL Server-database, met inbegrip van Microsoft Access, Sybase, MySQL Oracle en DB2, naar Azure SQL Database raadpleegt u [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) (Migratieassistent voor SQL Server).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Methode 1: Migratie die gepaard gaat met uitvaltijd

 Gebruik deze methode om naar een enkele of een gegroepeerde Data Base te migreren als u enige downtime kunt of een test migratie van een productie database uitvoert voor latere migratie. Zie [een SQL Server-Data Base migreren](../dms/tutorial-sql-server-to-azure-sql.md)voor een zelf studie.

De volgende lijst bevat de algemene werk stroom voor een SQL Server database migratie van een enkele of een gegroepeerde Data Base met behulp van deze methode. Zie [migratie naar een beheerd exemplaar](sql-database-managed-instance-migrate.md)voor migratie naar een beheerd exemplaar.

  ![Diagram van VSSSDT-migratie](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Evalueer](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) de Data Base voor compatibiliteit met behulp van de nieuwste versie van de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Bereid alle benodigde fixes voor als Transact-SQL-scripts.
3. Maak een transactioneel consistente kopie van de bron database die wordt gemigreerd of stopt nieuwe trans acties in de bron database terwijl de migratie plaatsvindt. Voor het uitvoeren van deze laatste optie, waaronder het uitschakelen van client connectiviteit of het maken van een [moment opname van een Data Base](https://msdn.microsoft.com/library/ms175876.aspx). Na de migratie kunt u transactionele replicatie gebruiken om de gemigreerde data bases bij te werken met wijzigingen die zich voordoen na het afsluit punt voor de migratie. Zie [migreren met transactionele migratie](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Implementeer de Transact-SQL-scripts om de fixes toe te passen op de databasekopie.
5. [Migreer](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) de database kopie naar een nieuwe Azure SQL database met behulp van de Data Migration Assistant.

> [!NOTE]
> In plaats van DMA te gebruiken, kunt u ook een BACPAC-bestand gebruiken. Zie [een BACPAC-bestand importeren in een nieuwe Azure SQL database](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>De prestaties van de gegevensoverdracht tijdens de migratie optimaliseren

Hieronder leest u enkele aanbevelingen voor het behalen van optimale prestaties tijdens het importeren.

- Kies de hoogste servicelaag en reken grootte die uw budget toestaat om de overdrachts prestaties te maximaliseren. Na voltooiing van de migratie kunt u weer omlaag schalen om geld te besparen.
- Minimaliseer de afstand tussen uw BACPAC-bestand en het doel Data Center.
- Schakel automatische statistieken tijdens de migratie uit.
- Partitioneer tabellen en indexen.
- Verwijder geïndexeerde weergaven en maak ze opnieuw nadat de migratie is voltooid.
- Verplaats zelden opgevraagde historische gegevens naar een andere database en migreer ze vervolgens naar een afzonderlijke Azure SQL-database. U kunt dan [elastische query's](sql-database-elastic-query-overview.md) uitvoeren om eventueel benodigde historische gegevens op te vragen.

### <a name="optimize-performance-after-the-migration-completes"></a>Prestaties optimaliseren nadat de migratie is voltooid

[Werk uw statistieken bij](https://msdn.microsoft.com/library/ms187348.aspx) met een volledige scan nadat de migratie is voltooid.

## <a name="method-2-use-transactional-replication"></a>Methode 2: Transactionele replicatie gebruiken

Als u het zich niet kunt permitteren om tijdens de migratie uw SQL Server-database uit de productieomgeving te verwijderen, kunt u als migratieoplossing transactionele replicatie voor SQL Server gebruiken. Voor het gebruik van deze methode moet de brondatabase voldoen aan de [vereisten voor transactionele replicatie](https://msdn.microsoft.com/library/mt589530.aspx) en compatibel zijn met Azure SQL Database. Zie replicatie configureren voor AlwaysOn- [beschikbaarheids groepen (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)voor meer informatie over SQL-replicatie met Always on.

Als u deze oplossing wilt gebruiken, configureert u uw Azure SQL Database als abonnee voor het SQL Server-exemplaar dat u wilt migreren. De distributeur voor transactionele replicatie synchroniseert dan de gegevens uit de database die moeten worden gesynchroniseerd (de uitgever) terwijl er nieuwe transacties blijven binnenkomen.

Met transactionele replicatie komen alle wijzigingen aan uw gegevens of schema in uw Azure SQL Database terecht. Nadat de synchronisatie is voltooid en u klaar bent om te migreren, wijzigt u de verbindingstekenreeks in uw toepassingen zodanig dat deze naar uw Azure SQL Database verwijst. Wanneer door toepassing van transactionele replicatie alle wijzigingen die nog in uw brondatabase aanwezig zijn, zijn overgenomen en al uw toepassingen naar Azure DB verwijzen, kunt u de functie voor transactionele replicatie verwijderen. Uw Azure SQL Database is nu uw productiesysteem.

 ![SeedCloudTR-diagram](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> U kunt transactionele replicatie ook gebruiken voor het migreren van een subset van uw brondatabase. De publicatie die u naar Azure SQL Database repliceert, kan worden beperkt tot een subset van de tabellen in de betreffende database. Voor elke tabel die wordt gerepliceerd, kunt u de gegevens beperken tot een subset van de rijen en/of een subset van de kolommen.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migratie naar SQL Database met de transactiereplicatiewerkstroom

> [!IMPORTANT]
> Gebruik altijd de nieuwste versie van SQL Server Management Studio om gesynchroniseerd te blijven met updates voor Microsoft Azure en SQL Database. In oudere versies van SQL Server Management Studio kunt u SQL Database niet instellen als abonnee. [SQL Server Management Studio bijwerken](https://msdn.microsoft.com/library/mt238290.aspx).

1. Distributie instellen
   - [SQL Server Management Studio (SSMS) gebruiken](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Transact-SQL gebruiken](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Publicatie maken
   - [SQL Server Management Studio (SSMS) gebruiken](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Transact-SQL gebruiken](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Abonnement maken
   - [SQL Server Management Studio (SSMS) gebruiken](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Transact-SQL gebruiken](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Enkele tips en verschillen met betrekking tot migratie naar SQL Database

- Gebruik een lokale distributeur.
  - Dit veroorzaakt een invloed op de prestaties van de server.
  - Als de prestatie-impact onacceptabel is, kunt u een andere server gebruiken, maar dit verhoogt wel de complexiteit van het beheer.
- Als u een map met momentopnamen selecteert, moet u ervoor zorgen dat de map die u selecteert, groot genoeg is voor een BCP van elke tabel die u wilt repliceren.
- Als u een momentopname maakt, worden de gekoppelde tabellen vergrendeld totdat het proces is voltooid. Het is dus belangrijk dat u het maken van momentopnamen goed plant.
- In Azure SQL Database worden alleen push-abonnementen ondersteund. U kunt alleen abonnees toevoegen vanuit de brondatabase.

## <a name="resolving-database-migration-compatibility-issues"></a>Compatibiliteitsproblemen met databasemigratie oplossen

Er zijn veel verschillende compatibiliteitsproblemen die zich kunnen voordoen. Dit is afhankelijk van de versie van SQL Server in de brondatabase en de complexiteit van de database die u migreert. Oudere versies van SQL Server hebben vaker te maken met compatibiliteitsproblemen. Gebruik de volgende resources en voer ook op internet een gerichte zoekopdracht uit (met uw zoekmachine):

- [SQL Server-databasefuncties die niet worden ondersteund in Azure SQL Database](sql-database-transact-sql-information.md)
- [Database Engine-functionaliteit in SQL Server 2016 stopgezet](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Database Engine-functionaliteit in SQL Server 2014 stopgezet](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Database Engine-functionaliteit in SQL Server 2012 stopgezet](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Database Engine-functionaliteit in SQL Server 2008 R2 stopgezet](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Database Engine-functionaliteit in SQL Server 2005 stopgezet](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

In aanvulling op deze resources en uw zoekopdrachten op internet kunt u ook de [MSDN SQL Server-communityforums](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) of [StackOverflow](https://stackoverflow.com/) raadplegen.

> [!IMPORTANT]
> Met SQL Database Managed Instance kunt u een bestaand SQL Server exemplaar en de bijbehorende data bases migreren met minimale problemen met de compatibiliteit. Zie [Wat is een beheerd exemplaar](sql-database-managed-instance.md).

## <a name="next-steps"></a>Volgende stappen

- Gebruik het script dat beschikbaar is op de Azure SQL EMEA Engineers-blog [om het tempdb-gebruik tijdens de migratie te bewaken](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Gebruik het script dat beschikbaar is op de Azure SQL EMEA Engineers-blog [om de transactielogboekruimte van de database te bewaken terwijl de migratie bezig is](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Meer informatie over hoe u na de migratie met de UTC-tijd werkt, vindt u in [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (De standaardtijdzone voor uw lokale tijdzone wijzigen).
- Meer informatie over hoe u na de migratie de standaardtaal van een database wijzigt, vindt u in [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (De standaardtaal van Azure SQL Database wijzigen).
