---
title: Aanbevolen procedures voor Apache Spark op Azure HDInsight
description: Meer informatie over aanbevolen procedures voor het gebruik van Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106123"
---
# <a name="apache-spark-best-practices"></a>Apache Spark best practices

In dit artikel vindt u verschillende aanbevolen procedures voor het gebruik van Apache Spark op Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hoe kan ik Spark-taken uitvoeren of indienen?

| Optie | Documenten |
|---|---|
| VSCode | [Spark & Hive-hulpprogramma's gebruiken voor visual studiocode](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Zelfstudie: Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen te maken voor een HDInsight-cluster](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Zelfstudie: Een Scala Maven-toepassing maken voor Apache Spark in HDInsight met behulp van IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin-notebooks | [Apache Zeppelin-notebooks gebruiken met Apache Spark-cluster in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Vacature op afstand bij Livy | [Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hoe kan ik Spark-taken controleren en debugen?

| Optie | Documenten |
|---|---|
| Azure-toolkit voor IntelliJ | [Fout mislukte foutopsporing van problemen met Azure Toolkit voor IntelliJ (voorbeeld)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit voor IntelliJ via SSH | [Lokaal of op afstand fouten opsporen in Apache Spark-toepassingen in een HDInsight-cluster met de Azure-toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit voor IntelliJ via VPN | [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen in HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Taakgrafiek op Apache Spark History Server | [Uitgebreide Apache Spark History Server gebruiken om fouten op te sporen in Apache Spark-toepassingen](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hoe zorg ik ervoor dat mijn Spark-taken efficiënter worden uitgevoerd?

| Optie | Documenten |
|---|---|
| IO-cache | [Prestaties van Apache Spark-workloads verbeteren met Azure HDInsight IO-cache (Preview)](./apache-spark-improve-performance-iocache.md) |
| Configuratie-opties | [Apache Spark-taken optimaliseren](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hoe maak ik verbinding met andere Azure Services?

| Optie | Documenten |
|---|---|
| Apache Hive op HDInsight | [Integreer Apache Spark en Apache Hive met de Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka op HDInsight | [Zelfstudie: Apache Spark Structured Streaming gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Een lambda-architectuur implementeren op het Azure-platform](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Wat zijn mijn opslagopties?

| Optie | Documenten |
|---|---|
| Data Lake Storage Gen2 | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Azure-opslag gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark-instellingen configureren](apache-spark-settings.md)
* [Apache Spark-taken optimaliseren in HDInsight](apache-spark-perf.md)
