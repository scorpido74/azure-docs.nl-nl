---
title: Bulkladen in Apache Phoenix met psql - Azure HDInsight
description: Gebruik het psql-hulpprogramma om bulkbelastingsgegevens te laden in Apache Phoenix-tabellen in Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552607"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Bulksgewijs gegevens laden in Apache Phoenix met psql

[Apache Phoenix](https://phoenix.apache.org/) is een open source, massaal parallelle relationele database gebouwd op [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix biedt SQL-achtige query's via HBase. Phoenix gebruikt JDBC-stuurprogramma's om gebruikers in staat te stellen SQL-tabellen, indexen, weergaven en sequenties en upserte rijen afzonderlijk en in bulk te maken, te verwijderen en te wijzigen. Phoenix gebruikt noSQL native compilatie in plaats van MapReduce te gebruiken om query's samen te stellen, om toepassingen met lage latentie bovenop HBase te maken. Phoenix voegt coprocessors toe ter ondersteuning van het uitvoeren van door de client geleverde code in de adresruimte van de server, waarbij de code wordt uitgevoerd die bij de gegevens is gekoppeld. Dit minimaliseert de overdracht van gegevens over client/server.  Als u met gegevens wilt werken met Phoenix in HDInsight, maakt u eerst tabellen en laadt u gegevens ervervolgens in.

## <a name="bulk-loading-with-apache-phoenix"></a>Bulkladen met Apache Phoenix

Er zijn meerdere manieren om gegevens in HBase te krijgen, waaronder het gebruik van client-API's, een mapReduce-taak met TableOutputFormat of het handmatig invoeren van de gegevens met behulp van de HBase-shell. Phoenix biedt twee methoden voor het laden van CSV-gegevens in Phoenix-tabellen: een clientlaadtool met de naam `psql`en een op MapReduce gebaseerdbulklaadgereedschap.

De `psql` tool is single-threaded en is het meest geschikt voor het laden van megabytes of gigabytes aan gegevens. Alle CSV-bestanden die moeten worden geladen, moeten de bestandsextensie '.csv' hebben.  U sql-scriptbestanden `psql` ook opgeven in de opdrachtregel met de bestandsextensie '.sql'.

Bulkladen met MapReduce wordt gebruikt voor veel grotere gegevensvolumes, meestal in productiescenario's, omdat MapReduce meerdere threads gebruikt.

Controleer voordat u gegevens gaat laden of Phoenix is ingeschakeld en of de time-outinstellingen van de query zijn zoals verwacht.  Toegang tot uw HDInsight-cluster [Apache Ambari-dashboard,](https://ambari.apache.org/) selecteer HBase en vervolgens het tabblad Configuratie.  Scroll naar beneden om te `enabled` controleren of Apache Phoenix is ingesteld op zoals weergegeven:

![Clusterinstellingen apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Tabellen `psql` gebruiken om bulkbelasting te laden

1. Maak een `createCustomersTable.sql`bestand met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand op en sluit het.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Maak een `listCustomers.sql`bestand met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand op en sluit het.

    ```sql
    SELECT * from Customers;
    ```

1. Maak een `customers.csv`bestand met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand op en sluit het.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Maak een `customers2.csv`bestand met de naam en kopieer de onderstaande code naar het bestand. Sla het bestand op en sluit het.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Open een opdrachtprompt en wijzig de map in de locatie van de nieuw gemaakte bestanden. Vervang CLUSTERNAME hieronder door de werkelijke naam van uw HBase-cluster. Voer vervolgens de code uit om de bestanden te uploaden naar de headnode van uw cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Wijzig van uw ssh-sessie de map naar de locatie van het **psql-hulpprogramma.** Voer de opdracht hieronder uit:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Bulk laadt de gegevens. De onderstaande code maakt zowel de tabel **Klanten** als uploadt de gegevens.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Nadat `psql` de bewerking is voltooid, ziet u een soortgelijk bericht als volgt:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. U de `psql` inhoud van de tabel Klanten blijven bekijken. Voer de onderstaande code uit:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    U ook [HBase-shell](./query-hbase-with-hbase-shell.md)of [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) gebruiken om de gegevens op te vragen.

1. Upload extra gegevens. Nu de tabel al bestaat, geeft de opdracht de tabel op. Voer de opdracht hieronder uit:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Map gebruikenReduce to bulk load tables

Gebruik het gereedschap Laden met een hogere doorvoer die over het cluster wordt verdeeld, voor het laden met een hogere doorvoer die over het cluster wordt verdeeld. Deze lader converteert eerst alle gegevens in HFiles en levert vervolgens de gemaakte HFiles naar HBase.

1. Deze sectie gaat verder met de ssh-sessie en objecten die eerder zijn gemaakt. Maak de tabel **Klanten** en het bestand **customers.csv** indien nodig met behulp van de bovenstaande stappen. Indien nodig, herstel uw ssh-verbinding.

1. De inhoud van de tabel **Klanten** afkappen. Voer de onderstaande opdrachten uit tijdens uw open ssh-sessie:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Kopieer `customers.csv` het bestand van uw headnode naar Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Wijzig in de uitvoeringsmap voor de opdracht Bulkbelasting van mapreduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Start de CSV MapReduce `hadoop` loader met behulp van de opdracht met de Phoenix client jar:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Zodra het uploaden is voltooid, ziet u een bericht dat vergelijkbaar is als volgt:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Als u MapReduce wilt gebruiken met Azure Data Lake Storage, `hbase.rootdir` zoekt `hbase-site.xml`u de hoofdmap Gegevensmeeropslag, de waarde in . In de volgende opdracht is `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`de hoofdmap Gegevensmeeropslag . Geef in deze opdracht de invoer- en uitvoermappen van Data Lake Storage op als parameters:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Als u de gegevens wilt opvragen en bekijken, u **psql** gebruiken zoals eerder beschreven. U ook [HBase shell,](./query-hbase-with-hbase-shell.md)of [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Aanbevelingen

* Gebruik hetzelfde opslagmedium voor zowel invoer- als uitvoermappen, azure storage (WASB) of Azure Data Lake Storage (ADL). Als u gegevens wilt overbrengen van Azure Storage `distcp` naar Data Lake Storage, u de opdracht gebruiken:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Gebruik werknemersknooppunten op grotere grootte. De kaartprocessen van het bulkexemplaar MapReduce produceren grote hoeveelheden tijdelijke uitvoer die de beschikbare niet-DFS-ruimte vullen. Gebruik voor een grote hoeveelheid bulkladen meer en grotere werknemersknooppunten. Het aantal werknemersknooppunten dat u aan uw cluster toewijst, is rechtstreeks van invloed op de verwerkingssnelheid.

* Splits invoerbestanden in segmenten van ~ 10 GB. Bulkladen is een opslagintensieve bewerking, dus het splitsen van uw invoerbestanden in meerdere segmenten resulteert in betere prestaties.

* Vermijd hotspots voor regioserver. Als uw rijsleutel monotonisch toeneemt, kunnen hbase sequential writes leiden tot regioserver hotspotting. *Het zouten van* de rijtoets vermindert sequentiële schrijfbewerkingen. Phoenix biedt een manier om transparant zout de rijsleutel met een zouten byte voor een bepaalde tabel, zoals hieronder verwezen.

## <a name="next-steps"></a>Volgende stappen

* [Bulkgegevens laden met Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix gebruiken met Apache HBase-clusters op basis van Linux in HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Gezouten tafels](https://phoenix.apache.org/salted.html)
* [Apache Phoenix Grammatica](https://phoenix.apache.org/language/index.html)
