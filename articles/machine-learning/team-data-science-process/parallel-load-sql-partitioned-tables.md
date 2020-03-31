---
title: Parallel bulkgegevens importeren in SQL-partitietabellen - Team Data Science Process
description: Partitietabellen bouwen voor snelle parallelle bulkimporteren van gegevens naar een SQL Server-database.
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
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721333"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Tabellen bouwen en optimaliseren voor snelle parallelle invoer van gegevens in een SQL Server op een Azure VM

In dit artikel wordt beschreven hoe u partitietabellen maken voor het snel parallel importeren van gegevens naar een SQL Server-database. Voor het laden/overzetten van big data naar een SQL-database kan het importeren van gegevens naar de SQL DB en volgende query's worden verbeterd met behulp van *partitietabellen en weergaven.* 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Een nieuwe database en een set bestandsgroepen maken
* [Maak een nieuwe database,](https://technet.microsoft.com/library/ms176061.aspx)als deze nog niet bestaat.
* Voeg databasebestandsgroepen toe aan de database, die de partitiefysieke bestanden bevat. 
* Dit kan met [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) als er nieuw is of [ALS](https://msdn.microsoft.com/library/bb522682.aspx) de database al bestaat.
* Voeg een of meer bestanden (indien nodig) toe aan elke databasebestandsgroep.
  
  > [!NOTE]
  > Geef de doelbestandsgroep op, die gegevens voor deze partitie bevat en de fysieke databasebestandsnaam(s) waar de bestandsgroepgegevens worden opgeslagen.
  > 
  > 

In het volgende voorbeeld wordt een nieuwe database gemaakt met drie andere bestandsgroepen dan de primaire en logboekgroepen, met één fysiek bestand in elk bestand. De databasebestanden worden gemaakt in de standaard map SQL Server-gegevens, zoals geconfigureerd in de SQL Server-instantie. Zie [Bestandslocaties voor standaardinstellingen en benoemde exemplaren van SQL Server voor](https://msdn.microsoft.com/library/ms143547.aspx)meer informatie over de standaardbestandslocaties.

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Een partitietabel maken
Als u een partitietabel(en) wilt maken volgens het gegevensschema dat is toegewezen aan de databasebestandsgroepen die in de vorige stap zijn gemaakt, moet u eerst een partitiefunctie en -schema maken. Wanneer gegevens bulk worden geïmporteerd naar de partitietabel(s), worden records verdeeld over de bestandsgroepen volgens een partitieschema, zoals hieronder beschreven.

### <a name="1-create-a-partition-function"></a>1. Een partitiefunctie maken
[Een partitiefunctie maken](https://msdn.microsoft.com/library/ms187802.aspx) Met deze functie wordt het bereik gedefinieerd van waarden/grenzen die in elke afzonderlijke partitietabel\_moeten worden opgenomen, bijvoorbeeld om partities per maand (een datumtijdveld)\_in het jaar 2013 te beperken:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Een partitieschema maken
[Een partitieschema maken](https://msdn.microsoft.com/library/ms179854.aspx). Met dit schema wordt elk partitiebereik in de partitiefunctie toegewezen aan een fysieke bestandsgroep, bijvoorbeeld:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Voer de volgende query uit om de bereiken die in elke partitie van kracht zijn volgens de functie/regeling te verifiëren:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Een partitietabel maken
[Maak een partitietabel](https://msdn.microsoft.com/library/ms174979.aspx)(s) op basis van uw gegevensschema en geef het partitieschema en het beperkingsveld op dat wordt gebruikt om de tabel te partitioneren, bijvoorbeeld:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Zie [Partitietabellen en -indexen maken](https://msdn.microsoft.com/library/ms188730.aspx)voor meer informatie .

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Bulk importeert de gegevens voor elke afzonderlijke partitietabel

* U BCP, BULK INSERT of andere methoden gebruiken, zoals [de wizard SQL Server Migration](https://sqlazuremw.codeplex.com/). In het gegeven voorbeeld wordt de BCP-methode gebruikt.
* [Wijzig de database](https://msdn.microsoft.com/library/bb522682.aspx) om het registratieschema voor transacties te wijzigen in BULK_LOGGED om de overhead van logboekregistratie te minimaliseren, bijvoorbeeld:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Als u het laden van gegevens wilt versnellen, start u de bulkimportbewerkingen parallel. Zie 1 TB laden [in minder dan 1 uur](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)voor tips over het versnellen van het importeren van big data in SQL Server-databases.

Het volgende PowerShell-script is een voorbeeld van parallelle gegevensladen met BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Indexen maken om joins en queryprestaties te optimaliseren
* Als u gegevens voor modellering uit meerdere tabellen haalt, maakt u indexen op de joinsleutels om de joinprestaties te verbeteren.
* [Maak indexen](https://technet.microsoft.com/library/ms188783.aspx) (geclusterd of niet-geclusterd) die dezelfde bestandsgroep targeten voor elke partitie, bijvoorbeeld:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Of
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > U ervoor kiezen om de indexen te maken voordat u de gegevens in bulk importeert. Indexcreatie voordat bulk importeren vertraagt het laden van gegevens.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Voorbeeld van geavanceerd analyseproces en -technologie in actie
Zie [Team Data Science Process in Action: SQL Server gebruiken](sql-walkthrough.md)voor een end-to-end walkthrough-voorbeeld met behulp van het Team Data Science Process met een openbare gegevensset.

