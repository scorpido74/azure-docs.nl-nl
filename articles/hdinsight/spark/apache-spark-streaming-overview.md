---
title: Spark Streaming in Azure HDInsight
description: Apache Spark Streaming-toepassingen gebruiken op HDInsight Spark-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406270"
---
# <a name="overview-of-apache-spark-streaming"></a>Overzicht van Apache Spark Streaming

[Apache Vonk](https://spark.apache.org/) Streaming biedt gegevensstroomverwerking op HDInsight Spark-clusters, met de garantie dat elke invoergebeurtenis precies één keer wordt verwerkt, zelfs als er een knooppuntfout optreedt. Een Spark Stream is een langlopende taak die invoergegevens ontvangt van een breed scala aan bronnen, waaronder Azure Event Hubs, een Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ,](http://zeromq.org/)raw TCP-sockets of van het bewaken van [Apache Hadoop-bestandsYARNsystems.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) In tegenstelling tot een alleen gebeurtenisgestuurd proces, voeren een Spark Stream-batches gegevens in tijdvensters in, zoals een segment van 2 seconden, en transformeert vervolgens elke batch gegevens met behulp van kaart-, verminderen-, join- en extractbewerkingen. De Spark Stream schrijft vervolgens de getransformeerde gegevens uit naar bestandssystemen, databases, dashboards en de console.

![Streamverwerking met HDInsight en Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming-toepassingen moeten een fractie van een seconde wachten om elke *microbatch* gebeurtenissen te verzamelen voordat deze batch wordt verzonden voor verwerking. Een eventgestuurde toepassing verwerkt daarentegen elke gebeurtenis onmiddellijk. Spark Streaming latentie is meestal minder dan een paar seconden. De voordelen van de microbatchbenadering zijn efficiëntere gegevensverwerking en eenvoudigere geaggregeerde berekeningen.

## <a name="introducing-the-dstream"></a>Maak kennis met de DStream

Spark Streaming vertegenwoordigt een continue stroom van binnenkomende gegevens met behulp van een *gedescretiseerde stroom* genaamd een DStream. Een DStream kan worden gemaakt op basis van invoerbronnen zoals Gebeurtenishubs of Kafka, of door transformaties toe te passen op een andere DStream.

Een DStream biedt een abstractielaag bovenop de ruwe gebeurtenisgegevens.

Begin met een enkele gebeurtenis, bijvoorbeeld een temperatuurmeting van een aangesloten thermostaat. Wanneer deze gebeurtenis bij uw Spark Streaming-toepassing aankomt, wordt de gebeurtenis op een betrouwbare manier opgeslagen, waarbij deze wordt gerepliceerd op meerdere knooppunten. Deze fouttolerantie zorgt ervoor dat het uitvallen van een enkel knooppunt niet leidt tot het verlies van uw gebeurtenis. De Spark-kern maakt gebruik van een gegevensstructuur die gegevens distribueert over meerdere knooppunten in het cluster, waarbij elk knooppunt over het algemeen zijn eigen gegevens in het geheugen behoudt voor de beste prestaties. Deze gegevensstructuur wordt een *resilient distributed dataset* (RDD) genoemd.

Elke RDD vertegenwoordigt gebeurtenissen die zijn verzameld over een door de gebruiker gedefinieerd tijdsbestek dat het *batch-interval*wordt genoemd. Naarmate elk batch-interval verstrijkt, wordt een nieuwe RDD geproduceerd die alle gegevens uit dat interval bevat. De continue set RDD's wordt verzameld in een DStream. Als het batch-interval bijvoorbeeld een seconde lang is, zendt uw DStream elke seconde een batch uit met één RDD die alle gegevens bevat die tijdens die seconde zijn ingenomen. Bij het verwerken van de DStream wordt de temperatuurgebeurtenis weergegeven in een van deze batches. Een Spark Streaming-toepassing verwerkt de batches die de gebeurtenissen bevatten en werkt uiteindelijk op basis van de gegevens die in elke RDD zijn opgeslagen.

![Voorbeeld DStream met temperatuurgebeurtenissen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Structuur van een Spark Streaming-toepassing

Een Spark Streaming-toepassing is een langlopende toepassing die gegevens ontvangt van innamebronnen, transformaties toepast om de gegevens te verwerken en vervolgens de gegevens naar een of meer bestemmingen duwt. De structuur van een Spark Streaming applicatie heeft een statisch onderdeel en een dynamisch onderdeel. Het statische gedeelte definieert waar de gegevens vandaan komen, wat de verwerking op de gegevens moet doen en waar de resultaten naartoe moeten gaan. Het dynamische deel draait de toepassing voor onbepaalde tijd, wachtend op een stopsignaal.

De volgende eenvoudige toepassing ontvangt bijvoorbeeld een tekstregel over een TCP-socket en telt het aantal keren dat elk woord wordt weergegeven.

### <a name="define-the-application"></a>De toepassing definiëren

De definitie van toepassingslogica bestaat uit vier stappen:

1. Maak een StreamingContext.
2. Maak een DStream vanuit de StreamingContext.
3. Transformaties toepassen op de DStream.
4. Uitvoer de resultaten.

Deze definitie is statisch en er worden geen gegevens verwerkt totdat u de toepassing uitvoert.

#### <a name="create-a-streamingcontext"></a>Een StreamingContext maken

Maak een StreamingContext vanuit de SparkContext die naar uw cluster verwijst. Wanneer u een StreamingContext maakt, geeft u bijvoorbeeld in enkele seconden de grootte van de batch op:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Een DStream maken

Maak met de instantie StreamingContext een invoerDStream voor uw invoerbron. In dit geval let de toepassing op het uiterlijk van nieuwe bestanden in de standaardopslag die is gekoppeld aan het HDInsight-cluster.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Transformaties toepassen

U implementeert de verwerking door transformaties toe te passen op de DStream. Deze toepassing ontvangt één regel tekst tegelijk uit het bestand, splitst elke regel in woorden en gebruikt vervolgens een kaartverkleinend patroon om het aantal keren te tellen dat elk woord wordt weergegeven.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Uitvoerresultaten

Duw de transformatieresultaten naar de doelsystemen door uitvoerbewerkingen toe te passen. In dit geval wordt het resultaat van elke run door de berekening afgedrukt in de console-uitvoer.

```
wordCounts.print()
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Start de streaming-toepassing en voer uit totdat een beëindigingssignaal is ontvangen.

```
ssc.start()
ssc.awaitTermination()
```

Zie [De Gebeurtenisbeheerhandleiding](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)voor meer informatie over de Spark Stream-API en de gebeurtenisbronnen, transformaties en uitvoerbewerkingen die deze ondersteunt.

De volgende voorbeeldtoepassing is op zichzelf staand, zodat u deze in een [Jupyter-notitieblok](apache-spark-jupyter-notebook-kernels.md)uitvoeren. In dit voorbeeld wordt een mock-gegevensbron in de klasse DummySource gemaakt die de waarde van een teller en de huidige tijd in milliseconden om de vijf seconden uitvoert. Een nieuw object StreamingContext heeft een batch-interval van 30 seconden. Elke keer dat een batch wordt gemaakt, onderzoekt de streamingtoepassing de geproduceerde RDD, converteert de RDD naar een Spark DataFrame en maakt een tijdelijke tabel over het DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Wacht ongeveer 30 seconden na het starten van de bovenstaande toepassing.  Vervolgens u het DataFrame periodiek opvragen om de huidige set waarden in de batch te bekijken, bijvoorbeeld met behulp van deze SQL-query:

```sql
%%sql
SELECT * FROM demo_numbers
```

De resulterende uitvoer ziet er als volgt uit:

| waarde | tijd |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Er zijn zes waarden, omdat de DummySource elke 5 seconden een waarde creëert en de toepassing elke 30 seconden een batch uitzendt.

## <a name="sliding-windows"></a>Schuiframen

Als u gedurende een bepaalde periode geaggregeerde berekeningen op uw DStream wilt uitvoeren, bijvoorbeeld om een gemiddelde temperatuur in de afgelopen twee seconden te krijgen, u de *schuifvensterbewerkingen* gebruiken die zijn meegeleverd met Spark Streaming. Een schuifvenster heeft een duur (de vensterlengte) en het interval waarin de inhoud van het venster wordt geëvalueerd (het schuifinterval).

Schuifvensters kunnen elkaar overlappen, bijvoorbeeld, u een venster definiëren met een lengte van twee seconden, dat elke seconde schuift. Dit betekent dat elke keer dat u een aggregatieberekening uitvoert, het venster gegevens van de laatste seconde van het vorige venster bevat, evenals eventuele nieuwe gegevens in de volgende seconde.

![Voorbeeld van het eerste venster met temperatuurgebeurtenissen](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Voorbeeldvenster met temperatuurgebeurtenissen na schuiven](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

In het volgende voorbeeld wordt de code bijgewerkt die de DummySource gebruikt, om de batches te verzamelen in een venster met een duur van één minuut en een dia van één minuut.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Na de eerste minuut zijn er 12 vermeldingen - zes vermeldingen van elk van de twee batches verzameld in het venster.

| waarde | tijd |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

De schuifvensterfuncties die beschikbaar zijn in de Spark Streaming API omvatten venster, countByWindow, reduceByWindow en countByValueAndWindow. Zie [Transformaties op DStreams voor](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams)meer informatie over deze functies.

## <a name="checkpointing"></a>Controlepunten plaatsen

Om tolerantie en fouttolerantie te bieden, vertrouwt Spark Streaming op controlepunten om ervoor te zorgen dat de streamverwerking ononderbroken kan worden voortgezet, zelfs bij knooppuntfouten. In HDInsight maakt Spark controlepunten voor duurzame opslag (Azure Storage of Data Lake Storage). Deze controlepunten slaan de metagegevens op over de streamingtoepassing, zoals de configuratie, de bewerkingen die door de toepassing zijn gedefinieerd en alle batches die in de wachtrij zijn geplaatst maar nog niet zijn verwerkt. In sommige gevallen zullen de controlepunten ook het opslaan van de gegevens in de RdD's omvatten om de status van de gegevens sneller weer op te bouwen van wat aanwezig is in de RDD's die door Spark worden beheerd.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-toepassingen implementeren

U bouwt een Spark Streaming-toepassing meestal lokaal in een JAR-bestand en implementeert deze vervolgens naar Spark op HDInsight door het JAR-bestand te kopiëren naar de standaardopslag die is gekoppeld aan uw HDInsight-cluster. U uw toepassing starten met de LIVY REST API's die beschikbaar zijn in uw cluster met behulp van een POST-bewerking. De hoofdtekst van de POST bevat een JSON-document dat het pad naar uw JAR biedt, de naam van de klasse waarvan de hoofdmethode de streamingtoepassing definieert en uitvoert, en optioneel de resourcevereisten van de taak (zoals het aantal uitvoerders, geheugen en kernen) , en alle configuratie-instellingen die uw toepassingscode vereist.

![Een Spark Streaming-toepassing implementeren](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

De status van alle aanvragen kan ook worden gecontroleerd met een GET-aanvraag tegen een LIVY-eindpunt. Ten slotte u een lopende toepassing beëindigen door een DELETE-aanvraag uit te vaardigen tegen het LIVY-eindpunt. Zie [Externe taken met Apache LIVY voor](apache-spark-livy-rest-interface.md) meer informatie over de LIVY-API

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Spark-cluster maken in Azure HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeerhandleiding voor het streamen van Apache Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Start Apache Spark-taken op afstand met Apache LIVY](apache-spark-livy-rest-interface.md)
