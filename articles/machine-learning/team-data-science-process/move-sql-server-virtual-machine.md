---
title: Gegevens verplaatsen naar een virtuele SQL Server-machine - Team Data Science-proces
description: Gegevens verplaatsen van platte bestanden of van een on-premises SQL Server naar SQL Server op Azure VM.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721367"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Gegevens verplaatsen naar SQL Server op een virtuele Azure-machine

In dit artikel worden de opties beschreven voor het verplaatsen van gegevens van platte bestanden (CSV- of TSV-indelingen) of van een on-premises SQL Server naar SQL Server op een virtuele Azure-machine. Deze taken voor het verplaatsen van gegevens naar de cloud maken deel uit van het Team Data Science Process.

Zie [Gegevens verplaatsen naar een Azure SQL Database voor Azure Machine Learning](move-sql-azure.md)voor een onderwerp dat de opties voor het verplaatsen van gegevens naar een Azure SQL-database voor Azure Machine Learning beschrijft.

In de volgende tabel worden de opties voor het verplaatsen van gegevens naar SQL Server op een virtuele Azure-machine samengevat.

| <b>Bron</b> | <b>BESTEMMING: SQL Server op Azure VM</b> |
| --- | --- |
| <b>Vlak bestand</b> |1. <a href="#insert-tables-bcp">Command-line bulk copy utility (BCP)</a><br> <a href="#insert-tables-bulkquery">2. SQL-query voor het invoegen van bulk</a><br> 3. <a href="#sql-builtin-utilities">Grafische ingebouwde hulpprogramma's in SQL Server</a> |
| <b>On-Premises SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Een SQL Server-database implementeren in een Microsoft Azure VM-wizard</a><br> 2. <a href="#export-flat-file">Exporteren naar een plat bestand</a><br> <a href="#sql-migration">3. Wizard SQL-databasemigratie</a> <br> 4. <a href="#sql-backup">Database back-up en herstellen</a><br> |

In dit document wordt ervan uitgegaan dat SQL-opdrachten worden uitgevoerd vanuit SQL Server Management Studio of Visual Studio Database Explorer.

> [!TIP]
> Als alternatief u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om een pijplijn te maken en te plannen die gegevens verplaatst naar een SQL Server VM op Azure. Zie [Gegevens kopiëren met Azure Data Factory (Activiteit kopiëren)](../../data-factory/copy-activity-overview.md)voor meer informatie.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
Deze zelfstudie gaat ervan uit dat je hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslagaccount**. U gebruikt een Azure-opslagaccount voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel verkrijgen die wordt gebruikt om toegang te krijgen tot de opslag. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md).
* Ingerichte **SQL Server op een Azure VM**. Zie Een [virtuele Azure Server-machine instellen als iPython-laptopserver voor geavanceerde analyses voor](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)instructies.
* Azure **PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie Azure [PowerShell installeren en configureren](/powershell/azure/overview)voor instructies.

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Gegevens verplaatsen van een platte bestandsbron naar SQL Server op een Azure VM
Als uw gegevens zich in een plat bestand bevinden (gerangschikt in een rij/kolomindeling), kunnen deze via de volgende methoden worden verplaatst naar SQL Server VM op Azure:

1. [Command-line bulk copy utility (BCP)](#insert-tables-bcp)
2. [SQL-query voor bulkinvoegen](#insert-tables-bulkquery)
3. [Grafische ingebouwde hulpprogramma's in SQL Server (importeren/exporteren, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Command-line bulk copy utility (BCP)
BCP is een command-line hulpprogramma geïnstalleerd met SQL Server en is een van de snelste manieren om gegevens te verplaatsen. Het werkt voor alle drie SQL Server-varianten (On-premises SQL Server, SQL Azure en SQL Server VM op Azure).

> [!NOTE]
> **Waar moeten mijn gegevens zijn voor BCP?**  
> Hoewel het niet nodig is, zorgt het hebben van bestanden met brongegevens op dezelfde machine als de doelSQL Server voor snellere overdrachten (netwerksnelheid versus lokale schijf IO-snelheid). U de platte bestanden met gegevens verplaatsen naar de machine waar SQL Server is geïnstalleerd met behulp van verschillende hulpprogramma's voor het kopiëren van bestanden, zoals [AZCopy,](../../storage/common/storage-use-azcopy.md) [Azure Storage Explorer](https://storageexplorer.com/) of windows copy/paste via Remote Desktop Protocol (RDP).
>
>

1. Controleer of de database en de tabellen zijn gemaakt in de doelSQL Server-database. Hier is een voorbeeld van hoe `Create Database` `Create Table` dat te doen met behulp van de en commando's:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Genereer het opmaakbestand dat het schema voor de tabel beschrijft door de volgende opdracht uit te geven vanaf de opdrachtregel van de machine waar bcp is geïnstalleerd.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Voeg de gegevens in de database in met de opdracht bcp, die moet werken vanaf de opdrachtregel wanneer SQL Server op dezelfde machine is geïnstalleerd:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP-inserts optimaliseren** Raadpleeg het volgende artikel ['Guidelines for Optimizing Bulk Import'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) om dergelijke inserts te optimaliseren.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Invoegsels parallelmaken voor snellere gegevensbeweging
Als de gegevens die u verplaatst groot zijn, u dingen versnellen door tegelijkertijd meerdere BCP-opdrachten parallel in een PowerShell Script uit te voeren.

> [!NOTE]
> **Big data Inname** Als u het laden van gegevens voor grote en zeer grote gegevenssets wilt optimaliseren, partitiet u uw logische en fysieke databasetabellen met meerdere bestandsgroepen en partitietabellen. Zie [Parallelle load SQL-partitietabellen](parallel-load-sql-partitioned-tables.md)voor meer informatie over het maken en laden van gegevens naar partitietabellen.
>
>

In het volgende voorbeeld powershell-script worden parallelle inserts met bcp aangetoond:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-query voor bulkinvoegen
[Bulk Insert SQL Query](https://msdn.microsoft.com/library/ms188365) kan worden gebruikt om gegevens te importeren in de database uit rij-/kolomgebaseerde bestanden (de ondersteunde typen worden behandeld in het onderwerp[Gegevens voorbereiden voor bulkexport of importeren (SQL Server).](https://msdn.microsoft.com/library/ms188609)

Hier volgen enkele voorbeeldopdrachten voor Bulk Insert:  

1. Analyseer uw gegevens en stel alle aangepaste opties in voordat u importeert om ervoor te zorgen dat de SQL Server-database dezelfde indeling aanneemt voor speciale velden zoals datums. Hier vindt u een voorbeeld van het instellen van de datumnotatie als jaar-maanddag (als uw gegevens de datum in de opmaak van de jaarmaand bevatten):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Gegevens importeren met bulkimportoverzichten:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Ingebouwde hulpprogramma's in SQL Server
U SQL Server Integration Services (SSIS) gebruiken om gegevens te importeren in SQL Server VM op Azure vanuit een plat bestand.
SSIS is beschikbaar in twee studioomgevingen. Zie Integration [Services (SSIS) en Studio-omgevingen](https://technet.microsoft.com/library/ms140028.aspx)voor meer informatie:

* Zie [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx) voor meer informatie over SQL Server Data Tools  
* Zie wizard SQL Server importeren [en exporteren](https://msdn.microsoft.com/library/ms141209.aspx) voor meer informatie over de wizard Importeren/exporteren

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Gegevens verplaatsen van on-premises SQL Server naar SQL Server op een Azure VM
U ook de volgende migratiestrategieën gebruiken:

1. [Een SQL Server-database implementeren in een Microsoft Azure VM-wizard](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporteren naar vlak bestand](#export-flat-file)
3. [Wizard SQL-databasemigratie](#sql-migration)
4. [Database back-ups maken en herstellen](#sql-backup)

We beschrijven hieronder elk van deze opties:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Een SQL Server-database implementeren in een Microsoft Azure VM-wizard
De **wizard Een SQL Server-database implementeren naar een Microsoft Azure VM-wizard** is een eenvoudige en aanbevolen manier om gegevens te verplaatsen van een on-premises SQL Server-instantie naar SQL Server op een Azure VM. Zie [Een database migreren naar SQL Server op een Azure VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)voor gedetailleerde stappen en een discussie over andere alternatieven.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exporteren naar vlak bestand
Verschillende methoden kunnen worden gebruikt om bulk-exportgegevens van een On-Premises SQL Server zoals gedocumenteerd in het sql [server-onderwerp (Bulk Import and Export of Data).](https://msdn.microsoft.com/library/ms175937.aspx) Dit document behandelt het Bulk Copy Program (BCP) als voorbeeld. Zodra gegevens naar een vlak bestand zijn geëxporteerd, kan deze worden geïmporteerd naar een andere SQL-server met behulp van bulkimport.

1. De gegevens van on-premises SQL Server exporteren naar een bestand met het bcp-hulpprogramma als volgt

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Maak de database en de tabel op `create database` SQL `create table` Server VM op Azure met het tabelschema dat in stap 1 wordt geëxporteerd.
3. Maak een opmaakbestand voor het beschrijven van het tabelschema van de gegevens die worden geëxporteerd/geïmporteerd. Details van het opmaakbestand worden beschreven in [Een opmaakbestand maken (SQL Server).](https://msdn.microsoft.com/library/ms191516.aspx)

    Bestandsgeneratie opmaken bij het uitvoeren van BCP vanaf de SQL Server-machine

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Bestandsgeneratie opmaken wanneer BCP op afstand wordt uitgevoerd tegen een SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Gebruik een van de methoden die in de sectie [Gegevens verplaatsen van bestandsbron](#filesource_to_sqlonazurevm) wordt beschreven om de gegevens in platte bestanden naar een SQL Server te verplaatsen.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Wizard SQL-databasemigratie
[Wizard SQL Server-databasemigratie](https://sqlazuremw.codeplex.com/) biedt een gebruiksvriendelijke manier om gegevens te verplaatsen tussen twee SQL-server-exemplaren. Hiermee kan de gebruiker het gegevensschema tussen bronnen en doeltabellen toewijzen, kolomtypen en diverse andere functionaliteiten kiezen. Het maakt gebruik van bulk copy (BCP) onder de covers. Hieronder ziet u een schermafbeelding van het welkomstscherm voor de wizard SQL-databasemigratie.  

![Wizard SQL Server-migratie][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Database back-ups maken en herstellen
SQL Server-ondersteuning:

1. [Database back-up en herstel functionaliteit](https://msdn.microsoft.com/library/ms187048.aspx) (zowel naar een lokaal bestand of bacpac exporteren naar blob) en [Data Tier Applications](https://msdn.microsoft.com/library/ee210546.aspx) (met behulp van bacpac).
2. Mogelijkheid om SQL Server VM's op Azure rechtstreeks te maken met een gekopieerde database of te kopiëren naar een bestaande SQL Azure-database. Zie [De wizard Database kopiëren gebruiken](https://msdn.microsoft.com/library/ms188664.aspx)voor meer informatie.

Hieronder vindt u een screenshot van de opties Database back-up/restore van SQL Server Management Studio.

![SQL Server Import Tool][1]

## <a name="resources"></a>Resources
[Een database migreren naar SQL Server op een Azure VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Overzicht van SQL Server op virtuele machines in Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
