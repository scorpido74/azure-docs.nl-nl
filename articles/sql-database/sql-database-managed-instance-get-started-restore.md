---
title: Een back-up herstellen naar beheerde instantie
description: Herstel een back-up van de database voor een beheerd exemplaar voor Azure SQL Database met behulp van SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 37f7366d6622356017e458fb8f893b0be0851335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73825697"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Snelstart: een database herstellen naar een beheerde instantie

In deze quickstart gebruikt u SQL Server Management Studio (SSMS) om een database (het Wide World Importers - Standard Backup-bestand) van Azure Blob-opslag te herstellen naar een Azure SQL Database [Managed Instance.](sql-database-managed-instance.md)

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Zie [Managed Instance migration using DMS](../dms/tutorial-sql-server-to-managed-instance.md) (Migratie van een beheerd exemplaar via DMS) voor meer informatie over migratie met behulp van de Azure Database Migration Service (DMS).
> Zie [Migratie van SQL Server-exemplaar naar beheerd exemplaar voor Azure SQL Database](sql-database-managed-instance-migrate.md) voor meer informatie over de diverse migratiemethoden.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Maakt gebruik van resources uit de snelstart [Een beheerd exemplaar maken](sql-database-managed-instance-get-started.md).
- De meest recente versie van [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) moet op uw computer zijn geïnstalleerd.
- Er moet gebruik worden gemaakt van SSMS voor verbinding met uw beheerde exemplaar. Raadpleeg de volgende snelstart voor het maken van verbinding:
  - [Openbare eindpunt inschakelen](sql-database-managed-instance-public-endpoint-configure.md) op Beheerde instantie - dit wordt aanbevolen aanpak voor deze zelfstudie.
  - [Verbinding maken met een Azure SQL Database Managed Instance vanaf een Azure-VM](sql-database-managed-instance-configure-vm.md)
  - [Configureer een point-to-site verbinding met een Azure SQL Database Managed Instance vanuit on-premises](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Zie [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)voor meer informatie over het maken van back-ups en het herstellen van een SQL Server-database met Azure Blob-opslag en een [SAS-toets (Shared Access Signature).](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="restore-the-database-from-a-backup-file"></a>De database herstellen met een back-upbestand

Volg in SSMS deze stappen om de Wide World Importers-database te herstellen naar uw beheerde exemplaar. Het databaseback-upbestand wordt opgeslagen in een vooraf geconfigureerd Azure Blob Storage-account.

1. Open SMSS en maak verbinding met uw beheerde exemplaar.
2. Klik in het menu links met de rechtermuisknop op uw beheerde exemplaar en selecteer **Nieuwe query** om een nieuw queryvenster te openen.
3. Voer het volgende SQL-script uit. Dit maakt gebruik van een vooraf geconfigureerd opslagaccount en SAS-sleutel om [een referentie te maken](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) in uw beheerde exemplaar.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![referentie maken](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Voer het volgende script uit om uw referentie te controleren. Dit maakt gebruik van een [container](https://azure.microsoft.com/services/container-instances/)-URL voor het ophalen van een lijst met back-upbestanden.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lijst met bestanden](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Voer het volgende script uit om de Wide World Importers-database te herstellen.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![De pagina Restore](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Voer het volgende script om de status van het herstellen te volgen.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Wanneer het herstel is voltooid, bekijkt u de database in Object Explorer. U controleren of het herstel van de database is voltooid met behulp van [de sys.dm_operation_status-weergave.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)

> [!NOTE]
> Databaseherstelbewerking is asynchroon en opnieuw kan worden afgesteld. Mogelijk krijgt u een fout als SQL Server Management Studio als de verbinding wordt verbroken of als een time-out verloopt. Azure SQL Database blijft proberen de database op de achtergrond te herstellen en u de voortgang van het herstel bijhouden met behulp van de weergaven [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)
> In sommige fasen van het herstelproces ziet u unieke id in plaats van de werkelijke databasenaam in de systeemweergaven. Meer `RESTORE` informatie over verschillen in gedrag van [uitspraken vindt u hier](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) (Aanbevolen procedures en probleemoplossing voor back-up van SQL Server naar URL) voor het oplossen van problemen met een back-up naar een URL.
- Zie [Uw toepassingen verbinding laten maken met Managed Instance](sql-database-managed-instance-connect-app.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Zie [Quickstarts: Azure SQL Database Connect en Query](sql-database-connect-query.md)als u wilt query's met uw favoriete hulpprogramma's of talen.
