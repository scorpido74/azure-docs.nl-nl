---
title: Gegevens kopiëren tussen Data Lake Storage Gen1 en Azure SQL - Sqoop | Microsoft Documenten
description: Sqoop gebruiken om gegevens te kopiëren tussen Azure SQL Database en Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839065"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Gegevens kopiëren tussen Data Lake Storage Gen1 en Azure SQL Database met Sqoop

Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren van gegevens tussen Azure SQL Database en Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Wat is Sqoop?

Big data-toepassingen zijn een natuurlijke keuze voor het verwerken van ongestructureerde en semi-gestructureerde gegevens, zoals logboeken en bestanden. Het kan echter ook nodig zijn om gestructureerde gegevens te verwerken die zijn opgeslagen in relationele databases.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) is een tool die is ontworpen om gegevens over te dragen tussen relationele databases en een big data repository, zoals Data Lake Storage Gen1. U deze gebruiken om gegevens uit een relationeel databasebeheersysteem (RDBMS) zoals Azure SQL Database te importeren in Data Lake Storage Gen1. U de gegevens vervolgens transformeren en analyseren met big data-workloads en de gegevens vervolgens terugexporteren naar een RDBMS. In dit artikel gebruikt u een Azure SQL-database als relationele database om uit te importeren/exporteren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen1-account**. Zie Aan de slag met [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van het account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen1-account. Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). In dit artikel wordt ervan uitgegaan dat u een HDInsight Linux-cluster hebt met Data Lake Storage Gen1-toegang.
* **Azure SQL-database**. Zie Een Azure SQL-database maken voor instructies over het maken [van een Azure SQL-database](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Voorbeeldtabellen maken in de Azure SQL-database

1. Maak twee voorbeeldtabellen in de Azure SQL-database om te beginnen. Gebruik [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) of Visual Studio om verbinding te maken met de database en voer vervolgens de volgende query's uit.

    **Tabel maken1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Tabel maken2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Voer de volgende opdracht uit om enkele voorbeeldgegevens toe te voegen aan **Tabel1**. Laat **tabel 2** leeg. Later importeert u gegevens uit **tabel1** in Data Lake Storage Gen1. Vervolgens exporteert u gegevens van Data Lake Storage Gen1 naar **Tabel2.**

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Sqoop gebruiken vanuit een HDInsight-cluster met toegang tot Data Lake Storage Gen1

Een HDInsight cluster heeft al de Sqoop pakketten beschikbaar. Als u het HDInsight-cluster hebt geconfigureerd om Data Lake Storage Gen1 als extra opslag te gebruiken, u Sqoop (zonder configuratiewijzigingen) gebruiken om gegevens te importeren/exporteren tussen een relationele database zoals Azure SQL Database en een Data Lake Storage Gen1-account.

1. Voor dit artikel gaan we ervan uit dat u een Linux-cluster hebt gemaakt, dus u moet SSH gebruiken om verbinding te maken met het cluster. Zie [Verbinding maken met een HDInsight-cluster op Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Controleer of u vanuit het cluster toegang hebt tot het Data Lake Storage Gen1-account. Voer de volgende opdracht uit vanaf de SSH-prompt:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Deze opdracht bevat een lijst met bestanden/mappen in het Data Lake Storage Gen1-account.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Gegevens uit Azure SQL-database importeren in Data Lake Storage Gen1

1. Navigeer naar de map waar Sqoop-pakketten beschikbaar zijn. Typisch, deze locatie `/usr/hdp/<version>/sqoop/bin`is .

1. Importeer de gegevens uit **tabel1** in het Data Lake Storage Gen1-account. Gebruik de volgende syntaxis:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   De tijdelijke aanduiding **sql-database-server-naam** vertegenwoordigt de naam van de server waarop de Azure SQL-database wordt uitgevoerd. de tijdelijke aanduiding **sql-database-naam** vertegenwoordigt de werkelijke databasenaam.

   Bijvoorbeeld:

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Controleer of de gegevens zijn overgedragen naar het Data Lake Storage Gen1-account. Voer de volgende opdracht uit:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   De volgende uitvoer wordt weergegeven.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Elk **deel-m-*** bestand komt overeen met een rij in de brontabel, **Tabel1**. U de inhoud van de deel-m-*-bestanden bekijken om te verifiëren.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Gegevens uit Data Lake Storage Gen1 exporteren naar Azure SQL-database

1. Exporteer de gegevens van het Data Lake Storage Gen1-account naar de lege **tabel, Tabel2,** in de Azure SQL-database. Gebruik de volgende syntaxis.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Bijvoorbeeld:

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Controleer of de gegevens zijn geüpload naar de SQL Database-tabel. Gebruik [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) of Visual Studio om verbinding te maken met de Azure SQL Database en voer vervolgens de volgende query uit.

       SELECT * FROM TABLE2

   Deze opdracht moet de volgende uitvoer hebben.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Prestatieoverwegingen tijdens het gebruik van Sqoop

Zie het [blogbericht Sqoop performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)voor het afstemmen van uw Sqoop-taak om gegevens naar Data Lake Storage Gen1 te kopiëren.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens uit Azure Storage Blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
