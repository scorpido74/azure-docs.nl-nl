---
title: Transactionele replicatie
description: Meer informatie over het gebruik van SQL Server transactionele replicatie met enkelvoudige, gepoolde en instantie-data bases in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 41dd336bdb74fbe745ab48ebd3c168af0492ae2c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75691002"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transactionele replicatie met enkelvoudige, gepoolde en instantie-data bases in Azure SQL Database

Transactionele replicatie is een functie van Azure SQL Database en SQL Server waarmee u gegevens uit een tabel in Azure SQL Database of een SQL Server kunt repliceren naar de tabellen die zijn geplaatst op externe data bases. Met deze functie kunt u meerdere tabellen in verschillende data bases synchroniseren.

## <a name="when-to-use-transactional-replication"></a>Wanneer u transactionele replicatie wilt gebruiken

Transactionele replicatie is handig in de volgende scenario's:
- Publiceer wijzigingen die zijn aangebracht in een of meer tabellen in een Data Base en distribueer ze naar een of meer SQL Server of Azure SQL-data bases die zijn geabonneerd op de wijzigingen.
- Bewaar verschillende gedistribueerde data bases met de status gesynchroniseerd.
- Migreer data bases van een SQL Server of een beheerd exemplaar naar een andere data base door de wijzigingen continu te publiceren.

## <a name="overview"></a>Overzicht

De belangrijkste onderdelen in transactionele replicatie worden weer gegeven in de volgende afbeelding:  

![replicatie met SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

De **Uitgever** is een instantie of server die wijzigingen publiceert die zijn aangebracht in sommige tabellen (artikelen) door de updates naar de Distributor te verzenden. Publiceren naar een Azure-SQL database vanuit een on-premises SQL Server wordt ondersteund door de volgende versies van SQL Server:

- SQL Server 2019 (preview-versie)
- SQL Server 2016 tot SQL 2017
- SQL Server 2014 SP1 CU3 of hoger (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 of hoger (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Voor andere versies van SQL Server die het publiceren naar objecten in azure niet ondersteunen, is het mogelijk de methode voor het [opnieuw publiceren van gegevens](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) te gebruiken om gegevens te verplaatsen naar nieuwere versies van SQL Server. 

De **Distributor** is een instantie of server die wijzigingen in de artikelen verzamelt van een uitgever en deze distribueert naar de abonnees. De Distributor kan Azure SQL Database beheerde instantie of SQL Server zijn (elke versie zolang deze gelijk is aan of hoger is dan de versie van de uitgever). 

De **abonnee** is een exemplaar of server die de wijzigingen ontvangt die zijn aangebracht op de Publisher. Abonnees kunnen bestaan uit één, gegroepeerde en instantie-data base in Azure SQL Database-of SQL Server-data bases. Een abonnee op een enkele of gegroepeerde Data Base moet als push-abonnee worden geconfigureerd. 

| Rol | Enkele en gepoolde data bases | Exemplaar databases |
| :----| :------------- | :--------------- |
| **Publisher** | Nee | Ja | 
| **Verdeler** | Nee | Ja|
| **Pull-abonnee** | Nee | Ja|
| **Push-abonnee**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Een pull-abonnement wordt niet ondersteund wanneer de Distributor een exemplaar database is en de abonnee niet. 

Er zijn verschillende [typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replicatie | Enkele en gepoolde data bases | Exemplaar databases|
| :----| :------------- | :--------------- |
| [**Standaard transactionele**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (alleen als abonnee) | Ja | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (alleen als abonnee) | Ja|
| [**Samenvoeg replicatie**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nee | Nee|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nee | Nee|
| [**Bidirectioneel**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nee | Ja|
| [**Bij te werken abonnementen**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nee | Nee|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Het configureren van een replicatie met een oudere versie kan leiden tot een fout nummer MSSQL_REPL20084 (het proces kan geen verbinding maken met de abonnee.) en MSSQ_REPL40532 (kan de naam van de server \<niet openen > aangevraagd door de aanmelding. De aanmelding is mislukt.)
  > - Als u alle functies van Azure SQL Database wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Ondersteunings matrix voor instantie databases en on-premises systemen
  De replicatie-ondersteunings matrix voor instantie-data bases is dezelfde als die voor SQL Server on-premises. 
  
| **Publisher**   | **Verdeler** | **Gehaald** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Vereisten

- Connectiviteit maakt gebruik van SQL-verificatie tussen replicatiedeelnemers. 
- Een Azure Storage-account share voor de werkmap die wordt gebruikt voor replicatie. 
- Poort 445 (TCP outbound) moet zijn geopend in de beveiligings regels van het subnet van het beheerde exemplaar om toegang te krijgen tot de Azure-bestands share. 
- Poort 1433 (TCP uitgaand) moet worden geopend als de uitgever/distributeur zich op een beheerd exemplaar bevindt en de abonnee on-premises is.
- Alle typen replicatie deelnemers (uitgever, distributeur, pull-abonnee en push-abonnee) kunnen worden geplaatst op beheerde exemplaren, maar de uitgever en de Distributor moeten zowel in de Cloud als in beide on-premises zijn.
- Als de uitgever, de Distributor en/of de abonnee zich in verschillende virtuele netwerken bevinden, moet u VPN-peering tot stand brengen tussen elke entiteit, zodat er VPN-peering is tussen de uitgever en de Distributor en/of er VPN-peering is tussen de Distributor en de abonnee. 


>[!NOTE]
> - Fout 53 kan optreden bij het maken van verbinding met een Azure Storage-bestand als de uitgaande netwerk beveiligings groep (NSG) poort 445 wordt geblokkeerd wanneer de Distributor een exemplaar database is en de abonnee on-premises is. [Werk de vNet-NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) bij om dit probleem op te lossen. 


### <a name="compare-data-sync-with-transactional-replication"></a>Gegevens synchronisatie met transactionele replicatie vergelijken

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| Voordelen | -Actief-actief ondersteuning<br/>-Bi-richting tussen on-premises en Azure SQL Database | -Laagste latentie<br/>-Transactionele consistentie<br/>-Bestaande topologie na migratie opnieuw gebruiken |
| Nadelen | -5 minuten of meer latentie<br/>-Geen transactionele consistentie<br/>-Hogere gevolgen voor de prestaties | -Kan niet publiceren vanuit Azure SQL Database afzonderlijke data base of gegroepeerde Data Base<br/>-Hoge onderhouds kosten |
| | | |

## <a name="common-configurations"></a>Algemene configuraties

Over het algemeen moeten de uitgever en de Distributor zich in de Cloud of on-premises bestaan. De volgende configuraties worden ondersteund: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Publisher met lokale distributeur in een beheerd exemplaar

![Eén exemplaar als uitgever en distributeur](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Publisher en Distributor worden geconfigureerd binnen één beheerd exemplaar en distribueren wijzigingen naar een ander beheerd exemplaar, één data base, gepoolde data base of SQL Server on-premises. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Publisher met externe Distributor op een beheerd exemplaar

In deze configuratie publiceert één beheerd exemplaar wijzigingen in de Distributor die zijn geplaatst op een ander beheerd exemplaar en die een groot aantal door de bron beheerde instanties kunnen leveren en wijzigingen kan distribueren naar een of meer doelen voor een beheerd exemplaar, één data base, gepoolde data base of SQL Server.

![Afzonderlijke instanties voor Publisher en Distributor](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Publisher en Distributor worden geconfigureerd op twee beheerde exemplaren. Er zijn enkele beperkingen met deze configuratie: 

- Beide beheerde instanties bevinden zich in hetzelfde vNet.
- Beide beheerde instanties bevinden zich op dezelfde locatie.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Publisher en distributeur on-premises met een abonnee op een enkele, gegroepeerde en exemplaar database 

![Azure SQL-Data Base als abonnee](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
In deze configuratie is een Azure SQL Database (één, gegroepeerde en exemplaar-data base) een abonnee. Deze configuratie biedt ondersteuning voor migratie van on-premises naar Azure. Als een abonnee zich op één of gegroepeerde Data Base bevindt, moet deze in de push-modus zijn.  

## <a name="with-failover-groups"></a>Met failover-groepen

Als geo-replicatie is ingeschakeld voor een instantie van een **Uitgever** of **distributie server** in een [failovergroep](sql-database-auto-failover-group.md), moet de beheerder van het beheerde exemplaar alle publicaties opschonen op de oude primaire en opnieuw configureren op de nieuwe primaire locatie nadat een failover is uitgevoerd. In dit scenario zijn de volgende activiteiten nodig:

1. Stop alle replicatie taken die worden uitgevoerd op de data base, indien aanwezig.
2. Voer het volgende script uit op de Publisher-data base om de meta gegevens van uw abonnement uit Publisher te verwijderen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. De meta gegevens van het abonnement op de abonnee weghalen. Voer het volgende script uit op de abonnements database op het exemplaar van de abonnee:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Haal alle replicatie objecten geforceerd van Publisher door het volgende script uit te voeren in de gepubliceerde Data Base:

   ```sql
   EXEC sp_removedbreplication
   ```

1. De oude Distributor geforceerd verwijderen van de oorspronkelijke primaire instantie (als er een failover wordt uitgevoerd naar een oud primair exemplaar dat is gebruikt voor een distributeur). Voer het volgende script uit op de hoofd database in het oude beheerde exemplaar van de distributie server:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Als geo-replicatie is ingeschakeld voor een exemplaar van een **abonnee** in een failovergroep, moet de publicatie worden geconfigureerd om verbinding te maken met het listener-eind punt van de failover-groep voor het beheerde exemplaar van de abonnee. In het geval van een failover is de volgende actie door de beheerder van de beheerde instantie afhankelijk van het type failover dat heeft plaatsgevonden: 

- Voor een failover zonder gegevens verlies blijft replicatie na een failover werken. 
- Voor een failover met gegevens verlies zal de replicatie ook worden uitgevoerd. De verloren wijzigingen worden opnieuw gerepliceerd. 
- Voor een failover met gegevens verlies, maar het gegevens verlies valt buiten de Bewaar periode voor de distributie database, moet de beheerder van het beheerde exemplaar de abonnee database opnieuw initialiseren. 

## <a name="next-steps"></a>Volgende stappen

- [Replicatie tussen een MI-Publisher en een abonnee configureren](replication-with-sql-database-managed-instance.md)
- [Replicatie tussen een MI-Publisher, MI-distributeur en SQL Server abonnee configureren](sql-database-managed-instance-configure-replication-tutorial.md)
- [Maak een publicatie](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Maak een push-abonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) met behulp van de naam van de Azure SQL database-server als abonnee (bijvoorbeeld `N'azuresqldbdns.database.windows.net` en de Azure SQL database naam als de doel database (bijvoorbeeld **AdventureWorks**). )


Zie de volgende zelf studies voor meer informatie over het configureren van transactionele replicatie:



## <a name="see-also"></a>Zie ook  

- [Replicatie met een MI-en failover-groep](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replicatie naar SQL Database](replication-to-sql-database.md)
- [Replicatie naar een beheerd exemplaar](replication-with-sql-database-managed-instance.md)
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
