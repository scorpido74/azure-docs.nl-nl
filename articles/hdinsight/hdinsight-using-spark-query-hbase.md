---
title: Spark gebruiken om HBase-gegevens te lezen en te schrijven-Azure HDInsight
description: Gebruik de Spark HBase-connector om gegevens te lezen en te schrijven van een Spark-cluster naar een HBase-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623093"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven

Apache HBase wordt doorgaans opgevraagd met de API op laag niveau (scans, haalt en plaatst) of met een SQL-syntaxis met Apache Phoenix. Apache biedt ook de Apache Spark HBase-connector, een handig en alternatief voor het uitvoeren van query's en het wijzigen van gegevens die zijn opgeslagen door HBase.

## <a name="prerequisites"></a>Vereisten

* Twee afzonderlijke HDInsight-clusters die zijn geïmplementeerd in hetzelfde [virtuele netwerk](./hdinsight-plan-virtual-network-deployment.md). Eén HBase en een Spark met ten minste Spark 2,1 (HDInsight 3,6) geïnstalleerd. Zie op [Linux gebaseerde clusters in HDInsight maken met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Het [URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit schema is wasb://voor Azure Blob Storage, abfs://voor Azure Data Lake Storage Gen2 of adl://voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Blob Storage, wordt de URI `wasbs://`.  Zie ook [beveiligde overdracht](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Algemeen proces

Het proces op hoog niveau voor het inschakelen van uw Spark-cluster voor het uitvoeren van een query op uw HDInsight-cluster is als volgt:

1. Bereid enkele voorbeeld gegevens voor in HBase.
2. Haal het hbase-site. XML-bestand op in de map HBase cluster Configuration (/etc/hbase/conf).
3. Plaats een kopie van hbase-site. XML in de configuratiemap van Spark 2 (/etc/spark2/conf).
4. Voer `spark-shell` een verwijzing naar de Spark HBase-connector uit met de Maven-coördinaten in de `packages` optie.
5. Definieer een catalogus die het schema toewijst van Spark in HBase.
6. Interactie met de HBase-gegevens met behulp van de RDD-of data frame-Api's.

## <a name="prepare-sample-data-in-apache-hbase"></a>Voorbeeld gegevens voorbereiden in Apache HBase

In deze stap maakt en vult u een tabel in Apache HBase op die u vervolgens kunt uitvoeren met behulp van Spark.

1. Gebruik de opdracht `ssh` om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `HBASECLUSTER` te vervangen door de naam van uw HBase-cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Gebruik de opdracht `hbase shell` om de HBase Interactive shell te starten. Voer de volgende opdracht in voor uw SSH-verbinding:

    ```bash
    hbase shell
    ```

3. Gebruik de `create` opdracht om een HBase-tabel met twee kolom families te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik de opdracht `put` om in een opgegeven rij in een bepaalde tabel waarden in te voegen voor een opgegeven kolom. Voer de volgende opdracht in:

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

5. Gebruik de `exit` opdracht om de HBase-interactieve shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Hbase-site. XML kopiëren naar Spark-cluster

Kopieer de hbase-site. XML van de lokale opslag naar de hoofdmap van de standaard opslag van uw Spark-cluster.  Bewerk de onderstaande opdracht om uw configuratie weer te geven.  Voer vervolgens vanuit uw open SSH-sessie naar het HBase-cluster de volgende opdracht in:

| Syntaxis waarde | Nieuwe waarde|
|---|---|
|[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Wijzig om uw opslag ruimte weer te geven.  De onderstaande syntaxis is voor Blob Storage waarvoor beveiligde overdracht is ingeschakeld.|
|`SPARK_STORAGE_CONTAINER`|Vervang door de naam van de standaard opslag container die wordt gebruikt voor het Spark-cluster.|
|`SPARK_STORAGE_ACCOUNT`|Vervang door de standaard naam van het opslag account dat wordt gebruikt voor het Spark-cluster.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Sluit vervolgens uw SSH-verbinding met uw HBase-cluster af.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Hbase-site. XML op uw Spark-cluster plaatsen

1. Maak met SSH verbinding met het hoofd knooppunt van uw Spark-cluster. Bewerk de onderstaande opdracht door `SPARKCLUSTER` te vervangen door de naam van uw Spark-cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Voer de onderstaande opdracht in om `hbase-site.xml` van de standaard opslag van uw Spark-cluster te kopiëren naar de map Spark 2-configuratie op de lokale opslag van het cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Spark-shell uitvoeren die verwijst naar de Spark HBase-connector

1. Voer vanuit uw open SSH-sessie naar het Spark-cluster de volgende opdracht in om een Spark-shell te starten:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Bewaar dit Spark shell-exemplaar open en ga door naar de volgende stap.

## <a name="define-a-catalog-and-query"></a>Een catalogus en query definiëren

In deze stap definieert u een catalogus object dat het schema toewijst van Apache Spark aan Apache HBase.  

1. Voer in uw open Spark-shell de volgende `import`-instructies in:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Voer de onderstaande opdracht in om een catalogus te definiëren voor de tabel contact personen die u hebt gemaakt in HBase:

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

    De code doet het volgende:  

     a. Definieer een catalogus schema voor de HBase-tabel met de naam `Contacts`.  
     b. Identificeer de rowkey als `key`en wijs de kolom namen die worden gebruikt in Spark toe aan het kolom familie, de kolom naam en het kolom Type zoals gebruikt in HBase.  
     c. De rowkey moet ook gedetailleerd worden gedefinieerd als een benoemde kolom (`rowkey`), die een specifieke kolom familie heeft `cf` van `rowkey`.  

1. Voer de onderstaande opdracht in om een methode te definiëren die een data frame rond uw `Contacts` tabel in HBase biedt:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Maak een instantie van de data frame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Query uitvoeren op de data frame:

    ```scala
    df.show()
    ```

    U ziet nu twee rijen met gegevens:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registreer een tijdelijke tabel zodat u een query kunt uitvoeren op de HBase-tabel met behulp van Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Geef een SQL-query op voor de tabel `contacts`:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    De resultaten moeten er als volgt uitzien:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Nieuwe gegevens invoegen

1. Als u een nieuwe contactpersoonrecord wilt invoegen, definieert u een `ContactRecord` klasse:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Maak een instantie van `ContactRecord` en plaats deze in een matrix:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Sla de matrix van nieuwe gegevens op naar HBase:

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

1. Sluit de Spark-shell door de volgende opdracht in te voeren:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark HBase-connector](https://github.com/hortonworks-spark/shc)
