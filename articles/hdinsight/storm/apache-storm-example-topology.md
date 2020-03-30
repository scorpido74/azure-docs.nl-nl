---
title: Voorbeeld Apache Storm-topologieën in Azure HDInsight
description: 'Een lijst met voorbeeld Storm topologieën gemaakt en getest met Apache Storm op HDInsight met inbegrip van fundamentele C # en Java topologieën, en het werken met Event Hubs.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530660"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Voorbeeld Apache Storm topologieën en componenten voor Apache Storm op HDInsight

Het volgende is een lijst met voorbeelden die door Microsoft zijn gemaakt en onderhouden voor gebruik met [Apache Storm](https://storm.apache.org/) op HDInsight. Deze voorbeelden hebben betrekking op een verscheidenheid aan onderwerpen, van het maken van basis-C#- en Java-topologieën tot het werken met Azure-services zoals Event Hubs, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) op HDInsight en Azure Storage. Enkele voorbeelden laten ook zien hoe te werken met niet-Azure, of zelfs niet-Microsoft-technologieën, zoals SignalR en Socket.IO.

| Beschrijving | Demonstreert | Taal/Kader |
|:--- |:--- |:--- |
| [Schrijven naar Azure Data Lake-opslag vanuit Apache Storm](apache-storm-write-data-lake-store.md) |Schrijven naar Azure Data Lake-opslag |Java |
| [Bron van gebeurtenishub-spout en bout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Bron voor de Event Hub Spout en Bolt |Java |
| [Ontwikkelen java-gebaseerde topologieën voor Apache Storm op HDInsight][5797064f] |Maven |Java |
| [Ontwikkel C# topologieën voor Apache Storm op HDInsight met behulp van Visual Studio][16fce2d1] |HDInsight-tools voor Visual Studio |C#, Java |
| [Gebeurtenissen uit Azure-gebeurtenishubs verwerken met Apache Storm op HDInsight (C#)][844d1d81] |Event Hubs |C# en Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Gebeurtenissen uit Azure Event Hubs verwerken met Storm op HDInsight (Java)) |Event Hubs |Java |
| [Voertuigsensorgegevens van Event Hubs verwerken met Apache Storm op HDInsight][246ee964] |Gebeurtenishubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [ETL (Extract, Transform, and Load) van Azure Event Hubs naar Apache HBase, met Apache Storm op HDInsight][b4b68194] |Gebeurtenishubs, HBase |C# |
| [Sjabloon C# Stormtopologieproject voor het werken met Azure-services van Apache Storm op HDInsight][ce0c02a2] |Gebeurtenishubs, Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Schaalbaarheidsbenchmarks voor het lezen van Azure Event Hubs met Apache Storm op HDInsight][d6c540e3] |Berichtdoorvoer, Gebeurtenishubs, SQL-database |C#, Java |
| [Gebruik Apache Kafka met Apache Storm op HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm lezen en schrijven naar Apache Kafka | Java |

> [!WARNING]  
> De C# voorbeelden in deze lijst zijn oorspronkelijk gemaakt en getest met Windows-gebaseerde HDInsight, en werkt mogelijk niet correct met Linux-gebaseerde HDInsight clusters. Linux-gebaseerde clusters gebruiken Mono om .NET-code uit te voeren en kunnen compatibiliteitsproblemen hebben met de frameworks en pakketten die in het voorbeeld worden gebruikt.
>
> Linux is het enige besturingssysteem dat wordt gebruikt op HDInsight versie 3.4 of hoger.

## <a name="python-only"></a>Alleen Python

Zie [Python gebruiken met Apache Storm op HDInsight](apache-storm-develop-python-topology.md) voor een voorbeeld van Python-componenten met een Flux-topologie.

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Storm-topologie maken en bewaken in Azure HDInsight](./apache-storm-quickstart.md)
* [Meer informatie over het implementeren en beheren van Apache Storm topologieën met Apache Storm op HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Meer informatie over het implementeren en beheren van topologieën met behulp van het webgebaseerde Apache Storm Dashboard en stormgebruikersinterface of de HDInsight-tools voor Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Meer informatie over het maken van C# Storm topologieën met behulp van de HDInsight Tools voor Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Meer informatie over het maken van Storm-topologieën in Java, met behulp van Maven, door een basistopologie voor woordtellingen te maken."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Meer informatie over het lezen en schrijven van gegevens van Azure Event Hubs met Storm op HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Lees hoe u een Storm-topologie gebruikt om berichten uit Azure Event Hubs te lezen, documenten van Azure Cosmos DB te lezen voor gegevensverwijzingen en gegevens op te slaan in Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Verschillende topologieën om doorvoer aan te tonen bij het lezen van Azure Event Hubs en het opslaan naar SQL Database met Apache Storm op HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Lees hoe u gegevens uit Azure Event Hubs lezen, de gegevens samenvoegen & transformeren en deze vervolgens opslaan in HBase op HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dit project bevat sjablonen voor spuiten, bouten en topologieën om te communiceren met verschillende Azure-services zoals Event Hubs, Cosmos DB en SQL Database."
