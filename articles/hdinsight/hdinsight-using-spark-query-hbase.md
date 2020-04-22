---
title: Spark gebruiken om HBase-gegevens te lezen en te schrijven - Azure HDInsight
description: Gebruik de Spark HBase-connector om gegevens van een Spark-cluster te lezen en te schrijven naar een HBase-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4f2e8b2a691a6b17b5ed075745d556db4e330535
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682468"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven

Apache HBase wordt meestal opgevraagd met zijn API op laag niveau (scans, krijgt en zet) of met een SQL-syntaxis met Apache Phoenix. Apache levert ook de Apache Spark HBase Connector. De connector is een handig en performant alternatief voor het opvragen en wijzigen van gegevens die door HBase zijn opgeslagen.

## <a name="prerequisites"></a>Vereisten

* Twee afzonderlijke HDInsight-clusters die in hetzelfde [virtuele netwerk zijn](./hdinsight-plan-virtual-network-deployment.md)geïmplementeerd. Een HBase, en een Spark met ten minste Spark 2.1 (HDInsight 3.6) geïnstalleerd. Zie [Clusters op basis van Linux maken in HDInsight met behulp van de Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie.

* Het URI-schema voor de primaire opslag van uw clusters. Dit schema wordt wasb:// voor `abfs://` Azure Blob Storage, voor Azure Data Lake Storage Gen2 of adl:// voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Blob `wasbs://`Storage, is de URI .  Zie ook, [veilige overdracht](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Algemeen proces

Het proces op hoog niveau om uw Spark-cluster in staat te stellen uw HDInsight-cluster op te vragen, is als volgt:

1. Bereid enkele voorbeeldgegevens voor in HBase.
2. Verkrijg het hbase-site.xml-bestand uit de map HBase-clusterconfiguratie (/etc/hbase/conf).
3. Plaats een kopie van hbase-site.xml in uw Spark 2-configuratiemap (/etc/spark2/conf).
4. Voer `spark-shell` de Spark HBase-connector uit met de `packages` Maven-coördinaten in de optie.
5. Definieer een catalogus die het schema van Spark naar HBase in kaart brengt.
6. Interactie met de HBase-gegevens met behulp van de RDD- of DataFrame-API's.

## <a name="prepare-sample-data-in-apache-hbase"></a>Voorbeeldgegevens voorbereiden in Apache HBase

In deze stap maakt en vult u een tabel in Apache HBase die u vervolgens opvragen met Spark.

1. Gebruik `ssh` de opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande `HBASECLUSTER` opdracht door de naam van uw HBase-cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Gebruik `hbase shell` de opdracht om de interactieve shell Van HBase te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

3. Gebruik `create` de opdracht om een HBase-tabel met families met twee kolommen te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik `put` de opdracht om waarden in een bepaalde kolom in een bepaalde rij in een bepaalde tabel in te voegen. Voer de volgende opdracht in:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Gebruik `exit` de opdracht om de interactieve hbase-shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Hbase-site.xml kopiëren naar sparkcluster

Kopieer de hbase-site.xml van lokale opslag naar de hoofdmap van de standaardopslag van uw Spark-cluster.  Bewerk de onderstaande opdracht om uw configuratie weer te geven.  Voer vervolgens van uw geopende SSH-sessie naar het HBase-cluster de opdracht in:

| Syntaxiswaarde | Nieuwe waarde|
|---|---|
|[URI-regeling](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Wijzigen om uw opslag weer te geven.  De syntaxis hieronder is voor blobopslag met veilige overdracht ingeschakeld.|
|`SPARK_STORAGE_CONTAINER`|Vervang de standaardnaam van de opslagcontainer die wordt gebruikt voor het Spark-cluster.|
|`SPARK_STORAGE_ACCOUNT`|Vervang de standaardnaam van het opslagaccount die wordt gebruikt voor het Spark-cluster.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Sluit vervolgens uw ssh-verbinding af met uw HBase-cluster.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Hbase-site.xml op uw Spark-cluster plaatsen

1. Maak verbinding met het hoofdknooppunt van uw Spark-cluster met SSH. Bewerk de onderstaande `SPARKCLUSTER` opdracht door de naam van uw Spark-cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Voer de onderstaande `hbase-site.xml` opdracht in om vanuit de standaardopslag van het Spark-cluster te kopiëren naar de spark 2-configuratiemap op de lokale opslag van het cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark Shell uitvoeren die verwijst naar de Spark HBase-connector

1. Voer de onderstaande opdracht in van uw geopende SSH-sessie tot het Spark-cluster om een sparkshell te starten:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Houd deze Spark Shell-instantie open en ga door naar de volgende stap.

## <a name="define-a-catalog-and-query"></a>Een catalogus en query definiëren

In deze stap definieert u een catalogusobject dat het schema van Apache Spark naar Apache HBase in kaart brengt.  

1. Voer in uw geopende `import` Spark Shell de volgende instructies in:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Voer de onderstaande opdracht in om een catalogus te definiëren voor de tabel Contactpersonen die u in HBase hebt gemaakt:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    De code doet de volgende handelingen:  

     a. Definieer een catalogusschema voor de `Contacts`HBase-tabel met de naam .  
     b. Identificeer de rijsleutel als `key`en breng de kolomnamen die in Spark worden gebruikt in kaart aan de kolomfamilie, kolomnaam en kolomtype zoals die in HBase worden gebruikt.  
     c. De rijsleutel moet ook in detail worden gedefinieerd`rowkey`als een benoemde `cf` kolom `rowkey`( ), die een specifieke kolomfamilie van heeft .  

1. Voer de onderstaande opdracht in om een `Contacts` methode te definiëren die een DataFrame rond uw tabel in HBase biedt:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Een instantie van het DataFrame maken:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Query the DataFrame:

    ```scala
    df.show()
    ```

    U ziet twee rijen met gegevens:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registreer een tijdelijke tabel zodat u de HBase-tabel opvragen met Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Geef een SQL-query uit aan de `contacts` tabel:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    U ziet resultaten als deze:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Nieuwe gegevens invoegen

1. Als u een nieuwe record `ContactRecord` contactpersoon wilt invoegen, definieert u een klasse:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Een instantie `ContactRecord` maken van en deze in een array plaatsen:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Sla de array met nieuwe gegevens op in HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Bekijk de resultaten:

    ```scala  
    df.show()
    ```

    De uitvoer ziet er als volgt uit:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Sluit de vonkshell door de volgende opdracht in te voeren:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark Hbase-connector](https://github.com/hortonworks-spark/shc)
