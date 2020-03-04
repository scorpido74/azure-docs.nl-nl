---
title: Apache Spark & component-Hive Warehouse connector-Azure HDInsight
description: Meer informatie over het integreren van Apache Spark en Apache Hive met de Hive-Warehouse connector op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252411"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Apache Spark en Apache Hive integreren met de Hive-Warehouse connector

De Apache Hive Warehouse connector (HWC) is een bibliotheek waarmee u gemakkelijker kunt werken met Apache Spark en Apache Hive door ondersteunende taken, zoals het verplaatsen van gegevens tussen Spark DataFrames en Hive-tabellen, en het omleiden van Spark-streaminggegevens in Hive-tabellen. Hive Warehouse connector werkt als een brug tussen Spark en Hive. Scala, Java en python worden ondersteund voor ontwikkeling.

Met de component Warehouse connector kunt u profiteren van de unieke functies van Hive en Spark om krachtige toepassingen voor Big data te bouwen. Apache Hive biedt ondersteuning voor database transacties die atomisch, consistent, geïsoleerd en duurzaam zijn. Zie [Hive-trans acties](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)voor meer informatie over zuren en trans acties in Hive. Hive biedt ook gedetailleerde beveiligings controles via Apache zwerver en analytische verwerking met lage latentie die niet beschikbaar is in Apache Spark.

Apache Spark heeft een Structured streaming API die streaming-mogelijkheden biedt die niet beschikbaar zijn in Apache Hive. Vanaf HDInsight 4,0 kunnen Apache Spark 2.3.1 en Apache Hive 3.1.0 afzonderlijke meta Stores bevatten, waardoor interoperabiliteit lastig kan zijn. De Hive-Warehouse connector maakt het gemakkelijker om Spark en Hive samen te gebruiken. De HWC-bibliotheek laadt gegevens van LLAP-daemons parallel uitvoerender, en maakt deze efficiënter en schaalbaar dan het gebruik van een standaard JDBC-verbinding van Spark naar Hive.

![architectuur van de Hive-Warehouse connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Enkele van de bewerkingen die worden ondersteund door de Hive Warehouse connector zijn:

* Een tabel beschrijven
* Een tabel maken voor gegevens in de ORC-indeling
* Hive-gegevens selecteren en een Data Frame ophalen
* Een data frame naar een Hive schrijven in batch
* Een update-instructie van een Hive uitvoeren
* Tabel gegevens lezen uit Hive, deze transformeren in Spark en schrijven naar een nieuwe Hive-tabel
* Een data frame-of Spark-stroom naar een Hive schrijven met behulp van HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Setup van Hive Warehouse-connector

Volg deze stappen om de Hive-Warehouse connector in te stellen tussen een Spark en een interactief query cluster in azure HDInsight:

### <a name="create-clusters"></a>Clusters maken

1. Een HDInsight Spark **4,0** -cluster maken met een opslag account en een aangepast virtueel Azure-netwerk. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)voor meer informatie over het maken van een cluster in een virtueel Azure-netwerk.

1. Maak een HDInsight Interactive query (LLAP) **4,0** -cluster met hetzelfde opslag account en een virtueel Azure-netwerk als het Spark-cluster.

### <a name="modify-hosts-file"></a>Hosts-bestand wijzigen

Kopieer de knooppunt gegevens uit het `/etc/hosts`-bestand op headnode0 van uw interactieve query cluster en voeg de informatie toe aan het `/etc/hosts` bestand op de headnode0 van uw Spark-cluster. Met deze stap kan uw Spark-cluster IP-adressen van de knoop punten in het interactieve query cluster omzetten. Bekijk de inhoud van het bijgewerkte bestand met `cat /etc/hosts`. De uiteindelijke uitvoer moet er ongeveer als volgt uitzien, zoals in de onderstaande scherm afbeelding wordt weer gegeven.

![hosten bestand van Hive Warehouse-connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Voorlopige gegevens verzamelen

#### <a name="from-your-interactive-query-cluster"></a>Vanuit uw interactieve query cluster

1. Navigeer naar de Ambari-Hive-pagina van het cluster met `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` waarbij `LLAPCLUSTERNAME` de naam is van uw interactieve query cluster.

1. Navigeer naar **Advanced** > **General** > **Hive. meta Store. uri's** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Ga naar **geavanceerde** > **Geavanceerde component-site** > **Hive. Zookeeper. quorum** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

#### <a name="from-your-apache-spark-cluster"></a>Vanuit uw Apache Spark-cluster

1. Navigeer naar de Ambari-Hive-pagina van het cluster met `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` waarbij `SPARKCLUSTERNAME` de naam is van uw Apache Spark cluster.

1. Navigeer naar **geavanceerde** > **Geavanceerde component-interactive-site** > **Hive. llap. daemon. service. host** en noteer de waarde. De waarde kan er ongeveer als volgt uitzien: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Spark-cluster instellingen configureren

Navigeer vanuit uw Spark Ambari-webgebruikersinterface naar **Spark2** > **configs** > **aangepaste Spark2-defaults**.

![Configuratie van Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selecteer **eigenschap toevoegen...** , indien nodig, om het volgende toe te voegen of bij te werken:

| Sleutel | Waarde |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|De waarde die u eerder hebt verkregen vanuit **Hive. llap. daemon. service. hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Ingesteld op de JDBC-connection string, die verbinding maakt met Hiveserver2 op het interactieve query cluster. Vervang `LLAPCLUSTERNAME` door de naam van uw interactieve query cluster. Vervang `PWD` door het wacht woord.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Stel in op een geschikte, met HDFS compatibele tijdelijke directory. Als u twee verschillende clusters hebt, moet de map voor gefaseerde installatie een map zijn in de map met tijdelijke bestanden van het opslag account van het LLAP-cluster, zodat HiveServer2 er toegang toe heeft.  Vervang `STORAGE_ACCOUNT_NAME` door de naam van het opslag account dat wordt gebruikt door het cluster en `STORAGE_CONTAINER_NAME` door de naam van de opslag container.|
|`spark.datasource.hive.warehouse.metastoreUri`|De waarde die u eerder hebt verkregen uit **Hive. meta Store. uri's**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` voor de implementatie modus voor GARENs van de client.|
|`spark.hadoop.hive.zookeeper.quorum`|De waarde die u eerder hebt verkregen van **Hive. Zookeeper. quorum**.|

Sla de wijzigingen op en start de onderdelen indien nodig opnieuw op.

## <a name="using-the-hive-warehouse-connector"></a>De Hive-Warehouse connector gebruiken

### <a name="connecting-and-running-queries"></a>Query's maken en uitvoeren

U kunt kiezen uit een aantal verschillende methoden om verbinding te maken met uw interactieve query cluster en query's uit te voeren met de Hive-Warehouse connector. Ondersteunde methoden zijn onder andere de volgende hulpprogram ma's:

* [Spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-verzenden
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alle voor beelden in dit artikel worden uitgevoerd via Spark-shell.

Voer de volgende stappen uit om een Spark-shell-sessie te starten:

1. SSH in het hoofd knooppunt voor uw Apache Spark-cluster. Zie [verbinding maken met HDInsight (Apache Hadoop) met SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het maken van verbinding met uw cluster met SSH.

1. Voer de volgende opdracht in om de Spark-shell te starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Er wordt een welkomst bericht weer gegeven en een `scala>` prompt waarin u opdrachten kunt invoeren.

1. Nadat u de Spark-shell hebt gestart, kunt u een component Warehouse connector-exemplaar starten met behulp van de volgende opdrachten:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Verbinding maken en query's uitvoeren op Enterprise Security Package (ESP)-clusters

De Enterprise Security Package (ESP) biedt mogelijkheden op ondernemings niveau zoals op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor Apache Hadoop clusters in azure HDInsight. Zie [Enterprise Security package gebruiken in HDInsight](../domain-joined/apache-domain-joined-architecture.md)voor meer informatie over ESP.

1. SSH in het hoofd knooppunt voor uw Apache Spark-cluster. Zie [verbinding maken met HDInsight (Apache Hadoop) met SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het maken van verbinding met uw cluster met SSH.

1. Typ `kinit` en meld u aan met een domein gebruiker.

1. Start Spark-shell met de volledige lijst met configuratie parameters, zoals hieronder wordt weer gegeven. Alle waarden in hoofd letters tussen punt haken moeten worden opgegeven op basis van het cluster. Zie de sectie over de installatie van de [Hive-Warehouse connector](#hive-warehouse-connector-setup)als u de waarden voor de invoer van de onderstaande para meters wilt weten.:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Spark DataFrames maken op basis van Hive-query's

De resultaten van alle query's die gebruikmaken van de HWC-bibliotheek worden geretourneerd als een data frame. De volgende voor beelden laten zien hoe u een eenvoudige query maakt.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

De resultaten van de query zijn Spark DataFrames, die kunnen worden gebruikt met Spark-bibliotheken als MLIB en SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark-DataFrames naar Hive-tabellen schrijven

Spark ondersteunt geen systeem eigen ondersteuning voor het schrijven naar de tabellen van de beheerde zuren van de component. Met HWC kunt u echter elke data frame naar een Hive-tabel schrijven. U kunt deze functionaliteit op het volgende voor beeld bekijken:

1. Maak een tabel met de naam `sampletable_colorado` en geef de kolommen op met behulp van de volgende opdracht:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filter de tabel `hivesampletable` waarbij de kolom `state` gelijk is aan `Colorado`. Deze query van de Hive-tabel wordt geretourneerd als een Spark-data frame. Vervolgens wordt de data frame opgeslagen in de Hive-tabel `sampletable_colorado` met behulp van de functie `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Bekijk de resultaten met de volgende opdracht:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![component-Warehouse connector-Hive weer geven](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gestructureerd streamen schrijven

Met hive Warehouse connector kunt u met Spark streaming gegevens schrijven naar Hive-tabellen.

Volg de onderstaande stappen om een component Warehouse connector-voor beeld te maken waarmee gegevens uit een Spark-stroom op localhost-poort 9999 worden opgenomen in een Hive-tabel.

1. Volg de stappen onder [verbinding maken en query's uitvoeren](#connecting-and-running-queries).

1. Start de Spark-stroom met de volgende opdracht:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Genereer gegevens voor de Spark-stroom die u hebt gemaakt door de volgende stappen uit te voeren:
    1. Open een tweede SSH-sessie op hetzelfde Spark-cluster.
    1. Typ `nc -lk 9999`bij de opdracht prompt. Met deze opdracht wordt het netcat-hulp programma gebruikt voor het verzenden van gegevens van de opdracht regel naar de opgegeven poort.

1. Ga terug naar de eerste SSH-sessie en maak een nieuwe Hive-tabel om de streaminggegevens te bewaren. Voer in de Spark-shell de volgende opdracht in:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Schrijf vervolgens de streaminggegevens naar de zojuist gemaakte tabel met behulp van de volgende opdracht:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > De opties `metastoreUri` en `database` moeten momenteel hand matig worden ingesteld vanwege een bekend probleem in Apache Spark. Zie [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460)voor meer informatie over dit probleem.

1. Ga terug naar de tweede SSH-sessie en voer de volgende waarden in:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Ga terug naar de eerste SSH-sessie en noteer de korte activiteit. Gebruik de volgende opdracht om de gegevens weer te geven:

    ```scala
    hive.table("stream_table").show()
    ```

Gebruik **CTRL + C** om netcat te stoppen bij de tweede SSH-sessie. Gebruik `:q` om Spark-shell te sluiten voor de eerste SSH-sessie.

### <a name="securing-data-on-spark-esp-clusters"></a>Gegevens beveiligen op Spark ESP-clusters

1. Maak een tabel `demo` met een aantal voorbeeld gegevens door de volgende opdrachten in te voeren:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Bekijk de inhoud van de tabel met de volgende opdracht. Voordat u het beleid toepast, wordt in de tabel `demo` de volledige kolom weer gegeven.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demo tabel vóór het Toep assen van het beleid voor Zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Pas een kolom maskerings beleid toe dat alleen de laatste vier tekens van de kolom bevat.  
    1. Ga naar de gebruikers interface van zwerver op `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klik onder **Hive**op de component service voor uw cluster.
        ![zwerver Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klik op het tabblad **maskeren** en vervolgens op **Nieuw beleid toevoegen**

        ![Hive-beleids lijst van de component Warehouse connector zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Geef een gewenste beleids naam op. Data base selecteren: **standaard**, Hive-tabel: **demo**, Hive-kolom: **naam**, gebruiker: **rsadmin2**, toegangs typen: **selecteren**, en **gedeeltelijk masker: weer geven laatste 4** van het menu **optie masker selecteren** . Klik op **Toevoegen**.
                ![beleid maken](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Bekijk de inhoud van de tabel opnieuw. Na het Toep assen van het beleid voor zwerver, kunnen we alleen de laatste vier tekens van de kolom zien.

    ![demo tabel na toepassing van het beleid van zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Volgende stappen

* [Interactieve query gebruiken met HDInsight](./apache-interactive-query-get-started.md)
* [Voor beelden van interactie met hive Warehouse connector met Zeppelin, livy, Spark-Submit en pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
