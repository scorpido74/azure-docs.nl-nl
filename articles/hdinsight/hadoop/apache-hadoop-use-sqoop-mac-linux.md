---
title: Apache Sqoop met Apache Hadoop - Azure HDInsight
description: Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren tussen Apache Hadoop op HDInsight en een Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769384"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop gebruiken voor het importeren en exporteren van gegevens tussen Apache Hadoop in HDInsight en SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van Apache Sqoop voor het importeren en exporteren tussen een Apache Hadoop-cluster in Azure HDInsight en Azure SQL Database of Microsoft SQL Server-database. De stappen in dit `sqoop` document gebruiken de opdracht rechtstreeks vanaf de headnode van het Hadoop-cluster. U gebruikt SSH om verbinding te maken met het hoofdknooppunt en voert de opdrachten in dit document uit. Dit artikel is een voortzetting van [Gebruik Apache Sqoop met Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [de testomgeving instellen](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) van [Apache Sqoop gebruiken met Hadoop in HDInsight.](./hdinsight-use-sqoop.md)

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Vertrouwdheid met Sqoop. Zie [Sqoop-gebruikershandleiding](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)voor meer informatie.

## <a name="set-up"></a>Instellen

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Stel variabelen in voor gebruiksgemak. Vervang `PASSWORD` `MYSQLSERVER`, `MYDATABASE` en met de relevante waarden en voer de onderstaande opdrachten in:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop export

Van Hive naar SQL Server.

1. Als u wilt controleren of Sqoop uw SQL-database kan zien, voert u de onderstaande opdracht in in uw geopende SSH-verbinding. Met deze opdracht wordt een lijst met databases geretourneerd.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Voer de volgende opdracht in om een lijst met tabellen voor de opgegeven database te bekijken:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Als u gegevens `hivesampletable` uit de `mobiledata` tabel Hive wilt exporteren naar de tabel in SQL Database, voert u de onderstaande opdracht in in uw geopende SSH-verbinding:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Als u wilt controleren of gegevens zijn geëxporteerd, gebruikt u de volgende query's van uw SSH-verbinding om de geëxporteerde gegevens weer te geven:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop importeren

Van SQL Server naar Azure-opslag.

1. Voer de onderstaande opdracht in uw geopende `mobiledata` SSH-verbinding in `wasbs:///tutorials/usesqoop/importeddata` om gegevens uit de tabel in SQL Database te importeren in de map op HDInsight. De velden in de gegevens worden gescheiden door een tabteken en de regels worden beëindigd door een nieuwregelteken.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. U ook een Hive-tabel opgeven:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Zodra de import is voltooid, voert u de volgende opdracht in in uw geopende SSH-verbinding om de gegevens in de nieuwe map weer te geven:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Gebruik [beeline](./apache-hadoop-use-hive-beeline.md) om te controleren of de tabel is gemaakt in Hive.

    1. Verbinding maken

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Voer elke query hieronder één voor één uit en bekijk de uitvoer:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Exit beeline `!exit`met .

## <a name="limitations"></a>Beperkingen

* Bulkexport - Met HDInsight op Basis van Linux biedt de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database geen bulkinserts.

* Batching - Met Linux-gebaseerde HDInsight maakt Sqoop bij het gebruik van de `-batch` schakelaar bij het uitvoeren van wisselplaten meerdere wisselplaten in plaats van het batchn van de insert-bewerkingen.

## <a name="important-considerations"></a>Belangrijke overwegingen

* Zowel HDInsight als SQL Server moeten zich op hetzelfde Azure Virtual Network bevinden.

    Zie bijvoorbeeld het [connect HDInsight naar uw on-premises netwerkdocument.](./../connect-on-premises-network.md)

    Zie het [document Extend HDInsight with Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) voor meer informatie over het gebruik van HDInsight met een Azure Virtual Network. Zie het document Overzicht van het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md) voor meer informatie over Azure Virtual Network.

* SQL Server moet zijn geconfigureerd om SQL-verificatie toe te staan. Zie het document [Een verificatiemodus kiezen](https://msdn.microsoft.com/ms144284.aspx) voor meer informatie.

* Mogelijk moet u SQL Server configureren om externe verbindingen te accepteren. Zie Problemen met [verbinding met het SQL Server-databaseenginedocument](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu heb je geleerd hoe je Sqoop moet gebruiken. Voor meer informatie zie:

* [Apache Oozie gebruiken met HDInsight](../hdinsight-use-oozie-linux-mac.md): Gebruik Sqoop-actie in een Oozie-workflow.
* [Vluchtvertragingsgegevens analyseren met HDInsight:](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)Gebruik Interactieve query om vluchtvertragingsgegevens te analyseren en gebruik Vervolgens Sqoop om gegevens naar een Azure SQL-database te exporteren.
* [Gegevens uploaden naar HDInsight:](../hdinsight-upload-data.md)zoek andere methoden voor het uploaden van gegevens naar HDInsight/Azure Blob-opslag.
