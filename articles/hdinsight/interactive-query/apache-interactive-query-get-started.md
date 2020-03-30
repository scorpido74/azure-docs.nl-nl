---
title: Wat is Interactieve query in Azure HDInsight?
description: Een inleiding tot Interactieve Query, ook wel Apache Hive LLAP genoemd, in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: e133e08e333cb478269a93cce963566e195d6949
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271957"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Wat is interactieve query in Azure HDInsight

Interactieve query (ook wel Apache Hive LLAP of [Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP)is een Azure HDInsight-clustertype. [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type) Interactive Query ondersteunt caches in het geheugen, waardoor Apache Hive-query's sneller en veel interactiever worden. Klanten gebruiken Interactieve query om gegevens die zijn opgeslagen in Azure-opslag & Azure Data Lake Storage supersnel op te vragen. Interactieve query maakt het gemakkelijk voor ontwikkelaars en data scientist om te werken met de big data met behulp van BI-tools waar ze het meest van houden. HDInsight Interactive Query ondersteunt verschillende tools om op een eenvoudige manier toegang te krijgen tot big data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Een cluster interactieve query is anders dan een Apache Hadoop-cluster. Het bevat alleen de Hive-service.

U hebt alleen toegang tot de Hive-service in het cluster Interactieve query via Apache Ambari Hive View, Beeline en het Microsoft Hive Open Database Connectivity-stuurprogramma (Hive ODBC). U hebt er geen toegang toe via de Hive-console, Templeton, de Azure Classic CLI of Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Een cluster interactieve query maken

Zie [Apache Hadoop-clusters maken in HDInsight voor](../hdinsight-hadoop-provision-linux-clusters.md)informatie over het maken van een HDInsight-cluster. Kies het clustertype Interactieve query.

> [!IMPORTANT]
> De minimale headnodegrootte voor interactieve queryclusters is Standard_D13_v2. Zie de [Azure VM-formaatdiagram](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)voor meer informatie.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive-query's uitvoeren vanuit interactieve query

Als u Hive-query's wilt uitvoeren, hebt u de volgende opties:

|Methode |Beschrijving |
|---|---|
|Microsoft Power BI|Zie [Interactieve Query Apache Hive-gegevens visualiseren met Power BI in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)en [Visualiseer big data met Power BI in Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md)|
|Visual Studio|Zie [Verbinding maken met Azure HDInsight en Apache Hive-query's uitvoeren met Data Lake Tools voor Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)|
|Visual Studio Code|Zie [Visual Studio Code gebruiken voor Apache Hive, LLAP of pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari Hive View|Zie [Apache Hive View gebruiken met Apache Hadoop in Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Hive View is niet beschikbaar voor HDInsight 4.0.|
|Apache Beeline|Zie [Apache Hive gebruiken met Apache Hadoop in HDInsight met Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). U Beeline gebruiken vanaf het hoofdknooppunt of vanaf een leeg randknooppunt. We raden u aan Beeline te gebruiken vanaf een leeg randknooppunt. Zie [Lege randknooppunten gebruiken in HDInsight](../hdinsight-apps-use-edge-node.md)voor informatie over het maken van een HDInsight-cluster met behulp van een leeg randknooppunt.|
|Bijenkorf ODBC|Zie [Excel verbinden met Apache Hadoop met het Microsoft Hive ODBC-stuurprogramma](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

Ga als lid van het nieuws naar de verbindingstekenreeks (Java Database Connectivity):

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`naar `CLUSTERNAME` , waar is de naam van uw cluster.
1. Als u de URL wilt kopiëren, selecteert u het klembordpictogram:

   ![HDInsight Hadoop Interactieve Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van interactieve queryclusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Meer informatie over het [visualiseren van big data met Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Meer informatie over het [gebruik van Apache Zeppelin om Apache Hive-query's uit te voeren in Azure HDInsight.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
