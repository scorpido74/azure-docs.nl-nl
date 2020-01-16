---
title: Gegevens verplaatsen naar een computer met SQL Server-machine - Team Data Science Process
description: Gegevens verplaatsen van platte bestanden of van een on-premises SQL Server naar SQL Server op virtuele Azure-machine.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ddc732655c7cfb72c4948f83752440608332915d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974090"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Gegevens verplaatsen naar SQL Server op een virtuele Azure-machine

In dit artikel bevat een overzicht van de opties voor het verplaatsen van gegevens uit platte bestanden (CSV-bestand of TSV indelingen) of van een on-premises SQL Server naar SQL Server op een Azure-machine. Deze taken voor het verplaatsen van gegevens naar de cloud maken deel uit van het Team Data Science Process.

Zie voor een onderwerp dat geeft een overzicht van de opties voor het verplaatsen van gegevens naar een Azure SQL Database voor Machine Learning, [gegevens verplaatsen naar een Azure SQL Database voor Azure Machine Learning](move-sql-azure.md).

De volgende tabel geeft een overzicht van de opties voor het verplaatsen van gegevens naar SQL Server op een Azure-machine.

| <b>BRON</b> | <b>BESTEMMING: SQL Server op Azure VM</b> |
| --- | --- |
| <b>Plat bestand</b> |1. <a href="#insert-tables-bcp">opdracht regel programma voor bulksgewijs kopiëren (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">SQL-query bulksgewijs invoegen</a><br> 3. <a href="#sql-builtin-utilities">ingebouwde grafische Hulpprogram ma's in SQL Server</a> |
| <b>On-Premises SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">een SQL Server-Data Base implementeren op een Microsoft Azure VM-wizard</a><br> 2. <a href="#export-flat-file">exporteren naar een plat bestand</a><br> 3. <a href="#sql-migration">SQL database wizard Migratie</a> <br> 4. <a href="#sql-backup">back-up en herstel van data base</a><br> |

Merk op dat in dit document wordt ervan uitgegaan dat de SQL-opdrachten worden uitgevoerd in SQL Server Management Studio of Visual Studio-Database Explorer.

> [!TIP]
> Als alternatief kunt u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) maken en plannen van een pijplijn waarmee gegevens worden verplaatst naar een SQL Server-VM op Azure. Zie voor meer informatie, [kopiëren van gegevens met Azure Data Factory (Kopieeractiviteit)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Vereisten
In deze zelfstudie wordt ervan uitgegaan dat u hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure storage-account**. U gebruikt een Azure storage-account voor het opslaan van gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u de storage-account hebt gemaakt, moet u het ophalen van de accountsleutel gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Ingericht **SQL Server op een Azure-VM**. Zie voor instructies [een virtuele machine van Azure SQL-Server instellen als IPython Notebook-server voor geavanceerde analyses](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Geïnstalleerd en geconfigureerd **Azure PowerShell** lokaal. Zie voor instructies [hoe u Azure PowerShell installeren en configureren](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Gegevens van de bron van een plat bestand verplaatsen naar SQL Server op een Azure VM
Als uw gegevens zich in een plat bestand (gerangschikt in een indeling rij/kolom), kan het worden verplaatst naar SQL Server-VM op Azure via de volgende methoden:

1. [Hulpprogramma voor opdrachtregelprogramma voor het bulksgewijs kopiëren (BCP)](#insert-tables-bcp)
2. [Bulksgewijs invoegen SQL-Query](#insert-tables-bulkquery)
3. [Grafische ingebouwde hulpprogramma's in SQL Server (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Hulpprogramma voor opdrachtregelprogramma voor het bulksgewijs kopiëren (BCP)
BCP is een opdrachtregelprogramma moet zijn geïnstalleerd met SQL Server en is een van de snelste manieren om gegevens te verplaatsen. De Tool werkt voor alle drie SQL-Server varianten (On-premises SQL Server, SQL Azure en SQL Server-VM op Azure).

> [!NOTE]
> **Waar worden mijn gegevens, moet zijn voor BCP?**  
> Het is niet vereist, kunt dat bestanden met de brongegevens bevinden zich op dezelfde computer als de doel-SQL-Server u snellere overdracht (netwerk snelheid vs lokale schijf i/o-snelheid). U kunt verplaatsen de platte bestanden met gegevens aan de machine waarop SQL Server is geïnstalleerd met behulp van verschillende kopiëren van bestanden hulpprogramma's zoals [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](https://storageexplorer.com/) of windows kopiëren/plakken via Extern bureaublad Het protocol (RDP).
>
>

1. Zorg ervoor dat de database en de tabellen worden gemaakt op de doel-SQL Server-database. Hier volgt een voorbeeld van hoe u doet dat door de `Create Database` en `Create Table` opdrachten:

```sql
CREATE DATABASE <database_name>

CREATE TABLE <tablename>
(
    <columnname1> <datatype> <constraint>,
    <columnname2> <datatype> <constraint>,
    <columnname3> <datatype> <constraint>
)
```

1. Genereer het indelingsbestand waarin wordt beschreven van het schema voor de tabel door de volgende opdracht vanaf de opdrachtregel van de machine waarop bcp is geïnstalleerd.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. De gegevens invoegen in de database met behulp van de bcp-opdracht als volgt. Dit moet werken vanaf de opdrachtregel ervan uitgaande dat de SQL Server op dezelfde computer is geïnstalleerd:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimaliseren van BCP voegt** Raadpleeg het volgende artikel ['Richtlijnen voor het optimaliseren van bulkimport'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) dergelijke voegt optimaliseren.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Voegt voor snellere gegevensverplaatsing parallel
Als de gegevens die u verplaatst groot is, kunt u dingen van versnellen door tegelijkertijd meerdere BCP opdrachten parallel in een PowerShell-Script wordt uitgevoerd.

> [!NOTE]
> **Opname van Big Data** Voor het optimaliseren van het laden van gegevens voor grote en zeer grote gegevens sets kunt u de logische en fysieke database tabellen partitioneren met meerdere bestands groepen en partitie tabellen. Zie voor meer informatie over het maken en het laden van gegevens naar partitietabellen [Parallel laden in SQL-partitietabellen](parallel-load-sql-partitioned-tables.md).
>
>

In het volgende Power shell-voorbeeld script worden parallelle invoeg bladen gedemonstreerd met behulp van BCP:

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

### <a name="insert-tables-bulkquery"></a>Bulksgewijs invoegen SQL-Query
[Bulksgewijs invoegen van de SQL-Query](https://msdn.microsoft.com/library/ms188365) kan worden gebruikt voor het importeren van gegevens in de database van de rij/kolom op basis van bestanden (de ondersteunde typen worden behandeld in de[bereid gegevens voor bulksgewijs te exporteren of importeren (SQL Server)](https://msdn.microsoft.com/library/ms188609)) onderwerp.

Hier volgen enkele Voorbeeldopdrachten voor Bulk Insert zijn zoals hieronder:  

1. Analyseer uw gegevens en eventuele aangepaste opties instellen voor het importeren om ervoor te zorgen dat de SQL Server-database wordt ervan uitgegaan de dezelfde indeling voor speciale velden, zoals datums dat. Hier volgt een voorbeeld van het instellen van de notatie voor datum als jaar-maand-dag (als uw gegevens de datum in de indeling van de maand-jaar-dagen bevat):

```sql
SET DATEFORMAT ymd;
```
1. Gegevens importeren met bulksgewijs importinstructie toe:

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

### <a name="sql-builtin-utilities"></a>Ingebouwde hulpprogramma's in SQL Server
U kunt SQL Server integraties Services (SSIS) gebruiken om gegevens te importeren in SQL Server-VM op Azure uit een plat bestand.
SSIS is beschikbaar in twee studio-omgevingen. Zie voor meer informatie, [Integration Services (SSIS) en Studio omgevingen](https://technet.microsoft.com/library/ms140028.aspx):

* Zie voor meer informatie over SQL Server Data Tools, [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Zie voor meer informatie over de Wizard importeren/exporteren, [Wizard SQL Server importeren en exporteren](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Gegevens te verplaatsen van on-premises SQL Server naar SQL Server op een Azure-VM
U kunt ook de volgende strategieën:

1. [Een SQL Server-Database implementeren op een Microsoft Azure VM-wizard](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporteren naar platte bestanden](#export-flat-file)
3. [SQL Database-migratiewizard](#sql-migration)
4. [Database back-en herstellen](#sql-backup)

We beschrijven elk van deze hieronder:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Een SQL Server-Database implementeren op een Microsoft Azure VM-wizard
De **implementeren van een SQL Server-Database naar een Microsoft Azure VM-wizard** is een eenvoudige en aanbevolen manier om gegevens te verplaatsen van een on-premises SQL Server-exemplaar naar SQL Server op een Azure-VM. Zie voor gedetailleerde stappen, evenals een beschrijving van de andere alternatieven, [een database migreren naar SQL Server op een Azure-VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exporteren naar platte bestanden
Verschillende manieren kunnen worden gebruikt voor het bulksgewijs gegevens exporteren uit een On-Premises SQL Server, zoals beschreven in de [bulksgewijs importeren en exporteren van gegevens (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) onderwerp. Dit document wordt uitgelegd hoe de bulksgewijs kopiëren programma (BCP) als voorbeeld. Wanneer gegevens worden geëxporteerd naar een plat bestand, kan het kan worden geïmporteerd naar een andere SQL-server met behulp van bulkimport.

1. De gegevens van on-premises SQL Server naar een bestand met het hulpprogramma bcp als volgt exporteren

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. De database en de tabel in SQL Server-VM maken op Azure met de `create database` en `create table` voor het tabelschema geëxporteerd in stap 1.
3. Maak een indelingsbestand voor het beschrijven van het tabelschema van de gegevens worden geëxporteerd/geïmporteerd. Details van het indelingsbestand worden beschreven in [maken van een indelingsbestand (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Bestand-indeling als BCP uitgevoerd van de SQL Server-machine

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Bestand-indeling met het wanneer BCP op afstand uitvoeren in een SQL-Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Gebruik een van de methoden die worden beschreven in de sectie [verplaatsen van gegevens uit de bestandsbron](#filesource_to_sqlonazurevm) de gegevens in platte bestanden verplaatsen naar een SQL-Server.

### <a name="sql-migration"></a>SQL Database-migratiewizard
[SQL Server-Database-migratiewizard](https://sqlazuremw.codeplex.com/) biedt een gebruiksvriendelijke manier om gegevens te verplaatsen tussen twee SQL server-exemplaren. Hierdoor kan de gebruiker kan het schema tussen bronnen en doeltabellen toewijzen, kiest u kolommen van het type en verschillende andere functies. Bulksgewijs kopiëren (BCP) op de achtergrond wordt gebruikt. Hieronder ziet u een schermopname van het welkomstscherm wordt weergegeven voor de migratie van SQL Database-wizard.  

![SQL Server-migratiewizard][2]

### <a name="sql-backup"></a>Database back-en herstellen
Biedt ondersteuning voor SQL Server:

1. [Database back-en herstellen van de functionaliteit](https://msdn.microsoft.com/library/ms187048.aspx) (zowel een lokaal bestand of een bacpac exporteren naar een blob) en [laag gegevenstoepassingen](https://msdn.microsoft.com/library/ee210546.aspx) (met behulp van bacpac).
2. Mogelijkheid om SQL Server-VM's rechtstreeks in Azure maken met een database gekopieerde of kopiëren naar een bestaande SQL Azure-database. Zie voor meer informatie, [gebruikt u de Database-Wizard kopiëren](https://msdn.microsoft.com/library/ms188664.aspx).

Een schermafbeelding van de Database-back-up/herstellen-opties van SQL Server Management Studio wordt hieronder weergegeven.

![Hulpprogramma voor het SQL Server importeren][1]

## <a name="resources"></a>Resources
[Een Database migreren naar SQL Server op een Azure-VM](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Overzicht van SQL Server op virtuele machines in Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
