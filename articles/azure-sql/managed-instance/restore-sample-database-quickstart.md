---
title: 'Quickstart: Een back-up terugzetten (SSMS)'
titleSuffix: Azure SQL SQL Managed Instance
description: In deze quickstart leert u een database-back-up terugzetten in een Azure SQL Server Managed Instance met behulp van SQL Server Management (SSMS).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: c750912e942d5dadeb97e6675427f1730912704a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267576"
---
# <a name="quickstart-restore-a-database-to-an-azure-sql-managed-instance-with-ssms"></a>Quickstart: Een database terugzetten in een Azure SQL Managed Instance met SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze quickstart gebruikt u SQL Server Management Studio (SSMS) om een database (het standaardback-upbestand van Wide World Importers) te herstellen van Azure Blob Storage naar een [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Zie [SQL Managed Instance migration using DMS](../../dms/tutorial-sql-server-to-managed-instance.md) (Migratie van SQL Managed Instance met behulp van DMS) voor meer informatie over migratie met behulp van de Azure Database Migration Service (DMS).
> Zie [Migratie van SQL Server naar SQL Managed Instance](migrate-to-instance-from-sql-server.md) voor meer informatie over de diverse migratiemethoden.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Maakt gebruik van resources uit de quickstart [Een SQL Managed Instance maken](instance-create-quickstart.md).
- De laatste versie van [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) moet zijn geïnstalleerd.
- Er moet gebruik worden gemaakt van SSMS voor verbinding met uw SQL Managed Instance. Raadpleeg de volgende snelstart voor het maken van verbinding:
  - [Openbaar eindpunt inschakelen](public-endpoint-configure.md) op SQL Managed Instance: dit is de aanbevolen methode voor deze zelfstudie.
  - [Verbinding maken met een SQL Managed Instance vanaf een Azure-VM](connect-vm-instance-configure.md)
  - [Vanuit on-premises een punt-naar-site-verbinding configureren naar een SQL Managed Instance](point-to-site-p2s-configure.md).

> [!NOTE]
> Zie [Back-up van SQL Server naar URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017) voor meer informatie over het maken van een back-up van een SQL Server-database met behulp van Azure Blob Storage en een [SAS-sleutel (Shared Access Signature)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) en het herstellen van de back-up.

## <a name="restore-from-a-backup-file"></a>Herstellen vanuit een back-upbestand

Volg in SSMS (SQL Server Management Studio) deze stappen om de Wide World Importers-database te herstellen naar uw SQL Managed Instance. Het databaseback-upbestand wordt opgeslagen in een vooraf geconfigureerd Azure Blob Storage-account.

1. Open SSMS en maak verbinding met uw SQL Managed Instance.
2. Klik in **Objectverkenner** met de rechtermuisknop op uw SQL Managed Instance en selecteer **Nieuwe query** om een nieuw queryvenster te openen.
3. Voer het volgende SQL-script uit. Dit maakt gebruik van een vooraf geconfigureerd opslagaccount en SAS-sleutel om [een referentie te maken](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) in uw SQL Managed Instance.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![referentie maken](./media/restore-sample-database-quickstart/credential.png)

4. Voer het volgende script uit om uw referentie te controleren. Dit maakt gebruik van een [container](https://azure.microsoft.com/services/container-instances/)-URL voor het ophalen van een lijst met back-upbestanden.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lijst met bestanden](./media/restore-sample-database-quickstart/file-list.png)

5. Voer het volgende script uit om de Wide World Importers-database te herstellen.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![De pagina Restore](./media/restore-sample-database-quickstart/restore.png)

6. Voer het volgende script uit om de status van het herstellen te volgen.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Wanneer het herstellen is voltooid, bekijkt u de database in Objectverkenner. U kunt controleren of het herstellen van de database is voltooid met behulp van de weergave [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).

> [!NOTE]
> De herstelbewerking voor de database is asynchroon en kan opnieuw worden uitgevoerd. Mogelijk wordt er een fout weergegeven in SQL Server Management Studio als de verbinding wordt verbroken of als er een time-out optreedt. Herstellen van de database wordt nog steeds op de achtergrond uitgevoerd in Azure SQL Database, en u kunt de voortgang van het herstel bekijken in de weergaven [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).
> In sommige fasen van het herstelproces ziet u een unieke id in plaats van de werkelijke databasenaam in de systeemweergaven. U kunt [hier](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement) meer lezen over de verschillen in gedrag van de `RESTORE`-instructie.

## <a name="next-steps"></a>Volgende stappen

- Als in stap 5 het terugzetten van een database wordt beëindigd met het bericht met id 22003, maakt u een nieuw back-upbestand met back-upcontrolesommen en voert u het herstellen opnieuw uit. Zie [Enable or disable backup checksums during backup or restore](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server) (back-upcontrolesommen in- of uitschakelen tijdens back-up of terugzetten).
- Zie [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) (Aanbevolen procedures en probleemoplossing voor back-up van SQL Server naar URL) voor het oplossen van problemen met een back-up naar een URL.
- Zie [Uw toepassingen verbinding laten maken met SQL Managed Instance](connect-application-instance.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Voor informatie over het uitvoeren van een query met een van uw favoriete hulpprogramma's of talen, raadpleegt u [Snelstarts: verbinding maken met Azure SQL-database en hierop query's uitvoeren](../database/connect-query-content-reference-guide.md).
