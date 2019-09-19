---
title: Aanbevolen procedures voor het Apache Spark van Azure HDInsight
description: Lees de aanbevolen procedures voor het gebruik van Apache Spark in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106123"
---
# <a name="apache-spark-best-practices"></a>Aanbevolen procedures Apache Spark

In dit artikel vindt u diverse aanbevolen procedures voor het gebruik van Apache Spark in azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hoe kan ik u Spark-taken uitvoeren of verzenden?

| Optie | Documenten |
|---|---|
| VSCode | [Gebruik de Spark-& Hive-Hulpprogram Ma's voor Visual Studio code](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Zelfstudie: Gegevens laden en query's uitvoeren op een Apache Spark cluster in azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Zelfstudie: Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Zelfstudie: Een scala maven-toepassing maken voor Apache Spark in HDInsight met behulp van IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin-notebooks | [Apache Zeppelin-notebooks gebruiken met Apache Spark-cluster in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Externe taak verzenden met livy | [Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hoe kan ik controleren en fouten opsporen in Spark-taken?

| Optie | Documenten |
|---|---|
| Azure Toolkit voor IntelliJ | [Fout bij het opsporen van Spark-taken met Azure-toolkit voor IntelliJ (preview-versie)](apache-spark-intellij-tool-failure-debug.md) |
| Azure-toolkit voor IntelliJ via SSH | [Lokaal of op afstand fouten opsporen in Apache Spark-toepassingen in een HDInsight-cluster met de Azure-toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure-toolkit voor IntelliJ via VPN | [Azure-toolkit voor IntelliJ gebruiken om fout opsporing uit te Apache Spark toepassingen op afstand in HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Taak grafiek op Apache Spark geschiedenis server | [Uitgebreide Apache Spark History Server gebruiken om fouten op te sporen in Apache Spark-toepassingen](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hoe kan ik mijn Spark-taken efficiënter uit te voeren?

| Optie | Documenten |
|---|---|
| I/o-cache | [Verbeter de prestaties van Apache Spark werk belastingen met behulp van de Azure HDInsight IO-cache (preview)](./apache-spark-improve-performance-iocache.md) |
| Configuratie-opties | [Apache Spark taken optimaliseren](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hoe kan ik verbinding maken met andere Azure-Services?

| Optie | Documenten |
|---|---|
| Apache Hive in HDInsight | [Apache Spark en Apache Hive integreren met de Hive-Warehouse connector](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Apache Spark gebruiken om Apache HBase-gegevens te lezen en te schrijven](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka op HDInsight | [Zelfstudie: Apache Spark Structured streamen gebruiken met Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Een lambda-architectuur implementeren op het Azure-platform](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Wat zijn mijn opslag opties?

| Optie | Documenten |
|---|---|
| Data Lake Storage Gen2 | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Azure-opslag gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark-instellingen configureren](apache-spark-settings.md)
* [Apache Spark-taken in HDInsight optimaliseren](apache-spark-perf.md)
