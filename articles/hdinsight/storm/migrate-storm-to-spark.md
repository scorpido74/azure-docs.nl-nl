---
title: Azure HDInsight 3.6 Apache Storm migreren naar HDInsight 4.0 Apache Spark
description: De verschillen en migratiestroom voor het migreren van Apache Storm-workloads naar Spark Streaming of Spark Structured Streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157790"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Azure HDInsight 3.6 Apache Storm migreren naar HDInsight 4.0 Apache Spark

In dit document wordt beschreven hoe apache stormworkloads kunnen worden gemigreerd op HDInsight 3.6 naar HDInsight 4.0. HDInsight 4.0 ondersteunt het clustertype Apache Storm niet en u moet migreren naar een ander streamingdataplatform. Twee geschikte opties zijn Apache Spark Streaming en Spark Structured Streaming. Dit document beschrijft de verschillen tussen deze platforms en beveelt ook een workflow aan voor het migreren van Apache Storm-workloads.

## <a name="storm-migration-paths-in-hdinsight"></a>Stormmigratiepaden in HDInsight

Als u wilt migreren van Apache Storm op HDInsight 3.6, hebt u meerdere opties:

* Spark Streaming op HDInsight 4.0
* Spark Structured Streaming op HDInsight 4.0
* Azure Stream Analytics

Dit document biedt een handleiding voor het migreren van Apache Storm naar Spark Streaming en Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![HDInsight Storm migratiepad](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Vergelijking tussen Apache Storm en Spark Streaming, Spark Structured Streaming

Apache Storm kan verschillende niveaus van gegarandeerde berichtverwerking bieden. Een basistoepassing van Storm kan bijvoorbeeld ten minste eenmaal worden verwerkt en [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) kan precies één keer worden verwerkt. Spark Streaming en Spark Structured Streaming garanderen dat elke invoergebeurtenis precies één keer wordt verwerkt, zelfs als er een knooppuntfout optreedt. Storm heeft een model dat elke gebeurtenis verwerkt, en u ook het Micro Batch-model met Trident gebruiken. Spark Streaming en Spark Structured Streaming bieden micro-batch verwerkingsmodel.

|  |Storm |Spark-streaming | Gestructureerde streaming stimuleren|
|---|---|---|---|
|**Garantie voor gebeurtenisverwerking**|Ten minste eenmaal <br> Precies een keer (Trident) |[Precies een keer](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Precies een keer](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Verwerkingsmodel**|Real-time <br> Micro Batch (Trident) |Microbatch |Microbatch |
|**Ondersteuning voor gebeurtenistijd**|[Ja](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nee|[Ja](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Talen**|Java, enz.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark streaming vs Spark gestructureerde streaming

Spark Structured Streaming vervangt Spark Streaming (DStreams). Structured Streaming blijft verbeteringen en onderhoud ontvangen, terwijl DStreams alleen in de onderhoudsmodus staat. **Opmerking: links nodig om dit punt te benadrukken.** Structured Streaming heeft niet zoveel functies als DStreams voor de bronnen en gootstenen die het out of the box ondersteunt, dus evalueer uw vereisten om de juiste Spark stream processing optie te kiezen.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Verwerking van streaming (één gebeurtenis) vs Micro-Batch

Storm biedt een model dat elke gebeurtenis verwerkt. Dit betekent dat alle binnenkomende records worden verwerkt zodra ze aankomen. Spark Streaming-toepassingen moeten een fractie van een seconde wachten om elke microbatch gebeurtenissen te verzamelen voordat deze batch wordt verzonden voor verwerking. Een eventgestuurde toepassing verwerkt daarentegen elke gebeurtenis onmiddellijk. Spark Streaming latentie is meestal minder dan een paar seconden. De voordelen van de microbatchbenadering zijn efficiëntere gegevensverwerking en eenvoudigere geaggregeerde berekeningen.

> [!div class="mx-imgBorder"]
> ![streaming en verwerking van microbatch's](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Stormarchitectuur en componenten

Storm-topologieën bestaan uit meerdere onderdelen die zijn gerangschikt in een Directed Acyclic Graph (DAG). Gegevens stromen tussen de onderdelen in de grafiek. Elk onderdeel verbruikt een of meer gegevensstromen en kan eventueel een of meer stromen genereren.

|Onderdeel |Beschrijving |
|---|---|
|Tuit|Brengt gegevens in een topologie. Deze onderdelen introduceren een of meer stromen in de topologie.|
|Bout|Verbruikt streams die worden uitgestoten door tuiten of andere bouten. Bolts kunnen eventueel nieuwe stromen in de topologie introduceren. Bolts zijn ook verantwoordelijk voor het wegschrijven van gegevens naar externe services of opslag, zoals HDFS, Kafka of HBase.|

> [!div class="mx-imgBorder"]
> ![interactie van stormcomponenten](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm bestaat uit de volgende drie daemons, die de Storm cluster functioneren houden.

|Daemon |Beschrijving |
|---|---|
|Nimbus|Net als bij Hadoop JobTracker is het verantwoordelijk voor het distribueren van code rond het cluster en het toewijzen van taken aan machines en het monitoren op storingen.|
|Zookeeper|Gebruikt voor clustercoördinatie.|
|Supervisor|Luistert naar werk dat aan de machine is toegewezen en start en stopt werkprocessen op basis van richtlijnen van Nimbus. Elk werkproces voert een subset van een topologie uit. De toepassingslogica van de gebruiker (Spouts en Bolt) wordt hier uitgevoerd.|

> [!div class="mx-imgBorder"]
> ![nimbus, dierenverzorger en begeleider daemons](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark Streaming-architectuur en -componenten

In de volgende stappen wordt samengevat hoe componenten samenwerken in Spark Streaming (DStreams) en Spark Structured Streaming:

* Wanneer Spark Streaming wordt gestart, start het stuurprogramma de taak in de executor.
* De uitvoerder ontvangt een stream van een streaming gegevensbron.
* Wanneer de executeur gegevensstromen ontvangt, splitst deze de stroom op in blokken en houdt deze in het geheugen.
* Blokken van gegevens worden gerepliceerd naar andere uitvoerders.
* De verwerkte gegevens worden vervolgens opgeslagen in het doelgegevensarchief.

> [!div class="mx-imgBorder"]
> ![spark streaming pad naar uitvoer](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark Streaming (DStream) werkstroom

Naarmate elk batch-interval verstrijkt, wordt een nieuwe RDD geproduceerd die alle gegevens uit dat interval bevat. De continue sets RDD's worden verzameld in een DStream. Als het batch-interval bijvoorbeeld een seconde lang is, zendt uw DStream elke seconde een batch uit met één RDD die alle gegevens bevat die tijdens die seconde zijn ingenomen. Bij het verwerken van de DStream wordt de temperatuurgebeurtenis weergegeven in een van deze batches. Een Spark Streaming-toepassing verwerkt de batches die de gebeurtenissen bevatten en werkt uiteindelijk op basis van de gegevens die in elke RDD zijn opgeslagen.

> [!div class="mx-imgBorder"]
> ![spark streaming processing batches](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Zie [Transformaties op DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)voor meer informatie over de verschillende transformaties die beschikbaar zijn met Spark Streaming.

## <a name="spark-structured-streaming"></a>Gestructureerde streaming stimuleren

Spark Structured Streaming vertegenwoordigt een stroom van gegevens als een tabel die niet in de diepte is begrensd. De tabel blijft groeien als er nieuwe gegevens binnenkomen. Deze invoertabel wordt continu verwerkt door een langlopende query en de resultaten worden naar een uitvoertabel verzonden.

In Structured Streaming komen gegevens bij het systeem en worden ze onmiddellijk opgenomen in een invoertabel. U schrijft query's (met behulp van de API's Gegevensframe en Gegevensset) die bewerkingen uitvoeren tegen deze invoertabel.

De queryuitvoer levert een *resultatentabel*op, die de resultaten van uw query bevat. U gegevens uit de resultatentabel halen voor een externe datastore, zoals een relationele database.

De timing van het tijdstip waarop gegevens worden verwerkt vanuit de invoertabel wordt bepaald door het triggerinterval. Standaard is het triggerinterval nul, dus Structured Streaming probeert de gegevens te verwerken zodra deze binnenkomen. In de praktijk betekent dit dat zodra Structured Streaming klaar is met het verwerken van de vorige query, het een andere verwerking begint ten opzichte van nieuw ontvangen gegevens. U de trigger zo configureren dat deze met een interval wordt uitgevoerd, zodat de streaminggegevens in op tijd gebaseerde batches worden verwerkt.

> [!div class="mx-imgBorder"]
> ![verwerking van gegevens in gestructureerde streaming](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programmeermodel voor gestructureerde streaming](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Algemene migratiestroom

De aanbevolen migratiestroom van Storm naar Spark gaat uit van de volgende eerste architectuur:

* Kafka wordt gebruikt als streaming databron
* Kafka en Storm worden ingezet op hetzelfde virtuele netwerk
* De gegevens die door Storm worden verwerkt, worden naar een gegevenssink geschreven, zoals Azure Storage of Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagram van de veronderstelde huidige omgeving](./media/migrate-storm-to-spark/presumed-current-environment.png)

Ga als volgt te werk om uw toepassing van Storm te migreren naar een van de Spark-streaming API's:

1. **Een nieuw cluster implementeren.** Implementeer een nieuw HDInsight 4.0 Spark-cluster in hetzelfde virtuele netwerk en implementeer uw Spark Streaming- of Spark Structured Streaming-toepassing erop en test het grondig.

    > [!div class="mx-imgBorder"]
    > ![nieuwe spark-implementatie in HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Stop met consumeren op de oude Storm cluster.** In de bestaande Storm, stoppen met het consumeren van gegevens uit de streaming databron en wacht tot de gegevens klaar zijn met schrijven naar de doelgootsteen.

    > [!div class="mx-imgBorder"]
    > ![stoppen met consumeren op het huidige cluster](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Begin met consumeren op het nieuwe Spark-cluster.** Start streaminggegevens van een nieuw geïmplementeerd HDInsight 4.0 Spark-cluster. Op dit moment wordt het proces overgenomen door te consumeren van de nieuwste Kafka-offset.

    > [!div class="mx-imgBorder"]
    > ![beginnen met consumeren op nieuw cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Verwijder het oude cluster indien nodig.** Zodra de schakelaar is voltooid en goed werkt, verwijdert u de oude HDInsight 3.6 Storm cluster indien nodig.

    > [!div class="mx-imgBorder"]
    > ![oude HDInsight-clusters verwijderen als dat nodig is](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie over Storm, Spark Streaming en Spark Structured Streaming:

* [Overzicht van Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Overzicht van Apache Spark Structured Streaming](../spark/apache-spark-structured-streaming-overview.md)