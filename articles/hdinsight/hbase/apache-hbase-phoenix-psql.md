---
title: Bulksgewijs laden in Apache Phoenix met behulp van psql-Azure HDInsight
description: Gebruik het psql-hulp programma voor het laden van gegevens voor bulksgewijs laden in Apache Phoenix tabellen in azure HDInsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: f00f6bcf07cbdc5aeaeb04aeccf7e88cf4822dbf
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311707"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Bulksgewijs gegevens laden in Apache Phoenix met psql

[Apache Phoenix](https://phoenix.apache.org/) is een open-source, enorm parallelle relationele data base gebouwd op [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix biedt SQL-achtige query's via HBase. Phoenix maakt gebruik van JDBC-Stuur Programma's om gebruikers in staat te stellen SQL-tabellen, indexen, weer gaven en reeksen en upsert te maken, verwijderen en wijzigen. Phoenix gebruikt noSQL systeem eigen compilatie in plaats van MapReduce te gebruiken voor het compileren van query's om toepassingen met lage latentie op HBase te maken. Phoenix voegt co-processors toe ter ondersteuning van het uitvoeren van de door de client geleverde code in de adres ruimte van de-server, waarbij de code naast de gegevens wordt uitgevoerd. Hiermee wordt de client/server-gegevens overdracht geminimaliseerd.  Als u wilt werken met gegevens met behulp van Phoenix in HDInsight, maakt u eerst tabellen en laadt u de gegevens hierin.

## <a name="bulk-loading-with-apache-phoenix"></a>Bulksgewijs laden met Apache Phoenix

Er zijn meerdere manieren om gegevens op te halen in HBase, met inbegrip van client-Api's, een MapReduce-taak met TableOutputFormat of het hand matig invoeren van de gegevens via de HBase-shell. Phoenix biedt twee methoden voor het laden van CSV-gegevens in Phoenix-tabellen: een client laadprogramma met de naam `psql`en een MapReduce-hulp programma voor bulksgewijs laden.

Het hulp programma `psql` maakt deel uit van één thread en is het meest geschikt voor het laden van mega bytes of gigabytes aan gegevens. Alle CSV-bestanden die moeten worden geladen, moeten de bestands extensie. csv hebben.  U kunt ook SQL-script bestanden opgeven in de `psql` opdracht regel met de bestands extensie '. SQL '.

Bulksgewijs laden met MapReduce wordt gebruikt voor veel grotere gegevens volumes, doorgaans in productie scenario's, omdat MapReduce meerdere threads gebruikt.

Controleer voordat u begint met het laden van gegevens of Phoenix is ingeschakeld en of de instellingen van de querytime-out naar verwachting zijn.  Open uw HDInsight cluster [Apache Ambari](https://ambari.apache.org/) -dash board, selecteer HBase en klik vervolgens op het tabblad Configuratie.  Schuif omlaag om te controleren of Apache Phoenix is ingesteld op `enabled` zoals wordt weer gegeven:

![Instellingen van HDInsight-cluster Apache Phoenix](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>`psql` gebruiken om tabellen bulksgewijs te laden

1. Maak een nieuwe tabel en sla de query op met de bestands naam `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopieer uw CSV-bestand (voorbeeld inhoud weer gegeven) als `customers.csv` in een `/tmp/`-map voor het laden van de zojuist gemaakte tabel.  Gebruik de `hdfs` opdracht om het CSV-bestand naar de gewenste bron locatie te kopiëren.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Maak een SQL SELECT-query om te controleren of de ingevoerde gegevens correct zijn geladen en sla uw query vervolgens op met de bestands naam `listCustomers.sql`. U kunt elke SQL-query gebruiken.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. De gegevens bulksgewijs laden door een *Nieuw* Hadoop-opdracht venster te openen. Ga eerst naar de locatie van de uitvoerings Directory met de opdracht `cd` en gebruik vervolgens het hulp programma `psql` (python `psql.py` opdracht). 

    In het volgende voor beeld wordt verwacht dat u het `customers.csv`-bestand van een opslag account naar de lokale map Temp hebt gekopieerd met behulp van `hdfs` als in stap 2 hierboven.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Als u de naam van de `ZookeeperQuorum` wilt bepalen, zoekt u de [Apache ZooKeeper](https://zookeeper.apache.org/) quorum teken reeks in het bestand `/etc/hbase/conf/hbase-site.xml` met de eigenschaps naam `hbase.zookeeper.quorum`.

5. Nadat de `psql` bewerking is voltooid, ziet u een bericht in het opdracht Venster:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce gebruiken voor het bulksgewijs laden van tabellen

Gebruik het hulp programma MapReduce Load voor meer doorvoer belasting over het cluster. Met deze loader worden eerst alle gegevens naar HFiles geconverteerd en wordt vervolgens de gemaakte HFiles naar HBase.

1. Start de CSV MapReduce loader met behulp van de opdracht `hadoop` met het jar-client-Opper vlak:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Maak een nieuwe tabel met een SQL-instructie, net als bij `CreateCustomersTable.sql` in de vorige stap 1.

3. Als u het schema van uw tabel wilt controleren, voert u `!describe inputTable`uit.

4. Bepaal het pad naar de locatie van uw invoer gegevens, zoals het voorbeeld bestand `customers.csv`. De invoer bestanden bevinden zich mogelijk in uw WASB/ADLS-opslag account. In dit voorbeeld scenario bevinden de invoer bestanden zich in de map `<storage account parent>/inputFolderBulkLoad`.

5. Ga naar de map voor de uitvoering van de opdracht voor het bulksgewijs laden van MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Zoek de `ZookeeperQuorum` waarde in `/etc/hbase/conf/hbase-site.xml`met de eigenschaps naam `hbase.zookeeper.quorum`.

7. Stel het klassenpad in en voer de opdracht voor het `CsvBulkLoadTool`-hulp programma uit:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Als u MapReduce met Azure Data Lake Storage wilt gebruiken, zoekt u de Data Lake Storage hoofdmap. Dit is de `hbase.rootdir` waarde in `hbase-site.xml`. In de volgende opdracht is de Data Lake Storage Root Directory `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. In deze opdracht geeft u de Data Lake Storage invoer-en uitvoer mappen op als para meters:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Aanbevelingen

* Gebruik hetzelfde opslag medium voor zowel de invoer-als uitvoer mappen, Azure Storage (WASB) of Azure Data Lake Storage (ADL). Als u gegevens wilt overdragen van Azure Storage naar Data Lake Storage, kunt u de `distcp` opdracht gebruiken:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Gebruik werk knooppunten met een grotere grootte. De kaart processen van de MapReduce massa kopie produceren grote hoeveel heden tijdelijke uitvoer waarmee de beschik bare niet-DFS-ruimte wordt gevuld. Gebruik meer en grotere werk knooppunten voor een grote hoeveelheid belasting voor bulksgewijs laden. Het aantal worker-knoop punten dat u toewijst aan uw cluster is rechtstreeks van invloed op de verwerkings snelheid.

* Splits de invoer bestanden in segmenten van ongeveer 10 GB. Bulksgewijs laden is een tijdrovende bewerking, waardoor het splitsen van uw invoer bestanden in meerdere segmenten resulteert in betere prestaties.

* Vermijd de gebieden van de regio server. Als uw gestaag toeneemt, kan HBase sequentiële Schrijf bewerkingen de regio server hotspotting veroorzaken. Door het *zouten* van de rij-sleutel worden opeenvolgende schrijf bewerkingen verminderd. Phoenix biedt een manier om de rij-sleutel transparant te zouten met een zouten byte voor een bepaalde tabel, zoals hieronder wordt verwezen.

## <a name="next-steps"></a>Volgende stappen

* [Bulksgewijs laden van gegevens met Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix gebruiken met Apache HBase-clusters op basis van Linux in HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Gezouteerde tabellen](https://phoenix.apache.org/salted.html)
* [Apache Phoenix grammatica](https://phoenix.apache.org/language/index.html)
