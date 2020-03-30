---
title: Apache Spark & Hive - Hive Warehouse Connector - Azure HDInsight
description: Meer informatie over het integreren van Apache Spark en Apache Hive met de Hive Warehouse Connector op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252411"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integreer Apache Spark en Apache Hive met de Hive Warehouse Connector

De Apache Hive Warehouse Connector (HWC) is een bibliotheek waarmee u gemakkelijker met Apache Spark en Apache Hive werken door taken te ondersteunen, zoals het verplaatsen van gegevens tussen Spark DataFrames en Hive-tabellen, en ook spark-streaminggegevens naar Hive-tabellen te leiden. Hive Warehouse Connector werkt als een brug tussen Spark en Hive. Het ondersteunt Scala, Java en Python voor ontwikkeling.

Met de Hive Warehouse Connector u profiteren van de unieke functies van Hive en Spark om krachtige big-data toepassingen te bouwen. Apache Hive biedt ondersteuning voor databasetransacties die atoom, consistent, geïsoleerd en duurzaam (ACID) zijn. Zie [Hive Transactions](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)voor meer informatie over ACID en transacties in Hive. Hive biedt ook gedetailleerde beveiligingscontroles via Apache Ranger en Low Latency Analytical Processing die niet beschikbaar zijn in Apache Spark.

Apache Spark, heeft een Structured Streaming API die streaming mogelijkheden niet beschikbaar in Apache Hive geeft. Te beginnen met HDInsight 4.0, Apache Spark 2.3.1 en Apache Hive 3.1.0 hebben aparte metastores, wat de interoperabiliteit moeilijk kan maken. De Hive Warehouse Connector maakt het eenvoudiger om Spark en Hive samen te gebruiken. De HWC-bibliotheek laadt gegevens van LLAP-daemons parallel aan Spark-uitvoerders, waardoor deze efficiënter en schaalbaarder wordt dan met behulp van een standaard JDBC-verbinding van Spark naar Hive.

![hive warehouse connector architectuur](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Enkele van de bewerkingen die worden ondersteund door de Hive Warehouse Connector zijn:

* Een tabel beschrijven
* Een tabel maken voor orc-opgemaakte gegevens
* Hive-gegevens selecteren en een DataFrame ophalen
* Een DataFrame naar Hive schrijven in batch
* Een hive-update-instructie uitvoeren
* Tabelgegevens van Hive lezen, transformeren in Spark en schrijven naar een nieuwe Hive-tabel
* Een DataFrame of Spark-stream naar Hive schrijven met HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector-installatie

Volg de volgende stappen om de Hive Warehouse Connector in te stellen tussen een spark- en interactief querycluster in Azure HDInsight:

### <a name="create-clusters"></a>Clusters maken

1. Maak een HDInsight Spark **4.0-cluster** met een opslagaccount en een aangepast Virtueel Azure-netwerk. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk voor](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)informatie over het maken van een cluster in een virtueel Azure-netwerk.

1. Maak een LLAP-cluster (HDInsight Interactive Query) **4.0** met hetzelfde opslagaccount en het virtuele Azure-netwerk als het Spark-cluster.

### <a name="modify-hosts-file"></a>Hosts-bestand wijzigen

Kopieer de knooppuntgegevens `/etc/hosts` uit het bestand op headnode0 van uw cluster Interactieve `/etc/hosts` Query en geef de informatie toe aan het bestand op de headnode0 van uw Spark-cluster. Met deze stap kan uw Spark-cluster IP-adressen van de knooppunten in het cluster Interactieve query oplossen. Bekijk de inhoud van `cat /etc/hosts`het bijgewerkte bestand met . De uiteindelijke output moet er ongeveer uitzien als wat wordt weergegeven in de screenshot hieronder.

![hosts-bestand hive warehouse-connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Verzamel voorlopige informatie

#### <a name="from-your-interactive-query-cluster"></a>Vanuit uw cluster interactieve query's

1. Navigeer naar de Apache Ambari Hive-pagina van het cluster met `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` de `LLAPCLUSTERNAME` naam van uw cluster Interactieve query.

1. Navigeer naar **Advanced** > **General** > **hive.metastore.uris** en noteer de waarde. De waarde kan vergelijkbaar `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`zijn met: .

1. Navigeer naar **Advanced** > **Advanced Advanced hive-site** > **hive.zookeeper.quorum** en noteer de waarde. De waarde kan vergelijkbaar `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`zijn met: .

#### <a name="from-your-apache-spark-cluster"></a>Van uw Apache Spark-cluster

1. Navigeer naar de Apache Ambari Hive-pagina van het cluster met `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` de `SPARKCLUSTERNAME` naam van uw Apache Spark-cluster.

1. Navigeer naar **Advanced** > **Advanced Advanced Hive-interactive-site** > **hive.llap.daemon.service.hosts** en noteer de waarde. De waarde kan vergelijkbaar `@llap0`zijn met: .

### <a name="configure-spark-cluster-settings"></a>Spark-clusterinstellingen configureren

Navigeer vanuit uw Spark Ambari-webgebruikers naar **Spark2** > **CONFIGS** > **Custom spark2-standaardinstellingen.**

![Apache Ambari Spark2-configuratie](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selecteer **Eigenschap toevoegen...** indien nodig om het volgende toe te voegen/bij te werken:

| Sleutel | Waarde |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|De waarde die u eerder hebt verkregen van **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Stel in op de JDBC-verbindingstekenreeks, die verbinding maakt met Hiveserver2 in het cluster Interactieve query. VERVANG `LLAPCLUSTERNAME` de naam van uw cluster Interactieve query. Vervang `PWD` door het werkelijke wachtwoord.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Ingesteld op een geschikte HDFS-compatibele staging directory. Als u twee verschillende clusters hebt, moet de map met tijdelijke bestanden een map zijn in de map met tijdelijke bestanden van het opslagaccount van het LLAP-cluster, zodat HiveServer2 er toegang toe heeft.  Vervang `STORAGE_ACCOUNT_NAME` de naam van het opslagaccount dat `STORAGE_CONTAINER_NAME` door het cluster wordt gebruikt en door de naam van de opslagcontainer.|
|`spark.datasource.hive.warehouse.metastoreUri`|De waarde die u eerder hebt verkregen van **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`voor de implementatiemodus van yarn-client.|
|`spark.hadoop.hive.zookeeper.quorum`|De waarde die u eerder hebt verkregen van **hive.zookeeper.quorum**.|

Sla wijzigingen op en start onderdelen indien nodig opnieuw op.

## <a name="using-the-hive-warehouse-connector"></a>De Hive Warehouse Connector gebruiken

### <a name="connecting-and-running-queries"></a>Query's verbinden en uitvoeren

U kiezen tussen een aantal verschillende methoden om verbinding te maken met uw cluster interactieve query's en query's uit te voeren met behulp van de Hive Warehouse Connector. Ondersteunde methoden omvatten de volgende hulpmiddelen:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark (PySpark)
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alle voorbeelden in dit artikel worden uitgevoerd via spark-shell.

Ga als volgt te werk om een spark-shell-sessie te starten:

1. SSH in de headnode voor uw Apache Spark cluster. Zie Verbinding maken met [HDInsight (Apache Hadoop) met SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over verbinding maken met uw cluster.

1. Voer de volgende opdracht in om de vonkshell te starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    U ziet een welkomstbericht `scala>` en een prompt waar u opdrachten invoeren.

1. Nadat u de spark-shell hebt gestart, kan een hive Warehouse Connector-instantie worden gestart met de volgende opdrachten:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Esp-clusters (Enterprise Security Package) verbinden en uitvoeren

Het Enterprise Security Package (ESP) biedt enterprise-grade mogelijkheden zoals Active Directory-gebaseerde authenticatie, multi-user ondersteuning en role-based access control voor Apache Hadoop clusters in Azure HDInsight. Zie [Enterprise Security Package gebruiken in HDInsight](../domain-joined/apache-domain-joined-architecture.md)voor meer informatie over ESP.

1. SSH in de headnode voor uw Apache Spark cluster. Zie Verbinding maken met [HDInsight (Apache Hadoop) met SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over verbinding maken met uw cluster.

1. Typ `kinit` en log in bij een domeingebruiker.

1. Start spark-shell met de volledige lijst met configuratieparameters zoals hieronder weergegeven. Alle waarden in alle hoofdletters tussen hoekhaakjes moeten worden opgegeven op basis van uw cluster. Zie de sectie over de installatie van [Hive Warehouse Connector](#hive-warehouse-connector-setup)als u de waarden wilt achterhalen die u wilt invoeren voor een van de onderstaande parameters:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Spark DataFrames maken op hive-query's

De resultaten van alle query's met de HWC-bibliotheek worden geretourneerd als een DataFrame. In de volgende voorbeelden wordt uitgelegd hoe u een basisquery maakt.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

De resultaten van de query zijn Spark DataFrames, die kunnen worden gebruikt met Spark-bibliotheken zoals MLIB en SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark DataFrames uitschrijven naar Hive-tabellen

Spark ondersteunt het schrijven naar hive's managed ACID-tabellen niet native. Met Behulp van HWC u echter elk DataFrame naar een Hive-tabel schrijven. U deze functionaliteit op het werk zien in het volgende voorbeeld:

1. Maak een `sampletable_colorado` tabel aangeroepen en geef de kolommen op met de volgende opdracht:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filter de `hivesampletable` tabel `state` waar `Colorado`de kolom gelijk is . Deze query van de Hive-tabel wordt geretourneerd als een Spark DataFrame. Vervolgens wordt het DataFrame opgeslagen `sampletable_colorado` in `write` de tabel Hive met behulp van de functie.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Bekijk de resultaten met de volgende opdracht:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![hive warehouse connector show hive tabel](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gestructureerde streaming schrijft

Met Hive Warehouse Connector u Spark-streaming gebruiken om gegevens in Hive-tabellen te schrijven.

Volg de onderstaande stappen om een Hive Warehouse Connector-voorbeeld te maken waarmee gegevens uit een Spark-stream op localhost-poort 9999 worden opgenomen in een Hive-tabel.

1. Volg de stappen onder [Query's verbinden en uitvoeren](#connecting-and-running-queries).

1. Begin de spark stream met de volgende opdracht:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Genereer gegevens voor de Spark-stream die u hebt gemaakt door de volgende stappen uit te voeren:
    1. Open een tweede SSH-sessie op hetzelfde Spark-cluster.
    1. Typ bij de `nc -lk 9999`opdrachtprompt . Deze opdracht maakt gebruik van het netcat-hulpprogramma om gegevens van de opdrachtregel naar de opgegeven poort te verzenden.

1. Ga terug naar de eerste SSH-sessie en maak een nieuwe Hive-tabel om de streaminggegevens vast te houden. Voer bij de spark-shell de volgende opdracht in:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Schrijf de streaminggegevens vervolgens naar de nieuw gemaakte tabel met de volgende opdracht:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > De `metastoreUri` `database` opties en opties moeten momenteel handmatig worden ingesteld vanwege een bekend probleem in Apache Spark. Zie [SPARK-25460 voor](https://issues.apache.org/jira/browse/SPARK-25460)meer informatie over dit probleem.

1. Ga terug naar de tweede SSH-sessie en voer de volgende waarden in:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Keer terug naar de eerste SSH-sessie en noteer de korte activiteit. Gebruik de volgende opdracht om de gegevens weer te geven:

    ```scala
    hive.table("stream_table").show()
    ```

Gebruik **Ctrl + C** om netcat te stoppen op de tweede SSH-sessie. Gebruik `:q` om spark-shell af te sluiten op de eerste SSH-sessie.

### <a name="securing-data-on-spark-esp-clusters"></a>Gegevens beveiligen op Spark ESP-clusters

1. Maak een `demo` tabel met enkele voorbeeldgegevens door de volgende opdrachten in te voeren:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Bekijk de inhoud van de tabel met de volgende opdracht. Voordat u het beleid `demo` toepast, wordt in de tabel de volledige kolom weergegeven.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demotabel voordat u het rangerbeleid toepast](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Pas een kolommaskerbeleid toe dat alleen de laatste vier tekens van de kolom weergeeft.  
    1. Ga naar de Ranger `https://CLUSTERNAME.azurehdinsight.net/ranger/`Admin UI op .
    1. Klik op de Hive-service voor uw cluster onder **Hive**.
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klik op het tabblad **Maskeren en** voeg vervolgens **Nieuw beleid toe**

        ![hive warehouse connector ranger hive policy list](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Geef een gewenste beleidsnaam op. Selecteer database: **Standaard**, Hive tabel: **demo**, Hive kolom: **naam**, Gebruiker: **rsadmin2**, Access Types: **select**, and **Partial mask: show last 4** from the Select **Masking Option.** Klik op**toevoegen**.
                ![beleid maken](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Bekijk de inhoud van de tabel opnieuw. Na het toepassen van het rangerbeleid kunnen we alleen de laatste vier tekens van de kolom zien.

    ![demotabel na toepassing rangerbeleid](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Volgende stappen

* [Interactieve query gebruiken met HDInsight](./apache-interactive-query-get-started.md)
* [Voorbeelden van interactie met Hive Warehouse Connector met Zeppelin, Livy, spark-submit en pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
