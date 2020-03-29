---
title: Streamen op schaal in Azure HDInsight
description: Datastreaming gebruiken met schaalbare Apache-clusters in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435400"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Schaalbaar streamen in HDInsight

Real-time big data-oplossingen werken op data die in beweging is. Typisch, deze gegevens is het meest waardevol op het moment van aankomst. Als de binnenkomende gegevensstroom groter wordt dan op dat moment kan worden afgehandeld, moet u mogelijk resources beperken. Een HDInsight-cluster kan ook worden opgeschaald om aan uw streaming-oplossing te voldoen door knooppunten op aanvraag toe te voegen.

In een streaming-toepassing genereren een of meer gegevensbronnen gebeurtenissen (soms in de miljoenen per seconde) die snel moeten worden ingenomen zonder nuttige informatie te laten vallen. De binnenkomende gebeurtenissen worden afgehandeld met *streambuffering*, ook wel *gebeurteniswachtrij genoemd,* door een service zoals [Apache Kafka](kafka/apache-kafka-introduction.md) of [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Nadat u de gebeurtenissen hebt verzameld, u de gegevens analyseren met behulp van een realtime analysesysteem binnen de *streamverwerkingslaag,* zoals [Apache Storm](storm/apache-storm-overview.md) of Apache [Spark Streaming.](spark/apache-spark-streaming-overview.md) De verwerkte gegevens kunnen worden opgeslagen in opslagsystemen voor de lange termijn, zoals [Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)en in realtime worden weergegeven op een business intelligence-dashboard, zoals [Power BI,](https://powerbi.microsoft.com)Tableau of een aangepaste webpagina.

![Azure HDInsight-streamingpatronen](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka biedt een service met een hoge doorvoer, lage latentie in de wachtrij en maakt nu deel uit van de Apache-suite van Open Source Software (OSS). Kafka gebruikt een berichtenmodel voor publiceren en abonneren en slaat stromen van partitiegegevens veilig op in een gedistribueerd, gerepliceerd cluster. Kafka schaalt lineair naarmate de doorvoer toeneemt.

Zie [Apache Kafka introduceren op HDInsight voor](kafka/apache-kafka-introduction.md)meer informatie.

## <a name="apache-storm"></a>Apache Storm

Apache Storm is een gedistribueerd, fouttolerant, open-source berekeningssysteem dat is geoptimaliseerd voor het verwerken van gegevensstromen in realtime met Hadoop. De kerneenheid van gegevens voor een binnenkomende gebeurtenis is een Tuple, een onveranderlijke set sleutel/waardeparen. Een onbegrensde opeenvolging van deze Tuples vormt een Stroom, die uit een Spout komt. De Spout omsluit een streaming gegevensbron (zoals Kafka) en zendt Tuples uit. Een storm Topologie is een opeenvolging van transformaties op deze stromen.

Zie [Wat is Apache Storm op Azure HDInsight voor](storm/apache-storm-overview.md)meer informatie.

## <a name="spark-streaming"></a>Spark-streaming

Spark Streaming is een extensie voor Spark, waarmee u dezelfde code hergebruiken die u gebruikt voor batchverwerking. U zowel batch- als interactieve query's combineren in dezelfde toepassing. In tegenstelling tot Storm biedt Spark Streaming stateful precies een keer verwerking semantiek. Bij gebruik in combinatie met de [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), die ervoor zorgt dat alle Kafka-gegevens precies één keer door Spark Streaming worden ontvangen, is het mogelijk om precies één keer end-to-end te bereiken. Een van de sterke punten van Spark Streaming is de fouttolerante mogelijkheden, waarbij defecte knooppunten snel worden hersteld wanneer meerdere knooppunten binnen het cluster worden gebruikt.

Zie Wat is Apache Spark Streaming voor meer [informatie?](hdinsight-spark-streaming-overview.md)

## <a name="scaling-a-cluster"></a>Een cluster schalen

Hoewel u tijdens het maken het aantal knooppunten in uw cluster opgeven, u het cluster laten groeien of verkleinen om de werkbelasting te laten overeenkomen. Met alle HDInsight-clusters u [het aantal knooppunten in het cluster wijzigen.](hdinsight-administer-use-portal-linux.md#scale-clusters) Spark-clusters kunnen worden verwijderd zonder verlies van gegevens, omdat alle gegevens worden opgeslagen in Azure Storage of Data Lake Storage.

Er zijn voordelen aan het ontkoppelen van technologieën. Kafka is bijvoorbeeld een gebeurtenisbuffertechnologie, dus het is zeer IO-intensief en heeft niet veel verwerkingskracht nodig. Ter vergelijking: streamprocessors zoals Spark Streaming zijn rekenintensief, waardoor krachtigere VM's nodig zijn. Door deze technologieën in verschillende clusters te laten ontkoppelen, u ze onafhankelijk schalen terwijl u de VM's het beste gebruikt.

### <a name="scale-the-stream-buffering-layer"></a>De bufferlaag van de stroomschalen

De streambufferingtechnologieën Event Hubs en Kafka maken beide gebruik van partities en consumenten lezen uit die partities. Het schalen van de invoerdoorvoer vereist het opschalen van het aantal partities en het toevoegen van partities zorgt voor toenemende parallellisme. In gebeurtenishubs kan het aantal partities niet worden gewijzigd na implementatie, dus het is belangrijk om te beginnen met de doelschaal in gedachten. Met Kafka is het mogelijk om [partities toe](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)te voegen, zelfs terwijl Kafka gegevens verwerkt. Kafka biedt een hulpmiddel om `kafka-reassign-partitions.sh`partities opnieuw toe te wijzen. HDInsight biedt een [hulpmiddel voor het opnieuw balanceren van partitiereplica's](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Deze rebalancing tool `kafka-reassign-partitions.sh` roept de tool op een zodanige wijze dat elke replica is in een aparte fout domein en update domein, waardoor Kafka rack bewust en toenemende fouttolerantie.

### <a name="scale-the-stream-processing-layer"></a>De stroomverwerkingslaag schalen

Zowel Apache Storm als Spark Streaming ondersteunen het toevoegen van worker nodes aan hun clusters, zelfs terwijl gegevens worden verwerkt.

Als u wilt profiteren van nieuwe knooppunten die zijn toegevoegd door storm te schalen, moet u eventuele stormtopologieën opnieuw in evenwicht brengen voordat de clustergrootte werd vergroot. Deze rebalancing kan worden gedaan met behulp van de Storm web UI of de CLI. Zie voor meer informatie de [documentatie van](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)apache storm .

Apache Spark gebruikt drie belangrijke parameters voor het configureren `spark.executor.instances`van `spark.executor.cores`de `spark.executor.memory`omgeving, afhankelijk van de toepassingsvereisten: , , en . Een *executor* is een proces dat wordt gestart voor een Spark-toepassing. Een executeur draait op het werknemersknooppunt en is verantwoordelijk voor het uitvoeren van de taken van de toepassing. Het standaardaantal uitvoerders en de uitvoerdergroottes voor elk cluster worden berekend op basis van het aantal werknemersknooppunten en de grootte van het werknemersknooppunt. Deze nummers worden `spark-defaults.conf`opgeslagen in het bestand op elk clusterhoofdknooppunt.

Deze drie parameters kunnen worden geconfigureerd op clusterniveau, voor alle toepassingen die op het cluster worden uitgevoerd, en kunnen ook worden opgegeven voor elke afzonderlijke toepassing. Zie [Resources voor Apache Spark-clusters beheren voor](spark/apache-spark-resource-manager.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Storm-topologie maken en bewaken in Azure HDInsight](storm/apache-storm-quickstart.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](storm/apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
* [Inleiding tot Apache Spark op HDInsight](spark/apache-spark-overview.md)
* [Met Apache Kafka in HDInsight beginnen](kafka/apache-kafka-get-started.md)
