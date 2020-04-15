---
title: Replicatie
description: Meer informatie over het gebruik van SQL Server-replicatie met enkele databases en databases van Azure SQL Database in elastische groepen
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381148"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replicatie naar enkele en samengevoegde databases van SQL Database

SQL Server-replicatie kan worden geconfigureerd voor afzonderlijke en samengevoegde databases op een [SQL Database-server](sql-database-servers.md) in Azure SQL Database.  

## <a name="supported-configurations"></a>**Ondersteunde configuraties:**
  
- De SQL Server kan een instantie zijn van SQL Server die on-premises wordt uitgevoerd of een instantie van SQL Server die wordt uitgevoerd in een Azure-virtuele machine in de cloud. Zie [SQL Server op Azure Virtual Machines-overzicht](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)voor meer informatie.  
- De Azure SQL-database moet een push-abonnee van een SQL Server-uitgever zijn.  
- De distributiedatabase en de replicatieagents kunnen niet in een Azure SQL-database worden geplaatst.  
- Momentopname en eenrichtingsreplicatie worden ondersteund. Peer-to-peer transactionele replicatie en samenvoegreplicatie worden niet ondersteund.
- Replicatie is beschikbaar voor een openbare preview op Azure SQL Database Managed Instance. Beheerde instantie kan uitgevers-, distributeur- en abonneedatabases hosten. Zie [Replicatie met SQL Database Managed Instance voor](replication-with-sql-database-managed-instance.md)meer informatie.

## <a name="versions"></a>Versies  

On-premises SQL Server-uitgevers en -distributeurs moeten (ten minste) een van de volgende versies gebruiken:  

- SQL Server 2016 en hoger
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) of [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) of [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Als u replicatie probeert te configureren met behulp van een niet-ondersteunde versie, kan dit leiden \<tot een foutnummer MSSQL_REPL20084 (Het proces kan geen verbinding maken met Abonnee.) en MSSQL_REPL40532 (Kan de servernaam niet openen> die door de aanmelding is aangevraagd. De login is mislukt.).  

Als u alle functies van Azure SQL Database wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en SQL Server Data Tools [gebruiken.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)  

  
## <a name="remarks"></a>Opmerkingen

- Replicatie kan worden geconfigureerd met [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of door Transact-SQL-instructies op de uitgever uit te voeren. U replicatie niet configureren met behulp van de Azure-portal.  
- Replicatie kan alleen SQL Server-verificatieaanmeldingen gebruiken om verbinding te maken met een Azure SQL-database.
- Gerepliceerde tabellen moeten een primaire sleutel hebben.  
- U moet een bestaand Azure-abonnement hebben.  
- De Azure SQL-databaseabonnee kan zich in elke regio bevinden.  
- Eén publicatie op SQL Server kan zowel Azure SQL Database als SQL Server (on-premises en SQL Server in een Azure virtual machine) abonnees ondersteunen.  
- Replicatiebeheer, -bewaking en probleemoplossing moeten worden uitgevoerd vanaf de on-premises SQL Server.  
- Alleen push-abonnementen op Azure SQL Database worden ondersteund.  
- Alleen `@subscriber_type = 0` wordt ondersteund in **sp_addsubscription** voor SQL Database.  
- Azure SQL Database biedt geen ondersteuning voor bidirectionele, directe, updatable of peer-to-peer-replicatie.

## <a name="replication-architecture"></a>Replicatiearchitectuur  

![replicatie-naar-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenario's  

### <a name="typical-replication-scenario"></a>Typisch replicatiescenario  

1. Maak een transactionele replicatiepublicatie in een on-premises SQL Server-database.  
2. Gebruik op de on-premises SQL Server de **wizard Nieuw abonnement** of Transact-SQL om een push-to-abonnement op Azure SQL Database te maken.  
3. Met afzonderlijke en samengevoegde databases in Azure SQL Database is de oorspronkelijke gegevensset een momentopname die wordt gemaakt door de momentopnameagent en wordt gedistribueerd en toegepast door de distributieagent. Met een beheerde instantiedatabase u ook een databaseback-up gebruiken om de abonneedatabase te zaaien.

### <a name="data-migration-scenario"></a>Scenario voor gegevensmigratie  

1. Gebruik transactionele replicatie om gegevens uit een on-premises SQL Server-database te repliceren naar Azure SQL Database.  
2. De client- of middenlaagstoepassingen omleiden om de azure SQL-databasekopie bij te werken.  
3. Stop met het bijwerken van de SQL Server-versie van de tabel en verwijder de publicatie.  

## <a name="limitations"></a>Beperkingen

De volgende opties worden niet ondersteund voor Azure SQL Database-abonnementen:

- Koppeling bestandsgroepen kopiëren  
- Indelingen van tabelpartities kopiëren  
- Indexeringsschema's kopiëren  
- Door de gebruiker gedefinieerde statistieken kopiëren  
- Standaardbindingen kopiëren  
- Regelbindingen kopiëren  
- Fulltext-indexen kopiëren  
- XML XSD kopiëren  
- XML-indexen kopiëren  
- Machtigingen kopiëren  
- Ruimtelijke indexen kopiëren  
- Gefilterde indexen kopiëren  
- Kenmerk gegevenscompressie kopiëren  
- Kenmerk schaarse kolom kopiëren  
- Bestandsstroom converteren naar MAX-gegevenstypen  
- Hierarchyid converteren naar MAX-gegevenstypen  
- Ruimtelijke omzetten in MAX-gegevenstypen  
- Uitgebreide eigenschappen kopiëren  
- Machtigingen kopiëren  

### <a name="limitations-to-be-determined"></a>Te bepalen beperkingen

- Collatie kopiëren  
- Uitvoering in een geserialiseerde transactie van de SP  

## <a name="examples"></a>Voorbeelden

Maak een publicatie en een push-abonnement. Zie voor meer informatie:
  
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Maak een Push-abonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) met de naam van de Azure SQL Database-server als abonnee (bijvoorbeeld **N'azuresqldbdns.database.windows.net'**) en de Azure SQL-databasenaam als doeldatabase (bijvoorbeeld **AdventureWorks).**  

## <a name="see-also"></a>Zie ook  

- [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md)
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Een Push-abonnement maken](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Controle (replicatie)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
