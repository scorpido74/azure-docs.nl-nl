---
title: Azure Monitor-logboeken voor Apache Kafka - Azure HDInsight
description: Meer informatie over het gebruik van Azure Monitor-logboeken om logboeken van het Apache Kafka-cluster op Azure HDInsight te analyseren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471177"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Logboeken analyseren voor Apache Kafka in HDInsight

Meer informatie over het gebruik van Azure Monitor-logboeken om logboeken te analyseren die zijn gegenereerd door Apache Kafka op HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Locatie logboeken

Apache Kafka logs in het `/var/log/kafka`cluster bevinden zich op . Kafka-logboeken worden niet opgeslagen of blijven bestaan in de levenscyclus van het cluster, ongeacht of beheerde schijven worden gebruikt. In de volgende tabel worden de beschikbare logboeken weergegeven.

|Logboek |Beschrijving |
|---|---|
|kafka.out|stdout en stderr van het Kafka-proces. U vindt Kafka opstarten en afsluiten logs in dit bestand.|
|server.log|Het belangrijkste Kafka-serverlogboek. Alle Kafka broker logs komen hier terecht.|
|controller.log|Controller logboeken als de makelaar optreedt als controller.|
|statechange.log|Alle statuswijzigingsgebeurtenissen in makelaars worden in dit bestand geregistreerd.|
|kafka-gc.log|Kafka Garbage Collection statistieken.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor-logboeken inschakelen voor Apache Kafka

De stappen om Azure Monitor-logboeken in te schakelen voor HDInsight zijn hetzelfde voor alle HDInsight-clusters. Gebruik de volgende koppelingen om te begrijpen hoe u de vereiste services maakt en configureert:

1. Maak een werkruimte Log Analytics. Zie het document [Logboeken in Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) voor meer informatie.

2. Maak een Kafka op HDInsight-cluster. Zie het document [Start with Apache Kafka op HDInsight](apache-kafka-get-started.md) voor meer informatie.

3. Configureer het Kafka-cluster om Azure Monitor-logboeken te gebruiken. Zie de [logboeken Azure Monitor gebruiken om het HDInsight-document te controleren](../hdinsight-hadoop-oms-log-analytics-tutorial.md) voor meer informatie.

> [!IMPORTANT]  
> Het kan ongeveer 20 minuten duren voordat gegevens beschikbaar zijn voor Azure Monitor-logboeken.

## <a name="query-logs"></a>Querylogboeken

1. Selecteer in de [Azure-portal](https://portal.azure.com)uw werkruimte Log Analytics.

2. Selecteer **Logboeken**in het linkermenu onder **Algemeen**. Vanaf hier u zoeken in de gegevens verzameld van Kafka. Voer een query in het queryvenster in en selecteer **Uitvoeren**. Hieronder volgen enkele voorbeeldzoekopdrachten:

* Schijfgebruik:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-gebruik:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Binnenkomende berichten per seconde: `your_kafka_cluster_name` (Vervangen door uw clusternaam.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Binnenkomende bytes per seconde: (Vervangen `wn0-kafka` door de hostnaam van een werknemersknooppunt.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Uitgaande bytes per seconde: (Vervangen `your_kafka_cluster_name` door uw clusternaam.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    U `*` ook invoeren om alle geregistreerde typen te doorzoeken. Momenteel zijn de volgende logboeken beschikbaar voor query's:

    | Logboektype | Beschrijving |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_\_kafka CL | Kafka JMX-statistieken |

    ![Apache kafka log analytics cpu-gebruik](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Volgende stappen

Zie Overzicht van Azure [Monitor](../../log-analytics/log-analytics-get-started.md)en [Query Azure Monitor-logboeken voor het bewaken van HDInsight-clusters voor](../hdinsight-hadoop-oms-log-analytics-use-queries.md)meer informatie over Azure Monitor.

Zie de volgende documenten voor meer informatie over het werken met Apache Kafka:

* [Spiegel Apache Kafka tussen HDInsight clusters](apache-kafka-mirroring.md)
* [Verhoog de schaal van Apache Kafka op HDInsight](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) gebruiken met Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Gebruik Apache Spark gestructureerde streaming met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
