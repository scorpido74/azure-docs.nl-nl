---
title: Voor beeld Apache Storm topologieën in azure HDInsight
description: Een lijst met voor beelden van Storm-topologieën die zijn gemaakt en getest C# met Apache Storm op HDInsight, waaronder Basic-en Java-topologieën en werken met Event hubs.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 302ba583f11b15be98832316b1ea05c7f9be931f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530660"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Voor beeld Apache Storm topologieën en onderdelen voor Apache Storm in HDInsight

Hier volgt een lijst met voor beelden die door micro soft zijn gemaakt en onderhouden voor gebruik met [Apache Storm](https://storm.apache.org/) op HDInsight. Deze voor beelden hebben betrekking op diverse onderwerpen, van het C# maken van basis-en Java-topologieën voor het werken met Azure-Services, zoals Event Hubs, Cosmos DB, SQL database, [Apache HBase](https://hbase.apache.org/) in HDInsight en Azure Storage. Enkele voor beelden laten zien hoe u kunt werken met niet-Azure-of zelfs niet-micro soft-technologieën, zoals signalerings-en Socket.IO.

| Beschrijving | Hier ziet u | Taal/Framework |
|:--- |:--- |:--- |
| [Schrijven naar Azure Data Lake Storage vanuit Apache Storm](apache-storm-write-data-lake-store.md) |Schrijven naar Azure Data Lake Storage |Java |
| [Event hub-Spout en-flits bron](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Bron voor de Event hub-Spout en-flits |Java |
| [Op Java gebaseerde topologieën ontwikkelen voor Apache Storm op HDInsight][5797064f] |Maven |Java |
| [Topologieën ontwikkelen C# voor Apache Storm op HDInsight met behulp van Visual Studio][16fce2d1] |HDInsight-Hulpprogram Ma's voor Visual Studio |C#, Java |
| [Verwerken van gebeurtenissen uit Azure Event Hubs met Apache Storm op HDInsight (C#)][844d1d81] |Event Hubs |C#en Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Gebeurtenissen uit Azure Event Hubs verwerken met Storm op HDInsight (Java)) |Event Hubs |Java |
| [Voertuig sensor gegevens van Event Hubs verwerken met Apache Storm op HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [ETL van Azure Event Hubs naar Apache HBase extra heren, transformeren en laden met behulp van Apache Storm op HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Sjabloon C# Storm-topologie project voor het werken met Azure-services van Apache Storm op HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, signaal sterkte |C#, Java |
| [Schaalbaarheids benchmarks voor het lezen van Azure Event Hubs met behulp van Apache Storm in HDInsight][d6c540e3] |Bericht doorvoer, Event Hubs, SQL Database |C#, Java |
| [Apache Kafka gebruiken met Apache Storm op HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm lezen en schrijven naar Apache Kafka | Java |

> [!WARNING]  
> De C# voor beelden in deze lijst zijn oorspronkelijk gemaakt en getest met hdinsight die op Windows zijn gebaseerd en werken mogelijk niet goed met hdinsight-clusters op basis van Linux. Op Linux gebaseerde clusters gebruiken mono om .NET-code uit te voeren en kunnen compatibiliteits problemen ondervinden met de frameworks en pakketten die in het voor beeld worden gebruikt.
>
> Linux is het enige besturings systeem dat wordt gebruikt in HDInsight-versie 3,4 of hoger.

## <a name="python-only"></a>Alleen python

Zie [python gebruiken met Apache Storm op HDInsight](apache-storm-develop-python-topology.md) voor een voor beeld van python-onderdelen met een stroom topologie.

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Storm topologie in azure HDInsight maken en bewaken](./apache-storm-quickstart.md)
* [Meer informatie over het implementeren en beheren van Apache Storm topologieën met Apache Storm op HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Meer informatie over het implementeren en beheren van topologieën met behulp van het webgebaseerde Apache Storm dash board en Storm-gebruikers interface of de HDInsight-Hulpprogram Ma's voor Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Meer informatie over het C# maken van Storm-topologieën met behulp van de HDInsight-hulpprogram Ma's voor Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Meer informatie over het maken van Storm-topologieën in Java met behulp van Maven, door een eenvoudige WordCount-topologie te maken."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Meer informatie over het lezen en schrijven van gegevens van Azure Event Hubs met Storm op HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Meer informatie over het gebruik van een storm-topologie voor het lezen van berichten van Azure Event Hubs, het lezen van documenten van Azure Cosmos DB voor gegevens die verwijzen naar en het opslaan van gegevens naar Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Verschillende topologieën om de door voer te demonstreren bij het lezen van Azure Event Hubs en het opslaan naar SQL Database met behulp van Apache Storm op HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Meer informatie over het lezen van gegevens uit Azure Event Hubs, het samen voegen & de gegevens te transformeren en vervolgens op te slaan voor HBase in HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dit project bevat sjablonen voor spouts, schichten en topologieën om te communiceren met verschillende Azure-Services, zoals Event Hubs, Cosmos DB en SQL Database."
