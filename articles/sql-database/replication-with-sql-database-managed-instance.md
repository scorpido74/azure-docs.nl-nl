---
title: Replicatie configureren in een beheerde instantiedatabase
description: Meer informatie over het configureren van transactionele replicatie tussen een Azure SQL Database beheerde instantieuitgever/distributeur en beheerde instantieabonnee.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299070"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Replicatie configureren in een azure SQL Database beheerde instantiedatabase

Met transactionele replicatie u gegevens repliceren naar een azure SQL Database managed instance database vanuit een SQL Server-database of een andere instantiedatabase. 

In dit artikel ziet u hoe u replicatie configureert tussen een uitgever/distributeur van beheerde instanties en een beheerde instantieabonnee. 

![Repliceren tussen twee beheerde exemplaren](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

U transactionele replicatie ook gebruiken om wijzigingen in een instantie in azure SQL Database beheerd exemplaar te pushen naar:

- Een SQL Server-database.
- Eén database in Azure SQL Database.
- Een gepoolde database in een elastische groep azure SQL Database.
 
Transactionele replicatie is in openbare preview op [azure SQL Database managed instance](sql-database-managed-instance.md). Een beheerde instantie kan uitgevers-, distributeur- en abonneedatabases hosten. Zie [transactionele replicatieconfiguraties](sql-database-managed-instance-transactional-replication.md#common-configurations) voor beschikbare configuraties.

  > [!NOTE]
  > - Dit artikel is bedoeld om een gebruiker te begeleiden bij het configureren van replicatie met een door Azure Database beheerde instantie van begin tot eind, te beginnen met het maken van de brongroep. Als u al geïmplementeerde instanties hebt geïmplementeerd, gaat u verder naar [Stap 4](#4---create-a-publisher-database) om uw uitgeversdatabase te maken of [stap 6](#6---configure-distribution) als u al een uitgever- en abonneedatabase hebt en klaar bent om replicatie te configureren.  
  > - In dit artikel configureert u uw uitgever en distributeur op hetzelfde beheerde exemplaar. Zie de zelfstudie Replicatie configureren tussen een [MI-uitgever en een MI-distributeur](sql-database-managed-instance-configure-replication-tutorial.md)om de distributeur op een afzonderlijke manged-instantie te plaatsen. 

## <a name="requirements"></a>Vereisten

Voor het configureren van een beheerde instantie om te functioneren als uitgever en/of distributeur moet worden geleid tot:

- Dat de uitgever beheerde instantie zich op hetzelfde virtuele netwerk bevindt als de distributeur en de abonnee, of [vNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) is vastgesteld tussen de virtuele netwerken van alle drie de entiteiten. 
- Connectiviteit maakt gebruik van SQL-verificatie tussen replicatiedeelnemers.
- Een Azure Storage Account share voor de replicatiewerkmap.
- Poort 445 (TCP-uitgaande) is geopend in de beveiligingsregels van NSG voor de beheerde exemplaren om toegang te krijgen tot het Azure-bestandsshare.  Als u de fout "Kan geen \<verbinding maken met de naam van het Azure Storage-account> met os-fout 53) tegenkomt, moet u een uitgaande regel toevoegen aan de NSG van het juiste SQL Managed Instance-subnet.


 > [!NOTE]
 > Afzonderlijke databases en gepoolde databases in Azure SQL Database kunnen alleen abonnees zijn. 


## <a name="features"></a>Functies

Ondersteunt:

- Transactionele en momentopnamereplicatiemix van SQL Server on-premises en beheerde exemplaren in Azure SQL Database.
- Abonnees kunnen zich in on-premises SQL Server-databases, afzonderlijke databases/beheerde instanties in Azure SQL Database of samengevoegde databases in elastische pools van Azure SQL Database bevinden.
- Eenrichtings- of bidirectionele replicatie.

De volgende functies worden niet ondersteund in een beheerde instantie in Azure SQL Database:

- [Opdatable abonnementen](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Actieve georeplicatie](sql-database-active-geo-replication.md) met transactionele replicatie. In plaats van actieve georeplicatie gebruikt u [Automatisch mislukken,](sql-database-auto-failover-group.md)maar houd er rekening mee dat de publicatie handmatig moet worden [verwijderd](sql-database-managed-instance-transact-sql-information.md#replication) uit de primaire beheerde instantie en opnieuw moet worden gemaakt op de secundaire beheerde instantie na failover.  
 
## <a name="1---create-a-resource-group"></a>1 - Een resourcegroep maken

Gebruik de [Azure-portal](https://portal.azure.com) om een `SQLMI-Repl`resourcegroep met de naam te maken.  

## <a name="2---create-managed-instances"></a>2 - Beheerde exemplaren maken

Gebruik de [Azure-portal](https://portal.azure.com) om twee [beheerde exemplaren](sql-database-managed-instance-create-tutorial-portal.md) te maken op hetzelfde virtuele netwerk en subnet. Noem bijvoorbeeld de twee beheerde instanties:

- `sql-mi-pub`(samen met een aantal tekens voor randomisatie)
- `sql-mi-sub`(samen met een aantal tekens voor randomisatie)

U moet ook [een Azure VM configureren om verbinding te maken met](sql-database-managed-instance-configure-vm.md) uw door Azure SQL Database beheerde exemplaren. 

## <a name="3---create-azure-storage-account"></a>3 - Azure Storage-account maken

[Maak een Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) voor de werkmap en maak vervolgens een [bestandsshare](../storage/files/storage-how-to-create-file-share.md) in het opslagaccount. 

Kopieer het pad voor bestandsshare in de indeling van:`\\storage-account-name.file.core.windows.net\file-share-name`

Voorbeeld: `\\replstorage.file.core.windows.net\replshare`

Kopieer de opslagtoegangssleutels in de indeling van:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Voorbeeld: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie . 

## <a name="4---create-a-publisher-database"></a>4 - Een uitgeversdatabase maken

Maak verbinding `sql-mi-pub` met uw beheerde instantie met SQL Server Management Studio en voer de volgende Transact-SQL -code (T-SQL) uit om uw uitgeversdatabase te maken:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Een abonneedatabase maken

Maak verbinding `sql-mi-sub` met uw beheerde instantie met SQL Server Management Studio en voer de volgende T-SQL-code uit om uw lege abonneedatabase te maken:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Distributie configureren

Maak verbinding `sql-mi-pub` met uw beheerde instantie met SQL Server Management Studio en voer de volgende T-SQL-code uit om uw distributiedatabase te configureren. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Uitgever configureren om distributeur te gebruiken 

Wijzig de queryuitvoering in de `sql-mi-pub` [SQLCMD-modus](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) in uw door uitgever beheerde instantie en voer de volgende code uit om de nieuwe distributeur bij uw uitgever te registreren. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Zorg ervoor dat u alleen`\`backslashes ( ) gebruiken voor de file_storage parameter. Het gebruik van`/`een voorwaartse slash ( ) kan een fout veroorzaken bij het maken van verbinding met de bestandsshare. 

Dit script configureert een lokale uitgever op de beheerde instantie, voegt een gekoppelde server toe en maakt een set taken voor de SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - Publicatie en abonnee maken

Voer met de [SQLCMD-modus](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) het volgende T-SQL-script uit om replicatie voor uw database in te schakelen en replicatie tussen uw uitgever, distributeur en abonnee te configureren. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Agentparameters wijzigen

Azure SQL Database managed instance is currently experiencing some backend issues with connectivity with the replication agents. Terwijl dit probleem wordt verholpen, wordt de tijdelijke oplossing om de inlogtime-outwaarde voor de replicatieagents te verhogen. 

Voer de volgende Opdracht T-SQL uit op de uitgever om de time-out van het inlogpunt te verhogen: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Voer de volgende Opdracht T-SQL opnieuw uit om de time-out van de aanmelding terug te zetten op de standaardwaarde, mocht u dit moeten doen:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Start alle drie de agents opnieuw om deze wijzigingen toe te passen. 

## <a name="10---test-replication"></a>10 - Replicatie testen

Zodra replicatie is geconfigureerd, u deze testen door nieuwe items op de uitgever in te voegen en de wijzigingen aan de abonnee te zien doorgeven. 

Voer het volgende T-SQL-fragment uit om de rijen op de abonnee weer te geven:

```sql
select * from dbo.ReplTest
```

Voer het volgende T-SQL-fragment uit om extra rijen op de uitgever in te voegen en controleer vervolgens de rijen opnieuw op de abonnee. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de publicatie wilt laten vallen, voert u de volgende opdracht T-SQL uit:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Als u de replicatieoptie uit de database wilt verwijderen, voert u de volgende opdracht T-SQL uit:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Voer de volgende Opdracht T-SQL uit om publicatie en distributie uit te schakelen:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

U uw Azure-resources opschonen door [de beheerde instantiebronnen uit de resourcegroep te verwijderen](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) en vervolgens de resourcegroep `SQLMI-Repl`te verwijderen. 

   
## <a name="see-also"></a>Zie ook

- [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md)
- [Zelfstudie: Transactionele replicatie configureren tussen een MI-uitgever en SQL Server-abonnee](sql-database-managed-instance-configure-replication-tutorial.md)
- [Wat is een beheerde instantie?](sql-database-managed-instance.md)
