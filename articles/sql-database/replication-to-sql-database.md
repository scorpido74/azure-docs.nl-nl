---
title: Replicatie
description: Meer informatie over het gebruik van SQL Server replicatie met Azure SQL Database afzonderlijke data bases en data bases in elastische Pools
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359866"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replicatie naar SQL Database afzonderlijke en gepoolde data bases

SQL Server replicatie kan worden geconfigureerd voor afzonderlijke en gepoolde data bases op een [SQL database-server](sql-database-servers.md) in Azure SQL database.  

## <a name="supported-configurations"></a>**Ondersteunde configuraties:**
  
- De SQL Server kan een exemplaar zijn van SQL Server die on-premises of een exemplaar van SQL Server worden uitgevoerd op een virtuele machine van Azure in de Cloud. Zie [SQL Server op Azure virtual machines Overview](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)voor meer informatie.  
- De Azure-SQL database moet een push-abonnee van een SQL Server Publisher zijn.  
- De distributie database en de replicatie agenten kunnen niet worden geplaatst op een Azure-SQL database.  
- Snap shot en eenrichtings transactionele replicatie worden ondersteund. Peer-to-peer transactionele replicatie en samenvoeg replicatie worden niet ondersteund.
- Replicatie is beschikbaar voor open bare preview op Azure SQL Database Managed instance. Een beheerd exemplaar kan Publisher-, Distributor-en Subscriber-data bases hosten. Zie [replicatie met SQL database Managed instance](replication-with-sql-database-managed-instance.md)voor meer informatie.

## <a name="versions"></a>Versies  

On-premises SQL Server uitgevers en distributeurs moeten (ten minste) een van de volgende versies gebruiken:  

- SQL Server 2016 en hoger
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) of [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) of [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Poging tot het configureren van een replicatie met een niet-ondersteunde versie kan resulteren in een fout nummer MSSQL_REPL20084 (het proces kan geen verbinding maken met de abonnee.) en MSSQL_REPL40532 (kan de naam van de server \<niet openen > aangevraagd door de aanmelding. De aanmelding is mislukt.)  

Als u alle functies van Azure SQL Database wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.  

  
## <a name="remarks"></a>Opmerkingen

- Replicatie kan worden geconfigureerd met behulp van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) of door Transact-SQL-instructies uit te voeren op de Publisher. U kunt geen replicatie configureren met behulp van de Azure Portal.  
- Replicatie kan alleen SQL Server verificatie aanmeldingen gebruiken om verbinding te maken met een Azure SQL database.
- Gerepliceerde tabellen moeten een primaire sleutel hebben.  
- U moet een bestaand Azure-abonnement hebben.  
- De Azure SQL database-abonnee kan zich in elke regio bevinden.  
- Eén publicatie op SQL Server kan zowel Azure SQL Database als SQL Server ondersteunen (on-premises en SQL Server in een Azure virtual machine)-abonnees.  
- Replicatie beheer, bewaking en probleem oplossing moeten worden uitgevoerd vanaf de on-premises SQL Server.  
- Alleen push-abonnementen naar Azure SQL Database worden ondersteund.  
- Alleen `@subscriber_type = 0` wordt ondersteund in **sp_addsubscription** voor SQL database.  
- Azure SQL Database biedt geen ondersteuning voor bidirectionele, onmiddellijke, bijwerk bare of peer-to-peer-replicatie.

## <a name="replication-architecture"></a>Replicatie architectuur  

![replicatie naar SQL-data base](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenario 's  

### <a name="typical-replication-scenario"></a>Typisch replicatie scenario  

1. Een transactionele replicatie publicatie maken op een on-premises SQL Server Data Base.  
2. Gebruik op de on-premises SQL Server de **wizard Nieuw abonnement** of de Transact-SQL-instructies voor het maken van een push-naar-Azure SQL database-abonnement.  
3. Met één en gegroepeerde Data bases in Azure SQL Database is de initiële gegevensset een moment opname die wordt gemaakt door de momentopname agent en wordt gedistribueerd en toegepast door de distributie agent. Met een beheerde exemplaar database kunt u ook een back-up van de Data Base gebruiken om de abonnee database te seeden.

### <a name="data-migration-scenario"></a>Scenario voor gegevens migratie  

1. Transactionele replicatie gebruiken om gegevens van een on-premises SQL Server Data Base te repliceren naar Azure SQL Database.  
2. Omleiden van de client-of middle-tier-toepassingen om de Azure SQL database-kopie bij te werken.  
3. Stop het bijwerken van de SQL Server versie van de tabel en verwijder de publicatie.  

## <a name="limitations"></a>Beperkingen

De volgende opties worden niet ondersteund voor Azure SQL Database-abonnementen:

- Koppeling bestands groepen kopiëren  
- Schema's voor het partitioneren van tabellen kopiëren  
- Schema's voor het partitioneren van indexen kopiëren  
- Door de gebruiker gedefinieerde statistieken kopiëren  
- Standaard bindingen kopiëren  
- Regel bindingen kopiëren  
- Volledige-tekst indexen kopiëren  
- XSD van kopiëren XML  
- XML-indexen kopiëren  
- Machtigingen kopiëren  
- Ruimtelijke indexen kopiëren  
- Gefilterde indexen kopiëren  
- Gegevens compressie kenmerk kopiëren  
- Sparse kolom kenmerk kopiëren  
- FileStream converteren naar maximum aantal gegevens typen  
- Hierarchyid naar MAX-gegevens typen converteren  
- Ruimtelijke naar maximum aantal gegevens typen converteren  
- Uitgebreide eigenschappen kopiëren  
- Machtigingen kopiëren  

### <a name="limitations-to-be-determined"></a>Te bepalen beperkingen

- Sortering kopiëren  
- Uitvoering in een geserialiseerde trans actie van de SP  

## <a name="examples"></a>Voorbeelden

Maak een publicatie en een push-abonnement. Ga voor meer informatie naar:
  
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Maak een push-abonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) met behulp van de naam van de Azure SQL database-server als de abonnee (bijvoorbeeld **N'azuresqldbdns. data base. Windows. net**) en de naam van de Azure-SQL database als de doel database (bijvoorbeeld **AdventureWorks**).  

## <a name="see-also"></a>Zie ook  

- [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md)
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
