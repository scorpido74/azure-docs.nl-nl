---
title: OutOfMemoryError-uitzonderingen voor Apache Spark in Azure HDInsight
description: Verschillende OutOfMemoryError-uitzonderingen voor apache spark-cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271965"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError-uitzonderingen voor Apache Spark in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenario: OutOfMemoryError uitzondering voor Apache Spark

### <a name="issue"></a>Probleem

Uw Apache Spark-toepassing is mislukt met een Niet-Geheugenfout zonder behandeling. Mogelijk ontvangt u een foutbericht dat vergelijkbaar is met:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Oorzaak

De meest waarschijnlijke oorzaak van deze uitzondering is dat niet genoeg heap geheugen wordt toegewezen aan de Java virtuele machines (JVMs). Deze JVM's worden gelanceerd als uitvoerders of stuurprogramma's als onderdeel van de Apache Spark-toepassing.

### <a name="resolution"></a>Oplossing

1. Bepaal de maximale grootte van de gegevens die de Spark-toepassing moet verwerken. Maak een schatting van de grootte op basis van het maximum van de grootte van invoergegevens, de tussenliggende gegevens die worden geproduceerd door het transformeren van de invoergegevens en de geproduceerde uitvoergegevens die de tussenliggende gegevens verder transformeren. Als de initiële schatting niet voldoende is, verhoogt u de grootte iets en wijzig deze totdat de geheugenfouten verdwijnen.

1. Zorg ervoor dat het HDInsight-cluster dat u wilt gebruiken, over voldoende resources (geheugen en kernen) beschikt voor uitvoering van de Spark-toepassing. Dit kan worden bepaald door de sectie Clusterstatistieken van de YARN-gebruikersinterface van het cluster te bekijken voor de waarden van **geheugen gebruikt** vs. **Memory Total** en **VCores Gebruikt** vs. **VCores Totaal**.

    ![garen kern geheugen weergave](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Stel de volgende Spark-configuraties in op de juiste waarden. Breng de toepassingsvereisten in evenwicht met de beschikbare resources in het cluster. Deze waarden mogen niet meer bedragen dan 90% van het beschikbare geheugen en de beschikbare kernen zoals die door YARN worden bekeken, en moeten ook voldoen aan de minimale geheugenvereisten van de Spark-toepassing:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Totaal geheugen dat door alle uitvoerders wordt gebruikt =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Totaal geheugen dat door bestuurder wordt gebruikt =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenario: Java heap space error when trying to open Apache Spark history server Scenario: Java heap space error when trying to open Apache Spark history server Scenario: Java heap space error when trying to open Apache Spark history server Scenario:

### <a name="issue"></a>Probleem

U ontvangt de volgende fout bij het openen van gebeurtenissen in de Spark History-server:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt vaak veroorzaakt door een gebrek aan middelen bij het openen van grote spark-event-bestanden. De grootte van de Spark-heap is standaard ingesteld op 1 GB, maar grote Spark-gebeurtenisbestanden vereisen mogelijk meer dan dit.

Als u de grootte wilt verifiëren van de bestanden die u probeert te laden, u de volgende opdrachten uitvoeren:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Oplossing

U het spark-geschiedenisservergeheugen verhogen `SPARK_DAEMON_MEMORY` door de eigenschap in de Spark-configuratie te bewerken en alle services opnieuw te starten.

U dit doen vanuit de Gebruikersinterface van de Ambari-browser door de sectie Spark2/Config/Advanced spark2-env te selecteren.

![Geavanceerde spark2-env-sectie](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Voeg de volgende eigenschap toe om het Spark History `SPARK_DAEMON_MEMORY=4g`Server-geheugen te wijzigen van 1g naar 4g: .

![Spark, eigenschap](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Zorg ervoor dat u alle betrokken services van Ambari opnieuw start.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenario: Livy Server start niet op Apache Spark-cluster

### <a name="issue"></a>Probleem

Livy Server kan niet worden gestart op een Apache Spark [(Spark 2.1 op Linux (HDI 3.6)]. Proberen om resultaten opnieuw op te starten in de volgende foutstack, uit de Livy logs:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Oorzaak

`java.lang.OutOfMemoryError: unable to create new native thread`hoogtepunten OS kunnen niet meer native threads toewijzen aan JVMs. Bevestigd dat deze uitzondering wordt veroorzaakt door de schending van de limiet voor het aantal threadperprocessen.

Wanneer Livy Server onverwacht sneuvelt, worden ook alle verbindingen met Spark-clusters beëindigd, wat betekent dat alle taken en gerelateerde gegevens verloren gaan. In HDP 2.6 sessie recovery mechanisme werd geïntroduceerd, Livy slaat de sessie details in Zookeeper worden hersteld nadat de Livy Server is terug.

Wanneer een groot aantal taken wordt ingediend via Livy, slaat als onderdeel van High Availability for Livy Server deze sessiestatussen op in ZK (op HDInsight-clusters) en herstelt deze sessies wanneer de Livy-service opnieuw wordt gestart. Bij het opnieuw opstarten na onverwachte beëindiging, Livy creëert een thread per sessie en dit accumuleert een bepaald aantal to-be-recovered sessies waardoor te veel threads worden gemaakt.

### <a name="resolution"></a>Oplossing

Verwijder alle items met behulp van onderstaande stappen.

1. Het IP-adres van de dierenverzorgerknooppunten opvragen met

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Boven opdracht vermeld alle zookeepers voor mijn cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Krijg alle IP-adres van de zookeeper knooppunten met behulp van ping Of u ook verbinding maken met zookeeper van headnode met behulp van zk naam

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Zodra u bent verbonden met zookeeper voer de volgende opdracht uit om alle sessies die worden geprobeerd opnieuw op te starten.

    1. De meeste gevallen kan dit een lijst van meer dan 8000 sessies ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Volgende opdracht is het verwijderen van alle te herstellen sessies. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Wacht tot de bovenstaande opdracht is voltooid en de cursor om de prompt terug te sturen en vervolgens de Livy-service van Ambari opnieuw te starten, wat moet lukken.

> [!NOTE]
> `DELETE`de livy sessie zodra het is voltooid zijn uitvoering. De Livy batch sessies worden niet automatisch verwijderd zodra de spark app is voltooid, wat door het ontwerp is. Een Livy-sessie is een entiteit die is gemaakt door een POST-aanvraag tegen de Livy Rest-server. Er `DELETE` is een aanroep nodig om die entiteit te verwijderen. Of we moeten wachten tot de GC begint.

---

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* [Overzicht van spark-geheugenbeheer](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugging Spark-toepassing op HDInsight-clusters](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
