---
title: Hoogst beschikbare Spark Streaming-taken in YARN - Azure HDInsight
description: Apache Spark-streaming instellen voor een scenario met hoge beschikbaarheid in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807150"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Maak apache spark-streamingtaken met hoge beschikbaarheid met YARN

[Apache Vonk](https://spark.apache.org/) Streaming stelt u in staat om schaalbare, hoge doorvoer- en fouttolerante toepassingen te implementeren voor gegevensverwerking. U Spark Streaming-toepassingen op een HDInsight Spark-cluster verbinden met verschillende soorten gegevensbronnen, zoals Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ,](http://zeromq.org/)raw TCP-sockets of door het [Apache Hadoop HDFS-bestandssysteem](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) te controleren op wijzigingen. Spark Streaming ondersteunt fouttolerantie met de garantie dat een bepaalde gebeurtenis precies één keer wordt verwerkt, zelfs met een knooppuntfout.

Spark Streaming creëert langlopende taken waarbij u transformaties op de gegevens toepassen en vervolgens de resultaten naar bestandssystemen, databases, dashboards en de console pushen. Spark Streaming verwerkt microbatches van gegevens, door eerst een batch gebeurtenissen te verzamelen via een bepaald tijdsinterval. Vervolgens wordt die batch verzonden voor verwerking en uitvoer. Batchtijdintervallen worden meestal gedefinieerd in fracties van een seconde.

![Spark-streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming vertegenwoordigt een continue stroom van gegevens met behulp van een *gedescretiseerde stream* (DStream). Deze DStream kan worden gemaakt op basis van invoerbronnen zoals Event Hubs of Kafka, of door transformaties toe te passen op een andere DStream. Wanneer een evenement bij uw Spark Streaming-toepassing aankomt, wordt het evenement op een betrouwbare manier opgeslagen. Dat wil zeggen dat de gebeurtenisgegevens worden gerepliceerd zodat meerdere knooppunten er een kopie van hebben. Dit zorgt ervoor dat het mislukken van een enkel knooppunt niet zal leiden tot het verlies van uw evenement.

De Spark-kern maakt gebruik van *resilient distributed datasets* (RDD's). RDD's distribueren gegevens over meerdere knooppunten in het cluster, waarbij elk knooppunt zijn gegevens over het algemeen volledig in het geheugen behoudt voor de beste prestaties. Elke RDD vertegenwoordigt gebeurtenissen die zijn verzameld via een batch-interval. Wanneer het batch-interval verstrijkt, produceert Spark Streaming een nieuwe RDD met alle gegevens in dat interval. Deze continue set RDD's wordt verzameld in een DStream. Een Spark Streaming-toepassing verwerkt de gegevens die zijn opgeslagen in de RDD van elke batch.

![Spark DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Gestructureerde streamingtaken stimuleren

Spark Structured Streaming werd geïntroduceerd in Spark 2.0 als een analytische engine voor gebruik op streaming gestructureerde gegevens. Spark Structured Streaming maakt gebruik van de SparkSQL batching engine API's. Net als bij Spark Streaming voert Spark Structured Streaming zijn berekeningen uit over continu binnenkomende microbatches van gegevens. Spark Structured Streaming vertegenwoordigt een stroom van gegevens als een invoertabel met onbeperkte rijen. Dat wil zeggen, de Input Table blijft groeien als er nieuwe gegevens binnenkomen. Deze invoertabel wordt continu verwerkt door een langlopende query en de resultaten worden uitgeschreven naar een uitvoertabel.

![Gestructureerde streaming stimuleren](./media/apache-spark-streaming-high-availability/structured-streaming.png)

In Structured Streaming komen gegevens bij het systeem en worden ze onmiddellijk opgenomen in de invoertabel. U schrijft query's die bewerkingen uitvoeren tegen deze invoertabel. De queryuitvoer levert een andere tabel op, de resultatentabel. De resultatentabel bevat resultaten van uw query, waaruit u gegevens ophaalt om naar een externe gegevenswinkel te verzenden, een dergelijke relationele database. Het *triggerinterval* stelt de timing in voor wanneer gegevens worden verwerkt vanuit de invoertabel. Structured Streaming verwerkt de gegevens standaard zodra deze binnenkomen. U de trigger echter ook zo configureren dat deze op een langere interval wordt uitgevoerd, zodat de streaminggegevens worden verwerkt in op tijd gebaseerde batches. De gegevens in de resultatentabel kunnen worden vernieuwd telkens wanneer er nieuwe gegevens zijn, zodat alle uitvoergegevens sinds het begin van de streamingquery zijn opgenomen *(volledige modus),* of alleen de gegevens bevatten die nieuw zijn sinds de laatste keer dat de query is verwerkt *(toebehorenmodus).*

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Fouttolerante Spark-streamingtaken maken

Als u een zeer beschikbare omgeving wilt maken voor uw Spark-streamingtaken, moet u eerst uw afzonderlijke taken coderen voor herstel in geval van uitval. Dergelijke zelfherstellende banen zijn fouttolerant.

RDD's hebben verschillende eigenschappen die hoog beschikbare en fouttolerante Spark-streamingtaken ondersteunen:

* Batches van invoergegevens die als DStream in RDD's zijn opgeslagen, worden automatisch gerepliceerd in het geheugen voor fouttolerantie.
* Gegevens die verloren zijn gegaan als gevolg van het falen van de werknemer, kunnen opnieuw worden berekend op basis van gerepliceerde invoergegevens over verschillende werknemers, zolang deze werknemersknooppunten beschikbaar zijn.
* Snel foutherstel kan binnen een seconde plaatsvinden, omdat herstel van fouten/achterblijvers plaatsvindt via berekening in het geheugen.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Precies één keer semantiek met Spark Streaming

Als u een toepassing wilt maken die elke gebeurtenis één keer (en slechts één keer) verwerkt, moet u overwegen hoe alle systeempunten opnieuw worden opgestart nadat een probleem is opgetreden en hoe u gegevensverlies voorkomen. Precies één keer vereist semantiek dat er op geen enkel moment gegevens verloren gaan en dat de verwerking van berichten opnieuw kan worden opgestart, ongeacht waar de fout optreedt. Zie [Spark-streamingtaken maken met precies één gebeurtenisverwerking](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming en Apache Hadoop GAREN

In HDInsight wordt clusterwerk gecoördineerd door *Yet Another Resource Negotiator* (YARN). Het ontwerpen van hoge beschikbaarheid voor Spark Streaming omvat technieken voor Spark Streaming, en ook voor YARN-componenten.  Een voorbeeldconfiguratie met YARN wordt hieronder weergegeven.

![GARENarchitectuur](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

In de volgende secties worden ontwerpoverwegingen voor deze configuratie beschreven.

### <a name="plan-for-failures"></a>Plannen voor fouten

Als u een YARN-configuratie wilt maken voor hoge beschikbaarheid, moet u een mogelijke executor of stuurprogrammafout plannen. Sommige Spark Streaming-taken bevatten ook vereisten voor gegevensgarantie die extra configuratie en installatie nodig hebben. Een streamingtoepassing kan bijvoorbeeld een zakelijke vereiste hebben voor een garantie voor nulgegevensverlies, ondanks elke fout die optreedt in het hostingstreamingsysteem of het HDInsight-cluster.

Als een **executeur** uitvalt, worden de taken en ontvangers automatisch opnieuw gestart door Spark, dus er is geen configuratiewijziging nodig.

Als een **stuurprogramma** echter mislukt, mislukken alle bijbehorende uitvoerders en gaan alle ontvangen blokken en berekeningsresultaten verloren. Als u wilt herstellen van een stuurprogrammafout, gebruikt u *DStream-controlepunten* zoals beschreven in [Spark-streamingtaken maken met precies één gebeurtenisverwerking](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream-controlepunten slaat periodiek de *gerichte acyclische grafiek* (DAG) van DStreams op naar fouttolerante opslag, zoals Azure Storage.  Met Checkpointing kan Spark Structured Streaming het mislukte stuurprogramma opnieuw starten vanaf de controlepuntgegevens.  Deze driver herstart lanceert nieuwe uitvoerders en herstart ook ontvangers.

Ga als u stuurprogramma's herstellen met DStream-controlepunten:

* Configureer automatisch opnieuw opstarten van `yarn.resourcemanager.am.max-attempts`het stuurprogramma op YARN met de configuratie-instelling .
* Stel een controlelijst in een HDFS-compatibel bestandssysteem in met `streamingContext.checkpoint(hdfsDirectory)`.
* Herstructureren broncode om checkpoints te gebruiken voor herstel, bijvoorbeeld:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Configureer verloren gegevensherstel door het voorschrijven `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`van logboek (WAL) in te `StorageLevel.MEMORY_AND_DISK_SER`schakelen met en replicatie in het geheugen uit te schakelen voor invoerdstreams met .

Om samen te vatten, met behulp van checkpointing + WAL + betrouwbare ontvangers, zult u in staat zijn om te leveren "ten minste eenmaal" data recovery:

* Precies een keer, zolang ontvangen gegevens niet verloren gaan en de uitgangen zijn ofwel idempotent of transactioneel.
* Precies een keer, met de nieuwe Kafka Direct-aanpak, die Kafka gebruikt als een gerepliceerd logboek, in plaats van ontvangers of WALs te gebruiken.

### <a name="typical-concerns-for-high-availability"></a>Typische zorgen voor hoge beschikbaarheid

* Het is moeilijker om streamingtaken te controleren dan batchtaken. Spark Streaming-taken zijn meestal langlopend en YARN verzamelt logboeken pas als een taak is voltooid.  Spark-controlepunten gaan verloren tijdens de app- of Spark-upgrades en je moet de controlelijst wissen tijdens een upgrade.

* Configureer de YARN-clustermodus om stuurprogramma's uit te voeren, zelfs als een client uitvalt. Ga als volgt te werk om automatisch opnieuw opstarten voor bestuurders in te stellen:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark en de Spark Streaming UI hebben een configureerbaar metrics-systeem. U ook extra bibliotheken gebruiken, zoals Graphite/Grafana om dashboardstatistieken te downloaden, zoals 'getalrecords verwerkt', 'geheugen/GC-gebruik op stuurprogramma's & uitvoerders', 'totale vertraging', 'gebruik van het cluster', enzovoort. In Gestructureerde streaming versie 2.1 of `StreamingQueryListener` hoger u extra statistieken verzamelen.

* U moet langlopende taken segmenteren.  Wanneer een Spark Streaming-toepassing wordt ingediend in het cluster, moet de GARENwachtrij waarin de taak wordt uitgevoerd, worden gedefinieerd. U een [YARN Capacity Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) gebruiken om langlopende taken in te dienen voor afzonderlijke wachtrijen.

* Sluit uw streaming applicatie sierlijk af. Als uw verschuivingen bekend zijn en alle toepassingsstatus extern wordt opgeslagen, u uw streamingtoepassing programmatisch stoppen op de juiste plaats. Een techniek is het gebruik van "draadhaken" in Spark, door te controleren op een externe vlag elke *n* seconden. U ook een *markeringsbestand* gebruiken dat op HDFS is gemaakt bij het starten van de toepassing en vervolgens wordt verwijderd wanneer u wilt stoppen. Gebruik voor een benadering van het markeringsbestand een afzonderlijke thread in uw Spark-toepassing die code oproept die vergelijkbaar is met deze:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Apache Spark-streaming](apache-spark-streaming-overview.md)
* [Apache Spark-streamingtaken maken met precies één gebeurtenisverwerking](apache-spark-streaming-exactly-once.md)
* [Langlopende Apache Spark Streaming Jobs op YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Structured Streaming: Fouttolerante semantiek](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Gedescretiseerde streams: een fouttolerant model voor schaalbare streamverwerking](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
