---
title: Azure Monitor logboeken voor Apache Kafka-Azure HDInsight
description: Informatie over het gebruik van Azure Monitor logboeken voor het analyseren van logboeken van Apache Kafka cluster op Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 44eea1bc6390e743aff104550e5b6d7e97c45929
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960117"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Logboeken voor Apache Kafka op HDInsight analyseren

Informatie over het gebruik van Azure Monitor logboeken voor het analyseren van logboeken die zijn gegenereerd door Apache Kafka op HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor logboeken inschakelen voor Apache Kafka

De stappen voor het inschakelen van Azure Monitor logboeken voor HDInsight zijn hetzelfde voor alle HDInsight-clusters. Gebruik de volgende koppelingen om inzicht te krijgen in het maken en configureren van de vereiste services:

1. Maak een Log Analytics-werk ruimte. Zie de [Logboeken in azure monitor](../../azure-monitor/platform/data-platform-logs.md) document voor meer informatie.

2. Maak een Kafka in HDInsight-cluster. Zie het document [beginnen met Apache Kafka in HDInsight](apache-kafka-get-started.md) voor meer informatie.

3. Configureer het Kafka-cluster om Azure Monitor-logboeken te gebruiken. Zie [Azure monitor Logboeken gebruiken om HDInsight-documenten te controleren](../hdinsight-hadoop-oms-log-analytics-tutorial.md) voor meer informatie.

> [!IMPORTANT]  
> Het kan ongeveer 20 minuten duren voordat er gegevens beschikbaar zijn voor Azure Monitor Logboeken.

## <a name="query-logs"></a>Query-logboeken

1. Selecteer uw Log Analytics-werk ruimte in de [Azure Portal](https://portal.azure.com).

2. Ga naar het menu links en selecteer **Logboeken**onder **Algemeen**. Hier kunt u zoeken naar gegevens die zijn verzameld uit Kafka. Voer een query in het query venster in en selecteer vervolgens **uitvoeren**. Hier volgen enkele voor beelden van zoek opdrachten:

* Schijf gebruik:

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

* Inkomende berichten per seconde:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Binnenkomende bytes per seconde:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Uitgaande bytes per seconde:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Vervang de query waarden door uw specifieke cluster informatie. Bijvoorbeeld `ClusterName_s` moet worden ingesteld op de naam van uw cluster. `HostName_s`moet worden ingesteld op de domein naam van een werk knooppunt in het cluster.
    
    U kunt ook invoeren `*` om te zoeken naar alle typen die zijn geregistreerd. Momenteel zijn de volgende logboeken beschikbaar voor query's:
    
    | Logboektype | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka Broker-server. log |
    | log\_kafkacontroller\_CL | Kafka Broker controller. log |
    | metrische gegevens\_Kafkacl\_ | Kafka JMX-metrische gegevens |
    
    ![Afbeelding van de zoek opdracht voor het CPU-gebruik](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure monitor](../../log-analytics/log-analytics-get-started.md)voor meer informatie over Azure monitor en [query Azure monitor logboeken voor het bewaken van HDInsight-clusters](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Raadpleeg de volgende documenten voor meer informatie over het werken met Apache Kafka:

* [Mirror Apache Kafka tussen HDInsight-clusters](apache-kafka-mirroring.md)
* [De schaal baarheid van Apache Kafka op HDInsight verg Roten](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) gebruiken met Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Apache Spark Structured streamen gebruiken met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
