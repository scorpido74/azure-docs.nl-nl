---
title: Backup-en Restore-data bases-Azure SQL Edge (preview)
description: Meer informatie over de mogelijkheden voor back-up en herstel in Azure SQL Edge (preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596951"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Back-up en herstel van data bases in Azure SQL Edge (preview-versie) 

Azure SQL Edge is gebaseerd op de nieuwste versies van de Microsoft SQL Server data base-engine op Linux, waardoor de database mogelijkheden voor back-up en herstel worden geboden als die beschikbaar zijn in SQL Server on Linux en SQL Server in containers worden uitgevoerd. Het onderdeel back-up en herstel biedt een essentiële beveiliging voor het beveiligen van gegevens die zijn opgeslagen in uw Azure SQL Edge-data bases. Om het risico op onherstelbaar gegevens verlies te minimaliseren, is het raadzaam om regel matig een back-up van uw data bases te maken om de wijzigingen in uw gegevens regel matig te hand haven. Een goed geplande strategie voor back-up en herstel beschermt databases tegen gegevensverlies veroorzaakt door een verscheidenheid aan fouten. Test uw strategie door een set van back-ups te herstellen en vervolgens uw database terug te zetten zodat u voorbereid bent en doeltreffend kunt reageren op een noodgeval.

Zie back-up [en herstel van SQL server-data bases](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)voor meer informatie over waarom back-ups belang rijk zijn.

Azure SQL Edge ondersteunt het maken van back-ups naar en herstellen vanuit lokale opslag of vanuit Azure-blobs. Voor meer informatie over het maken van back-ups en het herstellen van Azure Blob Storage raadpleegt u [SQL Server Backup en Restore with Microsoft Azure Blob Storage service](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) en [SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Back-ups maken van een data base in Azure SQL Edge

Azure SQL Edge ondersteunt dezelfde back-uptypen als wordt ondersteund door SQL Server. Raadpleeg [overzicht van back-ups](/sql/relational-databases/backup-restore/backup-overview-sql-server/)voor een volledige lijst met de back-uptypen die worden ondersteund in SQL Server.

> [!IMPORTANT] 
> Data bases die zijn gemaakt in Azure SQL Edge maken standaard gebruik van eenvoudig herstel model. Als dergelijke logboek back-ups niet kunnen worden uitgevoerd op deze data bases. Als er een logboek back-up moet worden uitgevoerd op deze data bases, moet een beheerder het herstel model van de data base wijzigen in een volledig herstel model. Zie [overzicht van herstel](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)modellen voor een volledige lijst met herstel modellen die door SQL Server worden ondersteund.

### <a name="backup-to-local-disk"></a>Back-up op lokale schijf

In het onderstaande voor beeld wordt de Transact-SQL-opdracht BACKUP data base gebruikt voor het maken van een back-up van een data base in de container. In dit voor beeld wordt een nieuwe map met de naam ' back-up ' gemaakt voor het opslaan van de back-upbestanden.

1. Maak een map voor de back-ups. Deze opdracht moet worden uitgevoerd op de host waar de Azure SQL Edge-container wordt uitgevoerd. Vervang in de onderstaande opdracht **<AzureSQLEdge_Container_Name>** door de naam van de Azure SQL Edge-container in uw implementatie.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Maak verbinding met het Azure SQL Edge-exemplaar met behulp van SQL Server Management Studio (SSMS) of gebruik Azure Data Studio (ADS) en voer de opdracht Backup Data Base uit om de back-up van uw gebruikers database te maken. In het onderstaande voor beeld maken we de back-up van de *IronOreSilicaPrediction* -data base.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Nadat u de opdracht hebt uitgevoerd en de back-up van de data base is voltooid, ziet u berichten die vergelijkbaar zijn met de volgende in het gedeelte met resultaten van SSMS of ADS.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Back-up naar URL

Azure SQL Edge ondersteunt back-ups naar pagina-blobs en blok-blobs. Voor meer informatie over pagina-blobs en blok-blobs verwijzen we naar de [back-up om de BLOB tegenover de pagina te blok keren](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). In het onderstaande voor beeld wordt er een back-up van de Data Base *IronOreSilicaPrediction* gemaakt naar een blok-blob. 

1. De eerste stap bij het configureren van back-ups voor blok-blobs is het genereren van een SAS-token (Shared Access Signature) dat kan worden gebruikt om een SQL Server referentie te maken op Azure SQL Edge. Het script maakt een Shared Access Signature dat is gekoppeld aan een opgeslagen toegangs beleid. Zie [Shared Access Signatures, Part 1: uitleg over het SAS-model](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)voor meer informatie. Het script schrijft ook de T-SQL-opdracht die is vereist om de referentie te maken op SQL Server. In het onderstaande script wordt ervan uitgegaan dat u al een Azure-abonnement hebt met een opslag account en een opslag container voor de back-ups.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Nadat het script is uitgevoerd, kopieert u de opdracht referentie maken naar een query hulp programma, maakt u verbinding met een exemplaar van SQL Server en voert u de opdracht uit om de referentie te maken met de Shared Access Signature.

2. Maak verbinding met het Azure SQL Edge-exemplaar met behulp van SQL Server Management Studio (SSMS) of gebruik Azure Data Studio (ADS) en maak de referentie met behulp van de opdracht uit de vorige stap. Zorg ervoor dat u de opdracht referentie maken vervangt door de werkelijke uitvoer van de vorige stap.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Met de volgende opdracht maakt u een back-up van de *IronOreSilicaPrediction* naar de Azure-opslag container.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Een data base in Azure SQL Edge herstellen

Azure SQL Edge ondersteunt het herstellen van een lokale schijf, een netwerk locatie of een Azure Blob Storage-account. Zie [overzicht van herstel en herstel](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)voor een overzicht van herstel en herstel in SQL Server. Voor een overzicht van het eenvoudige herstel model in SQL Server raadpleegt u [complete data base restores (Simple Recovery model)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Herstellen vanaf lokale schijf

In dit voor beeld wordt de *IronOreSilicaPrediction* -back-up gebruikt die in het vorige voor beeld is uitgevoerd voor herstel als een nieuwe Data Base met een andere naam.

1. Als het back-upbestand van de data base niet al in de container aanwezig is, kunt u de onderstaande opdracht gebruiken om het bestand naar de container te kopiëren. In het volgende voor beeld wordt ervan uitgegaan dat het back-upbestand aanwezig is op de lokale host en wordt gekopieerd naar de map/var/opt/MSSQL/Backup naar een Azure SQL Edge-container met de naam sql1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Maak verbinding met het Azure SQL Edge-exemplaar met behulp van SQL Server Management Studio (SSMS) of gebruik Azure Data Studio (ADS) om de opdracht Restore uit te voeren. In het onderstaande voor beeld is **IronOrePredictDB. bak** teruggezet om een nieuwe Data Base te maken **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Nadat u de herstel opdracht hebt uitgevoerd en de herstel bewerking is geslaagd, ziet u de berichten die vergelijkbaar zijn met het volgende in het uitvoer venster. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Herstellen vanaf URL

Azure SQL Edge biedt ook ondersteuning voor het herstellen van een Data Base vanuit een Azure Storage-account. Herstel bewerkingen kunnen worden uitgevoerd vanuit de blok-blobs of pagina-BLOB-back-ups. In het onderstaande voor beeld wordt het back-upbestand van de *IronOreSilicaPrediction_2020_04_16. bak* -Data Base op een blok-BLOB teruggezet om de data base te maken *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


