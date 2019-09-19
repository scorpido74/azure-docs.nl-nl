---
title: Spark-streaming-taken met precies één keer per gebeurtenis verwerking-Azure HDInsight
description: Apache Spark streaming instellen om een gebeurtenis één keer en één keer te verwerken.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 908c49a46fe7993bc20bcb63a3c15758e2de5343
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091027"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Apache Spark streaming-taken maken met precies één keer per gebeurtenis verwerking

Toepassingen voor het verwerken van streams hebben verschillende benaderingen van het verwerken van berichten na een storing in het systeem:

* Ten minste één keer: Elk bericht wordt gegarandeerd verwerkt, maar kan meer dan één keer worden verwerkt.
* Maxi maal eenmaal: Elk bericht kan of kan niet worden verwerkt. Als een bericht wordt verwerkt, wordt het slechts eenmaal verwerkt.
* Slechts eenmaal: Elk bericht wordt gegarandeerd maar één keer verwerkt.

In dit artikel leest u hoe u Spark streaming kunt configureren om precies één keer te verwerken.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Exact eenmaal semantiek met Apache Spark streaming

Bedenk eerst hoe alle systeem punten van de fout opnieuw moeten worden opgestart na een probleem en hoe u gegevens verlies kunt voor komen. Een Spark-streaming-toepassing heeft:

* Een invoer bron.
* Een of meer ontvanger processen die gegevens uit de invoer bron ophalen.
* Taken die de gegevens verwerken.
* Een uitvoer sink.
* Een stuur programma dat de langlopende taak beheert.

Als er sprake is van een nauw keurige semantiek, moeten er op geen enkele wijze gegevens verloren gaan en kan de bericht verwerking opnieuw worden gestart, ongeacht waar de fout optreedt.

### <a name="replayable-sources"></a>Replaybare bronnen

De bron die door uw Spark-streaming-toepassing wordt gelezen, moet opnieuw worden *afgespeeld*. Dit betekent dat in gevallen waarin het bericht is opgehaald, maar dat het systeem is mislukt voordat het bericht persistent kon worden gemaakt of verwerkt. de bron moet hetzelfde bericht opnieuw opgeven.

In azure bieden zowel Azure Event Hubs als [Apache Kafka](https://kafka.apache.org/) op HDInsight replayable-bronnen. Een ander voor beeld van een replayable-bron is een fout tolerant bestands systeem zoals [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure Storage blobs of Azure data Lake Storage, waarbij alle gegevens permanent worden bewaard en op elk gewenst moment de gegevens volledig opnieuw kunnen worden gelezen.

### <a name="reliable-receivers"></a>Betrouw bare ontvangers

In Spark streaming hebben bronnen als Event Hubs en Kafka *betrouw bare ontvangers*, waarbij elke ontvanger de voortgang van het lezen van de bron bijhoudt. Een betrouw bare ontvanger persistent is de status van fout tolerante opslag, hetzij binnen [Apache ZooKeeper](https://zookeeper.apache.org/) , hetzij in de controle punten voor Spark-streaming, geschreven naar HDFS. Als een dergelijke ontvanger mislukt en later opnieuw wordt gestart, kan deze de locatie ophalen waar deze wordt verlaten.

### <a name="use-the-write-ahead-log"></a>Het Write-Ahead logboek gebruiken

Spark streaming ondersteunt het gebruik van een write-Ahead logboek waarbij elke ontvangen gebeurtenis eerst wordt geschreven naar de map met het controle punt van Spark in fout tolerante opslag en vervolgens wordt opgeslagen in een flexibele, gedistribueerde gegevensset (RDD). In azure wordt de fout tolerante opslag ondersteund door een Azure Storage of Azure Data Lake Storage. In uw Spark-streaming-toepassing wordt het Write-Ahead logboek ingeschakeld voor alle ontvangers door de `spark.streaming.receiver.writeAheadLog.enable` configuratie-instelling in te stellen op. `true` Het Write-Ahead logboek biedt fout tolerantie voor fouten van zowel het stuur programma als de uitvoerder.

Voor werk nemers die taken uitvoeren op basis van de gebeurtenis gegevens, wordt elke RDD per definitie gerepliceerd en gedistribueerd over meerdere werk rollen. Als een taak mislukt omdat het uitvoeren van de werk nemer is vastgelopen, wordt de taak opnieuw gestart op een andere werk nemer die een replica van de gebeurtenis gegevens heeft, waardoor de gebeurtenis niet verloren gaat.

### <a name="use-checkpoints-for-drivers"></a>Controle punten gebruiken voor Stuur Programma's

De taak Stuur Programma's moeten opnieuw worden gestart. Als het stuur programma dat uw Spark-streaming-toepassing uitvoert crasht, worden alle actieve ontvangers, taken en eventuele Rdd's opgeslagen. In dit geval moet u de voortgang van de taak kunnen opslaan, zodat u deze later kunt hervatten. Dit wordt bereikt door de gerichte acyclische grafiek (DAG) van de DStream periodiek te plaatsen op fout tolerante opslag. De DAG-meta gegevens bevatten de configuratie die wordt gebruikt voor het maken van de streaming-toepassing, de bewerkingen die de toepassing definiëren en alle batches die in de wachtrij staan, maar nog niet zijn voltooid. Met deze meta gegevens kan een defect stuur programma opnieuw worden gestart vanuit de controlepunt gegevens. Wanneer het stuur programma opnieuw wordt opgestart, worden nieuwe ontvangers gestart waarbij de gebeurtenis gegevens in het Rdd's worden hersteld.

Controle punten worden in twee stappen ingeschakeld in Spark-streaming. 

1. Configureer het opslagpad voor de controle punten in het StreamingContext-object:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    In HDInsight moeten deze controle punten worden opgeslagen in de standaard opslag die aan uw cluster is gekoppeld, hetzij Azure Storage als Azure Data Lake Storage.

2. Geef vervolgens een controlepunt interval (in seconden) op voor de DStream. De status gegevens die zijn afgeleid van de invoer gebeurtenis worden bij elk interval opgeslagen in de opslag. Blijvende status gegevens kunnen de reken kracht verminderen die nodig is om de status van de bron gebeurtenis opnieuw samen te stellen.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotent-sinks gebruiken

De doel-Sink waarnaar de taak de resultaten schrijft, moet de situatie kunnen afhandelen wanneer deze hetzelfde resultaat meermaals krijgt. De Sink moet in staat zijn om dergelijke dubbele resultaten te detecteren en te negeren. Een *idempotent* -Sink kan meermaals worden aangeroepen met dezelfde gegevens zonder wijziging van de status.

U kunt idempotent-sinks maken door logica te implementeren die eerst controleert op het bestaan van het binnenkomende resultaat in de gegevens opslag. Als het resultaat al bestaat, wordt de schrijf actie in het perspectief van uw Spark-taak weer gegeven, maar in werkelijkheid worden de dubbele gegevens door de gegevens opslag genegeerd. Als het resultaat niet bestaat, moet de Sink dit nieuwe resultaat invoegen in de opslag. 

U kunt bijvoorbeeld een opgeslagen procedure gebruiken met Azure SQL Database waarmee gebeurtenissen in een tabel worden ingevoegd. Deze opgeslagen procedure zoekt eerst naar de gebeurtenis op basis van sleutel velden en alleen wanneer er geen overeenkomende gebeurtenis is gevonden, wordt de record ingevoegd in de tabel.

Een ander voor beeld is het gebruik van een gepartitioneerd bestands systeem, zoals Azure Storage blobs of Azure Data Lake Storage. In dit geval hoeft de Sink-logica niet te controleren op het bestaan van een bestand. Als het bestand dat de gebeurtenis bevat, al bestaat, wordt dit eenvoudigweg overschreven met dezelfde gegevens. Anders wordt er een nieuw bestand gemaakt op het berekende pad.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Apache Spark streaming](apache-spark-streaming-overview.md)
* [Maxi maal beschik bare Apache Spark streaming-taken maken in Apache Hadoop GARENs](apache-spark-streaming-high-availability.md)
