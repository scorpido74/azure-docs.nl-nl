---
title: Spark Streaming & precies één gebeurtenisverwerking - Azure HDInsight
description: Apache Spark-streaming instellen om een gebeurtenis één keer en slechts één keer te verwerken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228974"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Apache Spark-streamingtaken maken met precies één gebeurtenisverwerking

Stream processing toepassingen nemen verschillende benaderingen van hoe ze omgaan met opwerking van berichten na een storing in het systeem:

* Ten minste één keer: Elk bericht wordt gegarandeerd verwerkt, maar het kan meerdere keer worden verwerkt.
* Hoogstens één keer: Elk bericht kan al dan niet worden verwerkt. Als een bericht wordt verwerkt, wordt het slechts één keer verwerkt.
* Precies één keer: Elk bericht wordt gegarandeerd één keer verwerkt.

In dit artikel ziet u hoe u Spark Streaming configureert om precies één keer verwerking te bereiken.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Precies-een semantiek met Apache Spark Streaming

Bedenk eerst hoe alle systeempunten opnieuw worden opgestart na een probleem en hoe u gegevensverlies voorkomen. Een Spark Streaming-toepassing heeft:

* Een invoerbron.
* Een of meer ontvangerprocessen die gegevens uit de invoerbron halen.
* Taken die de gegevens verwerken.
* Een uitgangsgootsteen.
* Een stuurprogrammaproces dat de langlopende taak beheert.

Precies één keer vereisen semantiek dat er op geen enkel moment gegevens verloren gaan en dat de verwerking van berichten opnieuw kan worden opgestart, ongeacht waar de fout optreedt.

### <a name="replayable-sources"></a>Herspeelbare bronnen

De bron van uw Spark Streaming-toepassing waar uw gebeurtenissen van worden gelezen, moet *herspeelbaar*zijn. Dit betekent dat in gevallen waarin het bericht is opgehaald, maar het systeem is mislukt voordat het bericht kan worden gehandhaafd of verwerkt, de bron hetzelfde bericht opnieuw moet geven.

In Azure bieden zowel Azure Event Hubs als [Apache Kafka](https://kafka.apache.org/) op HDInsight herspeelbare bronnen. Een ander voorbeeld van een herspeelbare bron is een fouttolerant bestandssysteem zoals [Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)Azure Storage blobs of Azure Data Lake Storage, waar alle gegevens voor altijd worden bewaard en op elk moment u de gegevens in zijn geheel herlezen.

### <a name="reliable-receivers"></a>Betrouwbare ontvangers

In Spark Streaming hebben bronnen zoals Event Hubs en Kafka *betrouwbare ontvangers,* waarbij elke ontvanger de voortgang bijhoudt bij het lezen van de bron. Een betrouwbare ontvanger blijft zijn staat in fouttolerante opslag, hetzij binnen [Apache ZooKeeper](https://zookeeper.apache.org/) of in Spark Streaming checkpoints geschreven naar HDFS. Als een dergelijke ontvanger uitvalt en later opnieuw wordt opgestart, kan deze verder gaan waar hij was gebleven.

### <a name="use-the-write-ahead-log"></a>Het logboek voor schrijven gebruiken

Spark Streaming ondersteunt het gebruik van een Write-Ahead Log, waarbij elke ontvangen gebeurtenis eerst wordt geschreven naar spark's checkpoint directory in fouttolerante opslag en vervolgens opgeslagen in een Resilient Distributed Dataset (RDD). In Azure wordt de fouttolerante opslag ondersteund door Azure Storage of Azure Data Lake Storage. In uw Spark Streaming-toepassing is de Write-Ahead Log `spark.streaming.receiver.writeAheadLog.enable` ingeschakeld voor `true`alle ontvangers door de configuratie-instelling in te stellen op . De Write-Ahead Log biedt fouttolerantie voor storingen van zowel het stuurprogramma als de uitvoerders.

Voor werknemers die taken uitvoeren op basis van de gebeurtenisgegevens, wordt elke RDD per definitie gerepliceerd en verdeeld over meerdere werknemers. Als een taak mislukt omdat de werknemer die deze uitvoert, is vastgelopen, wordt de taak opnieuw gestart op een andere werknemer met een replica van de gebeurtenisgegevens, zodat de gebeurtenis niet verloren gaat.

### <a name="use-checkpoints-for-drivers"></a>Controlepunten gebruiken voor bestuurders

De job drivers moeten opnieuw op te starten. Als het stuurprogramma waarop de Spark Streaming-toepassing wordt uitgevoerd, wordt deze uitgeschakeld met alle actieve ontvangers, taken en eventuele RDD's die gebeurtenisgegevens opslaan. In dit geval moet u de voortgang van de taak kunnen opslaan, zodat u deze later hervatten. Dit wordt bereikt door de Gerichte Acyclische Grafiek (DAG) van de DStream periodiek te controleren op fouttolerante opslag. De DAG-metagegevens bevatten de configuratie die wordt gebruikt om de streaming-toepassing te maken, de bewerkingen die de toepassing definiëren en alle batches die in de wachtrij staan maar nog niet zijn voltooid. Met deze metagegevens kan een mislukt stuurprogramma opnieuw worden gestart vanaf de controlepuntgegevens. Wanneer de driver opnieuw wordt opgestart, worden nieuwe ontvangers gestart die zelf de gebeurtenisgegevens herstellen in RDD's uit het Write-Ahead Log.

Controlepunten zijn in twee stappen ingeschakeld in Spark Streaming.

1. Configureer in het object StreamingContext het opslagpad voor de controlepunten:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    In HDInsight moeten deze controlepunten worden opgeslagen in de standaardopslag die is gekoppeld aan uw cluster, azure storage of Azure Data Lake Storage.

2. Geef vervolgens een controlepunt-interval (in seconden) op in de DStream. Bij elk interval blijven statusgegevens die zijn afgeleid van de invoergebeurtenis aanhouden tot opslag. Persistentstatedata kunnen de berekening verminderen die nodig is bij het opnieuw opbouwen van de status van de brongebeurtenis.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Gebruik idempotente putten

De bestemmingsgootsteen waaraan uw taak resultaten schrijft, moet in staat zijn om de situatie aan te kunnen waarin deze meer dan eens hetzelfde resultaat heeft gekregen. De gootsteen moet in staat zijn om dergelijke dubbele resultaten te detecteren en te negeren. Een *idempotente* gootsteen kan meerdere keren worden aangeroepen met dezelfde gegevens zonder verandering van de status.

U idempotente sinks maken door logica te implementeren die eerst controleert op het bestaan van het binnenkomende resultaat in de datastore. Als het resultaat al bestaat, moet het schrijven lijken te slagen vanuit het perspectief van uw Spark-taak, maar in werkelijkheid heeft uw gegevensarchief de dubbele gegevens genegeerd. Als het resultaat niet bestaat, moet de gootsteen dit nieuwe resultaat in de opslag plaatsen.

U bijvoorbeeld een opgeslagen procedure gebruiken met Azure SQL Database die gebeurtenissen in een tabel invoegt. Met deze opgeslagen procedure wordt de gebeurtenis eerst op zoekt op belangrijke velden en alleen wanneer er geen overeenkomende gebeurtenis is gevonden, wordt de record ingevoegd in de tabel.

Een ander voorbeeld is het gebruik van een gepartitioneerd bestandssysteem, zoals Azure Storage blobs of Azure Data Lake Storage. In dit geval hoeft uw sink-logica niet te controleren op het bestaan van een bestand. Als het bestand dat de gebeurtenis vertegenwoordigt, wordt het gewoon overschreven met dezelfde gegevens. Anders wordt een nieuw bestand gemaakt op het berekende pad.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Apache Spark-streaming](apache-spark-streaming-overview.md)
* [Het creëren van zeer beschikbare Apache Spark Streaming banen in Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
