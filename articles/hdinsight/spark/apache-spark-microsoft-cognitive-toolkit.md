---
title: Microsoft Cognitive Toolkit met Apache Spark - Azure HDInsight
description: Ontdek hoe een getraind Microsoft Cognitive Toolkit deep learning-model kan worden toegepast op een gegevensset met behulp van de Spark Python API in een Azure HDInsight Spark-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206551"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Microsoft Cognitive Toolkit deep learning-model gebruiken met Azure HDInsight Spark-cluster

In dit artikel volgt u de volgende stappen.

1. Voer een aangepast script uit om [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) te installeren op een Azure HDInsight Spark-cluster.

2. Upload een [Jupyter-notitieblok](https://jupyter.org/) naar het [Apache Spark-cluster](https://spark.apache.org/) om te zien hoe u een getraind Microsoft Cognitive Toolkit deep learning-model toepast op bestanden in een Azure Blob-opslagaccount met behulp van de [Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Een Cluster Apache Spark maken](./apache-spark-jupyter-spark-sql-use-portal.md).

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight. Zie [Gegevens laden en query's uitvoeren met Apache Spark op HDInsight](./apache-spark-load-data-run-query.md)voor meer informatie.

## <a name="how-does-this-solution-flow"></a>Hoe stroomt deze oplossing?

Deze oplossing is verdeeld tussen dit artikel en een Jupyter notebook die u uploadt als onderdeel van dit artikel. In dit artikel voert u de volgende stappen uit:

* Voer een scriptactie uit op een HDInsight Spark-cluster om Microsoft Cognitive Toolkit- en Python-pakketten te installeren.
* Upload het Jupyter-notitieblok waarop de oplossing wordt uitgevoerd naar het HDInsight Spark-cluster.

De volgende resterende stappen worden behandeld in het Jupyter-notitieblok.

* Sampleafbeeldingen laden in een Spark Resilient Distributed Dataset of RDD.
  * Laad modules en definieer voorinstellingen.
  * Download de gegevensset lokaal in het Spark-cluster.
  * Converteer de gegevensset naar een RDD.
* Score de beelden met behulp van een getrainde Cognitieve Toolkit model.
  * Download het getrainde Cognitive Toolkit-model naar het Spark-cluster.
  * Definieer functies die door werknemersknooppunten moeten worden gebruikt.
  * Scoor de afbeeldingen op werkknooppunten.
  * Evalueer de nauwkeurigheid van het model.

## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit installeren

U Microsoft Cognitive Toolkit installeren op een Spark-cluster met behulp van scriptactie. Scriptactie gebruikt aangepaste scripts om componenten op het cluster te installeren die standaard niet beschikbaar zijn. U het aangepaste script gebruiken vanuit de Azure-portal, door HDInsight .NET SDK te gebruiken of met Azure PowerShell. U het script ook gebruiken om de toolkit te installeren als onderdeel van het maken van het cluster of nadat het cluster is uitgevoerd.

In dit artikel gebruiken we de portal om de toolkit te installeren, nadat het cluster is gemaakt. Zie [HDInsight-clusters aanpassen met Scriptactie](../hdinsight-hadoop-customize-cluster-linux.md)voor andere manieren om het aangepaste script uit te voeren.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Zie [HDInsight-clusters aanpassen met Scriptactie](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)voor instructies over het gebruik van de Azure-portal om scriptactie uit te voeren. Zorg ervoor dat u de volgende ingangen verstrekt om Microsoft Cognitive Toolkit te installeren. Gebruik de volgende waarden voor uw scriptactie:

|Eigenschap |Waarde |
|---|---|
|Scripttype|- Aangepast|
|Name| MCT installeren|
|Bash script URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Knooppunttype(s):|Hoofd, Arbeider|
|Parameters|Geen|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Het Jupyter-notitieblok uploaden naar Azure HDInsight Spark-cluster

Als u de Microsoft Cognitive Toolkit wilt gebruiken met het Azure HDInsight Spark-cluster, moet u het Jupyter-notitieblok **CNTK_model_scoring_on_Spark_walkthrough.ipynb** laden op het Azure HDInsight Spark-cluster. Deze notebook is beschikbaar op [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)GitHub op .

1. Downloaden en [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)uitpakken .

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/jupyter`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Selecteer in het Jupyter-notitieblok **Uploaden** in de rechterbovenhoek en `CNTK_model_scoring_on_Spark_walkthrough.ipynb`navigeer vervolgens naar het download- en selecteer bestand .

    ![Jupyter-laptop uploaden naar Azure HDInsight Spark-cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter-laptop uploaden naar Azure HDInsight Spark-cluster")

1. Selecteer Opnieuw **uploaden.**

1. Nadat het notitieblok is geüpload, klikt u op de naam van het notitieblok en volgt u de instructies in het notitieblok zelf over het laden van de gegevensset en het uitvoeren van het artikel.

## <a name="see-also"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetrie data-analyse met Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
