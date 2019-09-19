---
title: Apache Spark en Apache Hive integreren met de Hive-Warehouse connector
description: Meer informatie over het integreren van Apache Spark en Apache Hive met de Hive-Warehouse connector op Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 8a946a75a2dbd487494d70d0fd195a5becf5bd5a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122211"
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

1. Maak een HDInsight Spark 4,0-cluster met behulp van de Azure Portal met een opslag account en een aangepast virtueel Azure-netwerk. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)voor meer informatie over het maken van een cluster in een virtueel Azure-netwerk.
1. Maak een HDInsight Interactive query (LLAP) 4,0-cluster met behulp van de Azure Portal met hetzelfde opslag account en een virtueel Azure-netwerk als het Spark-cluster.
1. Kopieer de inhoud van het `/etc/hosts` bestand op headnode0 van uw interactieve query cluster naar het `/etc/hosts` bestand in de headnode0 van uw Spark-cluster. Met deze stap kan uw Spark-cluster IP-adressen van de knoop punten in het interactieve query cluster omzetten. Bekijk de inhoud van het bijgewerkte bestand met `cat /etc/hosts`. De uitvoer moet er ongeveer als volgt uitzien, zoals in de onderstaande scherm afbeelding wordt weer gegeven.

    ![hosten bestand van Hive Warehouse-connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Configureer de instellingen van het Spark-cluster door de volgende stappen uit te voeren: 
    1. Ga naar Azure Portal, selecteer HDInsight-clusters en klik vervolgens op de naam van uw cluster.
    1. Selecteer aan de rechter kant onder **cluster dashboards**de optie **Ambari Home**.
    1. Klik in de Ambari-webgebruikersinterface op **SPARK2** > **configs** > **Custom SPARK2-defaults**.

        ![Configuratie van Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Stel `spark.hadoop.hive.llap.daemon.service.hosts` in op dezelfde waarde als de **component Property. llap. daemon. service. hosts** onder * * Advanced component-Interactive-site * *. Bijvoorbeeld: `@llap0`

    1. Ingesteld `spark.sql.hive.hiveserver2.jdbc.url` op de JDBC-Connection String, die verbinding maakt met Hiveserver2 op het interactieve query cluster. De connection string voor uw cluster ziet er ongeveer als volgt uit. `CLUSTERNAME`is de naam van uw Spark-cluster en `user` de `password` para meters en zijn ingesteld op de juiste waarden voor uw cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        > [!Note]
        > De JDBC-URL moet referenties bevatten om verbinding te maken met Hiveserver2, met inbegrip van een gebruikers naam en wacht woord.

    1. Stel `spark.datasource.hive.warehouse.load.staging.dir` in op een geschikte, met HDFS compatibele tijdelijke directory. Als u twee verschillende clusters hebt, moet de map voor gefaseerde installatie een map zijn in de map met tijdelijke bestanden van het opslag account van het LLAP-cluster, zodat HiveServer2 er toegang toe heeft. Bijvoorbeeld, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` waarbij `STORAGE_ACCOUNT_NAME` de naam is van het opslag account dat wordt gebruikt door het cluster en `STORAGE_CONTAINER_NAME` de naam is van de opslag container.

    1. Stel `spark.datasource.hive.warehouse.metastoreUri` in met de waarde van de meta Store-URI van het interactieve query cluster. Als u de metastoreUri voor uw LLAP-cluster wilt vinden, zoekt u naar de **component. meta Store. uri's** in de Ambari-gebruikers interface van het LLAP-cluster in de **component** > **Advanced** > **General**. De waarde ziet er ongeveer als volgt uit:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Ingesteld `spark.security.credentials.hiveserver2.enabled` op`false` voor de garen-client implementatie modus.
    1. Stel `spark.hadoop.hive.zookeeper.quorum` in op het Zookeeper-quorum van uw LLAP-cluster. Als u het Zookeeper-quorum voor uw LLAP-cluster wilt vinden, zoekt u naar de eigenschap **Hive. Zookeeper. quorum** in de Ambari-gebruikers interface voor uw LLAP- **cluster onder** > Hive**Geavanceerde** > **Geavanceerde component-site**. De waarde ziet er ongeveer als volgt uit:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Volg de stappen in de sectie [verbinding maken en query's uitvoeren](#connecting-and-running-queries)om de configuratie van uw Hive-Warehouse connector te testen.

## <a name="using-the-hive-warehouse-connector"></a>De Hive-Warehouse connector gebruiken

### <a name="connecting-and-running-queries"></a>Query's maken en uitvoeren

U kunt kiezen uit een aantal verschillende methoden om verbinding te maken met uw interactieve query cluster en query's uit te voeren met de Hive-Warehouse connector. Ondersteunde methoden zijn onder andere de volgende hulpprogram ma's:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-verzenden
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Alle voor beelden in dit artikel worden uitgevoerd via Spark-shell.

Voer de volgende stappen uit om een Spark-shell-sessie te starten:

1. SSH in het hoofd knooppunt voor uw cluster. Zie [verbinding maken met HDInsight (Apache Hadoop) met SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het maken van verbinding met uw cluster met SSH.
1. Wijzig in de juiste directory door het `cd /usr/hdp/current/hive_warehouse_connector` volledige pad naar alle JAR-bestanden die als para meters worden gebruikt in de Spark-shell-opdracht te typen of op te geven.
1. Voer de volgende opdracht in om de Spark-shell te starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Er verschijnt een welkomst bericht en een `scala>` prompt waarin u opdrachten kunt invoeren.

1. Nadat u de Spark-shell hebt gestart, kunt u een component Warehouse connector-exemplaar starten met behulp van de volgende opdrachten:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Verbinding maken en query's uitvoeren op Enterprise Security Package (ESP)-clusters

De Enterprise Security Package (ESP) biedt mogelijkheden op ondernemings niveau zoals op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor Apache Hadoop clusters in azure HDInsight. Zie [Enterprise Security package gebruiken in HDInsight](../domain-joined/apache-domain-joined-architecture.md)voor meer informatie over ESP.

1. Volg de eerste stappen 1 en 2 onder [verbinding maken en query's uitvoeren](#connecting-and-running-queries).
1. Typ `kinit` en meld u aan met een domein gebruiker.
1. Start Spark-shell met de volledige lijst met configuratie parameters, zoals hieronder wordt weer gegeven. Alle waarden in hoofd letters tussen punt haken moeten worden opgegeven op basis van het cluster. Zie de sectie over de installatie van de [Hive-Warehouse connector](#hive-warehouse-connector-setup)als u de waarden voor de invoer van de onderstaande para meters wilt weten.:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
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

1. Maak een tabel met `sampletable_colorado` de naam en geef de kolommen op met behulp van de volgende opdracht:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filter de tabel `hivesampletable` waarbij de kolom `state` gelijk is `Colorado`aan. Deze query van de Hive-tabel wordt geretourneerd als een Spark-data frame. Vervolgens wordt de data frame opgeslagen in de Hive- `sampletable_colorado` tabel met `write` behulp van de functie.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

In de onderstaande scherm afbeelding ziet u de resulterende tabel.

![component-Warehouse connector-Hive weer geven](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gestructureerd streamen schrijven

Met hive Warehouse connector kunt u met Spark streaming gegevens schrijven naar Hive-tabellen.

Volg de onderstaande stappen om een component Warehouse connector-voor beeld te maken waarmee gegevens uit een Spark-stroom op localhost-poort 9999 worden opgenomen in een Hive-tabel.

1. Open een Terminal op uw Spark-cluster.
1. Start de Spark-stroom met de volgende opdracht:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Genereer gegevens voor de Spark-stroom die u hebt gemaakt door de volgende stappen uit te voeren:
    1. Open een andere terminal op hetzelfde Spark-cluster.
    1. Typ `nc -lk 9999`bij de opdracht prompt. Met deze opdracht wordt het netcat-hulp programma gebruikt voor het verzenden van gegevens van de opdracht regel naar de opgegeven poort.
    1. Typ de woorden die u wilt opnemen in de Spark-stream, gevolgd door een regel terugloop.

        ![invoer gegevens naar Apache Spark-stroom](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)

1. Maak een nieuwe Hive-tabel om de streaminggegevens in op te slaan. Typ in de Spark-shell de volgende opdrachten:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Schrijf de streaminggegevens naar de zojuist gemaakte tabel met behulp van de volgende opdracht:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > De `metastoreUri` opties `database` en moeten momenteel hand matig worden ingesteld vanwege een bekend probleem in Apache Spark. Zie [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460)voor meer informatie over dit probleem.

1. U kunt de gegevens die in de tabel zijn ingevoegd, weer geven met de volgende opdracht:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Gegevens beveiligen op Spark ESP-clusters

1. Voer de volgende `demo` opdrachten in om een tabel met enkele voorbeeld gegevens te maken:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Bekijk de inhoud van de tabel met de volgende opdracht. Voordat u het beleid toepast, `demo` wordt in de tabel de volledige kolom weer gegeven.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![demo tabel vóór het Toep assen van het beleid voor Zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Pas een kolom maskerings beleid toe dat alleen de laatste vier tekens van de kolom bevat.  
    1. Ga naar de gebruikers interface van Zwerver `https://CLUSTERNAME.azurehdinsight.net/ranger/`op.
    1. Klik onder **Hive**op de component service voor uw cluster.
        ![zwerver Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klik op het tabblad **maskeren** en vervolgens op **Nieuw beleid toevoegen**

        ![Hive-beleids lijst van de component Warehouse connector zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Geef een gewenste beleids naam op. Data base selecteren: **Standaard**, Hive-tabel: **demo**, Hive-kolom: **naam**, gebruiker: **Rsadmin2**, toegangs typen: **selecteren**en **gedeeltelijk masker: weer geven laatste 4** van het menu **optie masker selecteren** . Klik op **Toevoegen**.
                ![beleid maken](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Bekijk de inhoud van de tabel opnieuw. Na het Toep assen van het beleid voor zwerver, kunnen we alleen de laatste vier tekens van de kolom zien.

    ![demo tabel na toepassing van het beleid van zwerver](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Volgende stappen

* [Interactieve query gebruiken met HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Voor beelden van interactie met hive Warehouse connector met Zeppelin, livy, Spark-Submit en pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
