---
title: Transactionele replicatie
description: Meer informatie over het gebruik van SQL Server transactionele replicatie met één, samengevoegde en instantiedatabases in Azure SQL Database.
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
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607601"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transactionele replicatie met afzonderlijke, samengevoegde en instantiedatabases in Azure SQL Database

Transactionele replicatie is een functie van Azure SQL Database en SQL Server waarmee u gegevens uit een tabel in Azure SQL Database of een SQL Server repliceren naar de tabellen die in externe databases zijn geplaatst. Met deze functie u meerdere tabellen synchroniseren in verschillende databases.

## <a name="when-to-use-transactional-replication"></a>Wanneer transactionele replicatie gebruiken

Transactionele replicatie is handig in de volgende scenario's:
- Publiceer wijzigingen in een of meer tabellen in een database en distribueer deze naar een of meer SQL Server- of Azure SQL-databases die zijn geabonneerd op de wijzigingen.
- Houd verschillende gedistribueerde databases gesynchroniseerd.
- Migreer databases van de ene SQL Server of beheerde instantie naar een andere database door de wijzigingen continu te publiceren.

## <a name="overview"></a>Overzicht

De belangrijkste onderdelen van transactionele replicatie worden weergegeven in de volgende afbeelding:  

![replicatie met SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

De **uitgever** is een instantie of server die wijzigingen publiceert die zijn aangebracht in sommige tabellen (artikelen) door de updates naar de distributeur te verzenden. Publicatie naar een Azure SQL-database vanaf een on-premises SQL Server wordt ondersteund door de volgende versies van SQL Server:

- SQL Server 2019 (voorbeeld)
- SQL Server 2016 tot SQL 2017
- SQL Server 2014 SP1 CU3 of hoger (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 of hoger (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Voor andere versies van SQL Server die geen ondersteuning bieden voor het publiceren van objecten in Azure, is het mogelijk om de [methode voor het herpubliceren van gegevens](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) te gebruiken om gegevens naar nieuwere versies van SQL Server te verplaatsen. 

De **distributeur** is een instantie of server die wijzigingen in de artikelen van een uitgever verzamelt en distribueert naar de abonnees. De distributeur kan een Azure SQL Database-beheerde instantie of SQL Server zijn (elke versie zolang deze gelijk is aan of hoger is dan de Publisher-versie). 

De **abonnee** is een instantie of server die de wijzigingen in de uitgever ontvangt. Abonnees kunnen afzonderlijke, samengevoegde en instantiedatabases zijn in Azure SQL Database- of SQL Server-databases. Een abonnee in een enkele of samengevoegde database moet worden geconfigureerd als push-abonnee. 

| Rol | Enkele en samengevoegde databases | Instantiedatabases |
| :----| :------------- | :--------------- |
| **Publisher** | Nee | Ja | 
| **Distributeur** | Nee | Ja|
| **Abonnee optrekken** | Nee | Ja|
| **Push-abonnee**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Een pull-abonnement wordt niet ondersteund wanneer de distributeur een Instance-database is en de abonnee niet. 

Er zijn verschillende [soorten replicatie:](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)


| Replicatie | Enkele en samengevoegde databases | Instantiedatabases|
| :----| :------------- | :--------------- |
| [**Standaard transactioneel**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (alleen als abonnee) | Ja | 
| [**Momentopname**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (alleen als abonnee) | Ja|
| [**Replicatie samenvoegen**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nee | Nee|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nee | Nee|
| [**Bidirectionele**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nee | Ja|
| [**Updatable abonnementen**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nee | Nee|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Als u replicatie probeert te configureren met een oudere versie, kan dit leiden tot een \<foutnummer MSSQL_REPL20084 (Het proces kan geen verbinding maken met Abonnee.) en MSSQ_REPL40532 (Kan de servernaam niet openen> die door de aanmelding is aangevraagd. De login is mislukt.)
  > - Als u alle functies van Azure SQL Database wilt gebruiken, moet u de nieuwste versies van [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) en SQL Server Data Tools [(SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)gebruiken.
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Ondersteuningsmatrix voor bijvoorbeeld databases en on-premises systemen
  De matrix voor ondersteuning van replicatie voor bijvoorbeeld databases is dezelfde als die voor sql server on-premises. 
  
| **Publisher**   | **Distributeur** | **Abonnee** |
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
- Een Azure Storage Account share voor de werkmap die wordt gebruikt door replicatie. 
- Poort 445 (TCP-uitgaande) moet worden geopend in de beveiligingsregels van het beheerde instantiesubnet om toegang te krijgen tot het Azure-bestandsshare. 
- Poort 1433 (TCP outbound) moet worden geopend als de uitgever/distributeur zich op een beheerde instantie bevindt en de abonnee dat niet is. Mogelijk moet u ook de regel voor beheerde `allow_linkedserver_outbound` instantie NSG-uitgaande beveiligingsregels wijzigen `internet`voor de poort 1433 **Destination Service-tag** van `virtualnetwork` naar . 
- Alle typen replicatiedeelnemers (uitgever, distributeur, pull-abonnee en pushabonnee) kunnen worden geplaatst op beheerde instanties, maar de uitgever en de distributeur moeten zich zowel in de cloud als on-premises bevinden.
- Als de uitgever, distributeur en/of de abonnee in verschillende virtuele netwerken bestaan, moet VPN-peering tussen elke entiteit worden vastgesteld, zodat er VPN-peering is tussen de uitgever en distributeur, en/of er VPN-peering is tussen de distributeur en abonnee. 


>[!NOTE]
> - U fout 53 tegenkomen wanneer u verbinding maakt met een Azure Storage File als de NSG-poort 445 (uitgaande netwerkbeveiligingsgroep) wordt geblokkeerd wanneer de distributeur een instantiedatabase is en de abonnee on-premises is. [Update de vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) om dit probleem op te lossen. 


### <a name="compare-data-sync-with-transactional-replication"></a>Gegevenssynchronisatie vergelijken met transactionele replicatie

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| Voordelen | - Actieve ondersteuning<br/>- Bi-directioneel tussen on-premises en Azure SQL-database | - Lagere latentie<br/>- Transactionele consistentie<br/>- Bestaande topologie hergebruiken na migratie |
| Nadelen | - 5 min of meer latentie<br/>- Geen transactionele consistentie<br/>- Hogere impact op de prestaties | - Kan niet publiceren vanuit Azure SQL Database enkele database of gepoolde database<br/>- Hoge onderhoudskosten |
| | | |

## <a name="common-configurations"></a>Algemene configuraties

In het algemeen moeten de uitgever en de distributeur zich in de cloud of on-premises bevinden. De volgende configuraties worden ondersteund: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Uitgever bij lokale distributeur op een beheerde instantie

![Enkele instantie als uitgever en distributeur](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Uitgever en distributeur zijn geconfigureerd in één beheerde instantie en distribueren wijzigingen naar andere beheerde instantie, enkele database, samengevoegde database of SQL Server on-premises. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Uitgever met externe distributeur op een beheerde instantie

In deze configuratie publiceert een beheerde instantie wijzigingen in een distributeur die op een andere beheerde instantie zijn geplaatst en die veel bronbeheerde exemplaren kunnen weergeven en wijzigingen kunnen distribueren naar een of meer doelen op beheerde instantie, enkele database, samengevoegde database of SQL Server.

![Afzonderlijke exemplaren voor uitgever en distributeur](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Uitgever en distributeur zijn geconfigureerd op twee beheerde exemplaren. Er zijn enkele beperkingen met deze configuratie: 

- Beide beheerde exemplaren staan op dezelfde vNet.
- Beide beheerde exemplaren bevinden zich op dezelfde locatie.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Uitgever en distributeur on-premises met een abonnee in één, samengevoegde en instantiedatabase 

![Azure SQL DB als abonnee](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
In deze configuratie is een Azure SQL Database (single, pooled en instance database) een abonnee. Deze configuratie ondersteunt migratie van on-premises naar Azure. Als een abonnee zich in een enkele of gepoolde database bevindt, moet deze zich in de pushmodus bevinden.  

## <a name="with-failover-groups"></a>Met failovergroepen

Als georeplicatie is ingeschakeld op een **instantie van een uitgever** of **distributeur** in een [failovergroep,](sql-database-auto-failover-group.md)moet de beheerder van de beheerde instantie alle publicaties op het oude primaire opgeven en opnieuw configureren op het nieuwe primaire voorhoofd nadat een failover heeft plaatsgevonden. In dit scenario zijn de volgende activiteiten nodig:

1. Stop alle replicatietaken die in de database worden uitgevoerd, als die er zijn.
2. Metagegevens van abonnementen van uitgever verwijderen door het volgende script in de uitgeversdatabase uit te voeren:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Drop metagegevens van het abonnement van de abonnee. Voer het volgende script uit in de abonnementsdatabase in de abonnee-instantie:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Alle replicatieobjecten van publisher krachtig laten vallen door het volgende script in de gepubliceerde database uit te voeren:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Krachtig drop oude distributeur van originele primaire instantie (indien niet terug naar een oude primaire die gebruikt worden om een distributeur hebben). Voer het volgende script uit in de hoofddatabase in het oude door de distributeur beheerde exemplaar:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Als geo-replicatie is ingeschakeld op een **abonnee-instantie** in een failovergroep, moet de publicatie worden geconfigureerd om verbinding te maken met het eindpunt van de failovergroeplistener voor het door de abonnee beheerde exemplaar. In het geval van een failover is de volgende actie van de beheerde instantiebeheerder afhankelijk van het type failover dat heeft plaatsgevonden: 

- Voor een failover zonder gegevensverlies blijft replicatie werken na failover. 
- Voor een failover met gegevensverlies werkt replicatie ook. Het zal de verloren veranderingen opnieuw repliceren. 
- Voor een failover met gegevensverlies, maar het gegevensverlies valt buiten de bewaarperiode van de distributiedatabase, moet de beheerde instantiebeheerder de abonnementsdatabase opnieuw initialiseren. 

## <a name="next-steps"></a>Volgende stappen

- [Replicatie configureren tussen een MI-uitgever en abonnee](replication-with-sql-database-managed-instance.md)
- [Replicatie configureren tussen een MI-uitgever, MI-distributeur en SQL Server-abonnee](sql-database-managed-instance-configure-replication-tutorial.md)
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Maak een pushabonnement](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) met de naam van de Azure `N'azuresqldbdns.database.windows.net` SQL Database-server als abonnee (bijvoorbeeld de naam azure SQL-database als doeldatabase (bijvoorbeeld **Adventureworks**. )


Zie de volgende zelfstudies voor meer informatie over het configureren van transactionele replicatie:



## <a name="see-also"></a>Zie ook  

- [Replicatie met een MI- en een failovergroep](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replicatie naar SQL Database](replication-to-sql-database.md)
- [Replicatie naar beheerde instantie](replication-with-sql-database-managed-instance.md)
- [Een publicatie maken](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Een Push-abonnement maken](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Controle (replicatie)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
