---
title: Wat is interactieve query in azure HDInsight?
description: Een inleiding tot interactieve Query's, ook wel Apache Hive LLAP genoemd, in azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: 3e0fddc03ad880d36876276827198b0ac180a00d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076364"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Wat is interactieve query in azure HDInsight?

Interactieve Query's (ook wel Apache Hive LLAP of [analyse verwerking met lage latentie](https://cwiki.apache.org/confluence/display/Hive/LLAP)) is een Azure HDInsight- [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interactieve Query's ondersteunen caching in het geheugen, waardoor Apache Hive query's sneller en veel meer interactief worden uitgevoerd. Klanten gebruiken interactieve Query's om gegevens op te vragen die zijn opgeslagen in azure Storage & Azure Data Lake Storage op super snelle manier. Met interactieve query's kunt u eenvoudig ontwikkel aars en gegevens wetenschapper samen werken met de big data met behulp van BI-tools die ze het meest leuk vinden. HDInsight Interactive query ondersteunt diverse hulpprogram ma's waarmee u op eenvoudige wijze toegang kunt krijgen tot big data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Een interactief query cluster wijkt af van een Apache Hadoop cluster. Het bevat alleen de component service.

U hebt alleen toegang tot de component service in het interactieve query cluster via Apache Ambari Hive View, Beeline en het micro soft Hive Open Database Connectivity-stuur programma (Hive ODBC). U hebt geen toegang via de Hive-console, Templeton, de klassieke Azure-CLI of Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Een interactief query cluster maken

Zie [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster. Kies het type interactieve query cluster.

> [!IMPORTANT]
> De minimale grootte van de hoofd knooppunt voor interactieve query clusters is Standard_D13_v2. Zie het [formaat diagram van Azure VM](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)voor meer informatie.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive query's uitvoeren vanuit een interactieve query

Als u Hive-query's wilt uitvoeren, hebt u de volgende opties:

* Micro soft Power BI gebruiken

    Zie [interactieve Query's visualiseren Apache Hive gegevens met Power bi in azure hdinsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Zie [visualiseren Big data met Power bi in azure hdinsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Visual Studio gebruiken

    Zie [verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van data Lake-Hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Visual Studio code gebruiken

    Zie [Visual Studio code gebruiken voor Apache Hive, LLAP of pySpark](../hdinsight-for-vscode.md).
* Voer Apache Hive uit met de Hive-weer gave Apache Ambari.
  
    Zie [Apache Hive weergave gebruiken met Apache Hadoop in azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Voer Apache Hive uit met behulp van Beeline.
  
    Zie [Apache Hive gebruiken met Apache Hadoop in HDInsight met beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    U kunt Beeline gebruiken vanuit het hoofd knooppunt of vanuit een lege Edge-knoop punt. We raden u aan Beeline te gebruiken vanaf een lege Edge-knoop punt. Zie [lege Edge-knoop punten in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)voor informatie over het maken van een HDInsight-cluster met behulp van een leeg Edge-knoop punt.
* Voer Apache Hive uit met behulp van Hive ODBC.
  
    Zie [Excel verbinden met Apache Hadoop met het micro soft Hive ODBC-stuur programma](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

De connection string van de Java Data Base Connectivity (JDBC) zoeken:

1. Meld u aan bij Apache Ambari met behulp van de `https://<cluster name>.AzureHDInsight.net`volgende URL:.
2. Selecteer **Hive**in het linkermenu.
3. Als u de URL wilt kopiëren, selecteert u het pictogram van het klem bord:

   ![HDInsight Hadoop Interactive query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van interactieve query clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Meer informatie over het [visualiseren van Big data met Power bi in azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Meer informatie over het [gebruik van Apache Zeppelin om Apache Hive query's uit te voeren in azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
