---
title: Gestructureerde streaming stimuleren in Azure HDInsight
description: Spark Structured Streaming-toepassingen gebruiken op HDInsight Spark-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 19cfd5d8ed4100048c270fb41e5e54a920c61516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548833"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Overzicht van Apache Spark Structured Streaming

[Apache Vonk](https://spark.apache.org/) Structured Streaming stelt u in staat om schaalbare, krachtige, fouttolerante toepassingen te implementeren voor het verwerken van gegevensstromen. Structured Streaming is gebouwd op de Spark SQL-engine en verbetert de constructies van Spark SQL Data Frames en Datasets, zodat u streamingquery's schrijven op dezelfde manier als u batchquery's zou schrijven.  

Gestructureerde streamingtoepassingen worden uitgevoerd op HDInsight Spark-clusters en maken verbinding met streaminggegevens van [Apache Kafka,](https://kafka.apache.org/)een TCP-socket (voor foutopsporingsdoeleinden), Azure Storage of Azure Data Lake Storage. De laatste twee opties, die afhankelijk zijn van externe opslagservices, stellen u in staat om te kijken naar nieuwe bestanden die in opslag zijn toegevoegd en de inhoud ervan te verwerken alsof ze zijn gestreamd.

Structured Streaming maakt een langlopende query waarin u bewerkingen toepast op de invoergegevens, zoals selectie, projectie, aggregatie, windowing en het samenvoegen van het streaming DataFrame met referentiegegevensframes. Vervolgens u de resultaten uitvoergeven naar bestandsopslag (Azure Storage Blobs of Data Lake Storage) of naar een gegevensarchief met behulp van aangepaste code (zoals SQL Database of Power BI). Structured Streaming biedt ook uitvoer naar de console voor het opsporen van lokaal, en naar een in-memory tabel, zodat u de gegevens die worden gegenereerd voor foutopsporing in HDInsight zien.

![Streamprocessing met HDInsight en Spark Structured Streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Spark Structured Streaming vervangt Spark Streaming (DStreams). In de toekomst ontvangt Structured Streaming verbeteringen en onderhoud, terwijl DStreams alleen in de onderhoudsmodus staat. Structured Streaming is momenteel niet zo feature-complete als DStreams voor de bronnen en sinks die het ondersteunt out of the box, dus evalueer uw vereisten om de juiste Spark stream processing optie te kiezen.

## <a name="streams-as-tables"></a>Streams als tabellen

Spark Structured Streaming vertegenwoordigt een stroom van gegevens als een tabel die niet in de diepte is begrensd, dat wil zeggen dat de tabel blijft groeien als er nieuwe gegevens binnenkomen. Deze *invoertabel* wordt continu verwerkt door een langlopende query en de resultaten die naar een *uitvoertabel*worden verzonden:

![Gestructureerd streamingconcept](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

In Structured Streaming komen gegevens bij het systeem en worden ze onmiddellijk opgenomen in een invoertabel. U schrijft query's (met behulp van de API's Gegevensframe en Gegevensset) die bewerkingen uitvoeren tegen deze invoertabel. De queryuitvoer levert een andere tabel op, de *resultatentabel.* De resultatentabel bevat de resultaten van uw query, waaruit u gegevens maakt voor een externe datastore, zoals een relationele database. De timing van het tijdstip waarop gegevens worden verwerkt uit de invoertabel wordt gecontroleerd door het *triggerinterval*. Standaard is het triggerinterval nul, dus Structured Streaming probeert de gegevens te verwerken zodra deze binnenkomen. In de praktijk betekent dit dat zodra Structured Streaming klaar is met het verwerken van de vorige query, het een andere verwerking begint ten opzichte van nieuw ontvangen gegevens. U de trigger zo configureren dat deze met een interval wordt uitgevoerd, zodat de streaminggegevens in op tijd gebaseerde batches worden verwerkt.

De gegevens in de resultatentabellen mogen alleen de gegevens bevatten die nieuw zijn sinds de laatste keer dat de query is verwerkt *(toetewerkmodus)* of de tabel kan worden vernieuwd telkens wanneer er nieuwe gegevens zijn, zodat de tabel alle uitvoergegevens bevat sinds de streamingquery is gestart *(volledige modus).*

### <a name="append-mode"></a>Toetewerkmodus

In de toevoegmodus zijn alleen de rijen die aan de resultatentabel zijn toegevoegd sinds de laatste queryrun aanwezig in de resultatentabel en naar externe opslag geschreven. De eenvoudigste query kopieert bijvoorbeeld gewoon alle gegevens uit de invoertabel naar de resultatentabel ongewijzigd. Telkens wanneer een triggerinterval verstrijkt, worden de nieuwe gegevens verwerkt en worden de rijen die de nieuwe gegevens weergeven, weergegeven in de resultatentabel.

Overweeg een scenario waarin u telemetrie verwerkt met temperatuursensoren, zoals een thermostaat. Stel dat de eerste trigger één gebeurtenis op tijd 00:01 heeft verwerkt voor apparaat 1 met een temperatuurmeting van 95 graden. In de eerste trigger van de query wordt alleen de rij met tijd 00:01 weergegeven in de resultatentabel. Op tijd 00:02 wanneer een andere gebeurtenis aankomt, is de enige nieuwe rij de rij met tijd 00:02 en dus zou de resultatentabel slechts die ene rij bevatten.

![Modus Voor het toevoegen van gestructureerde streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Wanneer u de toekiesmodus gebruikt, past uw query projecties toe (waarbij de kolommen worden geselecteerd waar het om geeft), filtert (alleen rijen kiezen die aan bepaalde voorwaarden voldoen) of wordt lid (de gegevens vergroten met gegevens uit een statische opzoektabel). Append-modus maakt het gemakkelijk om alleen de relevante nieuwe gegevens wijst naar externe opslag.

### <a name="complete-mode"></a>Volledige modus

Overweeg hetzelfde scenario, dit keer met behulp van de volledige modus. In de volledige modus wordt de volledige uitvoertabel op elke trigger vernieuwd, zodat de tabel gegevens bevat, niet alleen van de meest recente triggerrun, maar van alle uitvoeringen. U de volledige modus gebruiken om de gegevens ongewijzigd van de invoertabel naar de resultatentabel te kopiëren. Bij elke geactiveerde run worden de nieuwe resultaatrijen weergegeven, samen met alle vorige rijen. De tabel met uitvoerresultaten zal uiteindelijk het opslaan van alle gegevens verzameld sinds de query begon, en je zou uiteindelijk opraken van het geheugen. De volledige modus is bedoeld voor gebruik met geaggregeerde query's die de binnenkomende gegevens op een bepaalde manier samenvatten, zodat op elke trigger de resultatentabel wordt bijgewerkt met een nieuw overzicht.

Stel dat er tot nu toe vijf seconden aan gegevens zijn verwerkt en het is tijd om de gegevens voor de zesde seconde te verwerken. De invoertabel heeft gebeurtenissen voor tijd 00:01 en tijd 00:03. Het doel van deze voorbeeldquery is om de gemiddelde temperatuur van het apparaat elke vijf seconden te geven. De implementatie van deze query past een aggregaat toe dat alle waarden neemt die binnen elk venster van 5 seconden vallen, de temperatuur gemiddeld en een rij produceert voor de gemiddelde temperatuur boven dat interval. Aan het einde van het eerste venster van 5 seconden zijn er twee tuples: (00:01, 1, 95) en (00:03, 1, 98). Dus voor het venster 00:00-00:05 produceert de aggregatie een tuple met de gemiddelde temperatuur van 96,5 graden. In het volgende venster van 5 seconden is er slechts één gegevenspunt op tijd 00:06, dus de resulterende gemiddelde temperatuur is 98 graden. Op het moment 00:10, met behulp van de volledige modus, de resultaten tabel heeft de rijen voor zowel windows 00:00-00:05 en 00:05-00:10 omdat de query uitgangen alle geaggregeerde rijen, niet alleen de nieuwe. Daarom blijft de resultatentabel groeien naarmate er nieuwe vensters worden toegevoegd.

![Complete modus voor gestructureerde streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Niet alle query's met de volledige modus zorgen ervoor dat de tabel zonder grenzen groeit.  Houd in het vorige voorbeeld rekening met het feit dat het gemiddelde in plaats van het gemiddelde van de temperatuur per tijdvenster, in plaats daarvan per apparaat-ID. De resultatentabel bevat een vast aantal rijen (één per apparaat) met de gemiddelde temperatuur voor het apparaat voor alle gegevenspunten die van dat apparaat zijn ontvangen. Als er nieuwe temperaturen worden ontvangen, wordt de resultatentabel bijgewerkt zodat de gemiddelden in de tabel altijd actueel zijn.

## <a name="components-of-a-spark-structured-streaming-application"></a>Onderdelen van een Spark Structured Streaming-toepassing

Een eenvoudige voorbeeldquery kan de temperatuurmetingen samenvatten op vensters van een uur. In dit geval worden de gegevens opgeslagen in JSON-bestanden in Azure Storage (gekoppeld als de standaardopslag voor het HDInsight-cluster):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Deze JSON-bestanden worden `temps` opgeslagen in de submap onder de container van het HDInsight-cluster.

### <a name="define-the-input-source"></a>De invoerbron definiëren

Configureer eerst een DataFrame waarin de bron van de gegevens en de instellingen die door die bron worden vereist, worden beschreven. In dit voorbeeld wordt gebruik gemaakt van de JSON-bestanden in Azure Storage en wordt een schema op deze bestanden op de leestijd gebruikt.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>De query toepassen

Pas vervolgens een query toe die de gewenste bewerkingen bevat tegen het Streaming DataFrame. In dit geval groepeert een aggregatie alle rijen in vensters van 1 uur en berekent vervolgens de minimum-, gemiddelde en maximumtemperaturen in dat venster van 1 uur.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>De uitvoergootsteen definiëren

Definieer vervolgens de bestemming voor de rijen die binnen elke triggerinterval aan de resultatentabel worden toegevoegd. In dit voorbeeld worden alleen alle rijen `temps` naar een tabel in het geheugen uitgevoerd die u later uitvoeren met SparkSQL. De volledige uitvoermodus zorgt ervoor dat alle rijen voor alle vensters elke keer worden uitgevoerd.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>De query starten

Start de streamingquery en voer uit totdat een beëindigingssignaal is ontvangen.

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>De resultaten bekijken

Terwijl de query wordt uitgevoerd, u in dezelfde SparkSession `temps` een SparkSQL-query uitvoeren op de tabel waarin de queryresultaten worden opgeslagen.

    select * from temps

Deze query levert resultaten op die vergelijkbaar zijn met de volgende:

| venster |  min(temp) | avg(temp) | max.temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Zie De Parameters voor gestructureerde streaming van [Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)voor meer informatie over de API voor structurele gegevens, en de uitvoergootstenen die deze ondersteunt.

## <a name="checkpointing-and-write-ahead-logs"></a>Controlepunten en vooruitschrijvende logboeken

Om tolerantie en fouttolerantie te bieden, vertrouwt Structured Streaming op *controlepunten* om ervoor te zorgen dat de streamverwerking ononderbroken kan worden voortgezet, zelfs bij knooppuntfouten. In HDInsight maakt Spark controlepunten voor duurzame opslag, azure storage of Data Lake Storage. Deze controlepunten slaan de voortgangsgegevens over de streamingquery op. Daarnaast maakt Structured Streaming gebruik van een *write-ahead log* (WAL). De WAL legt opgenomen gegevens vast die zijn ontvangen, maar nog niet zijn verwerkt door een query. Als er een fout optreedt en de verwerking opnieuw wordt gestart vanuit de WAL, gaan gebeurtenissen die van de bron zijn ontvangen, niet verloren.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-toepassingen implementeren

U bouwt een Spark Streaming-toepassing meestal lokaal in een JAR-bestand en implementeert deze vervolgens naar Spark op HDInsight door het JAR-bestand te kopiëren naar de standaardopslag die is gekoppeld aan uw HDInsight-cluster. U uw toepassing starten met de [Apache Livy](https://livy.incubator.apache.org/) REST API's die beschikbaar zijn in uw cluster met behulp van een POST-bewerking. De hoofdtekst van de POST bevat een JSON-document dat het pad naar uw JAR biedt, de naam van de klasse waarvan de hoofdmethode de streamingtoepassing definieert en uitvoert, en optioneel de resourcevereisten van de taak (zoals het aantal uitvoerders, geheugen en kernen) , en alle configuratie-instellingen die uw toepassingscode vereist.

![Een Spark Streaming-toepassing implementeren](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

De status van alle aanvragen kan ook worden gecontroleerd met een GET-aanvraag tegen een LIVY-eindpunt. Ten slotte u een lopende toepassing beëindigen door een DELETE-aanvraag uit te vaardigen tegen het LIVY-eindpunt. Zie [Externe taken met Apache LIVY voor](apache-spark-livy-rest-interface.md) meer informatie over de LIVY-API

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Spark-cluster maken in Azure HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeerhandleiding voor gestructureerde streaming van Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Start Apache Spark-taken op afstand met Apache LIVY](apache-spark-livy-rest-interface.md)
