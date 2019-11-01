---
title: Microsoft Cognitive Toolkit met Apache Spark-Azure HDInsight
description: Meer informatie over hoe een getraind model voor Microsoft Cognitive Toolkit diep leren kan worden toegepast op een gegevensset met behulp van de Spark python API in een Azure HDInsight Spark cluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 0f4172c7a5b287c85c0548c7fe9812305a1ee1e6
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241533"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Microsoft Cognitive Toolkit diepe leer model gebruiken met Azure HDInsight Spark cluster

In dit artikel voert u de volgende stappen uit.

1. Voer een aangepast script uit om [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) te installeren op een Azure HDInsight Spark cluster.

2. Upload een [Jupyter notebook](https://jupyter.org/) naar het [Apache Spark](https://spark.apache.org/) -cluster om te zien hoe u een getraind Microsoft Cognitive Toolkit diepe leer model kunt Toep assen op bestanden in een Azure Blob Storage-account met behulp van de [Spark python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Voordat u aan dit artikel begint, moet u een Azure-abonnement hebben. Zie [Maak vandaag nog uw gratis Azure-account](https://azure.microsoft.com/free).

* **Azure HDInsight Spark cluster**. Voor dit artikel maakt u een Spark 2,0-cluster. Zie [Apache Spark cluster maken in azure HDInsight](apache-spark-jupyter-spark-sql.md)voor instructies.

## <a name="how-does-this-solution-flow"></a>Hoe gaat deze oplossing stroom?

Deze oplossing is verdeeld over dit artikel en een Jupyter-notebook die u als onderdeel van dit artikel uploadt. In dit artikel voert u de volgende stappen uit:

* Voer een script actie uit op een HDInsight Spark-cluster om Microsoft Cognitive Toolkit-en Python-pakketten te installeren.
* Upload de Jupyter-notebook waarmee de oplossing wordt uitgevoerd naar het HDInsight Spark-cluster.

De volgende resterende stappen worden behandeld in de Jupyter-notebook.

- Laad voorbeeld afbeeldingen in een door Spark Resiliant gedistribueerde gegevensset of RDD.
   - Modules laden en voor instellingen definiëren.
   - Down load de gegevensset lokaal op het Spark-cluster.
   - Converteer de gegevensset naar een RDD.
- Een score voor de afbeeldingen met behulp van een getraind Cognitive Toolkit model.
   - Down load het getrainde Cognitive Toolkit model naar het Spark-cluster.
   - Definieer functies die moeten worden gebruikt door worker-knoop punten.
   - Een score voor de afbeeldingen op worker-knoop punten.
   - Evalueer de nauw keurigheid van het model.


## <a name="install-microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit installeren

U kunt Microsoft Cognitive Toolkit op een Spark-cluster installeren met behulp van script acties. Script actie maakt gebruik van aangepaste scripts om onderdelen op het cluster te installeren die niet standaard beschikbaar zijn. U kunt het aangepaste script uit het Azure Portal gebruiken met behulp van HDInsight .NET SDK of met behulp van Azure PowerShell. U kunt ook het script gebruiken om de Toolkit te installeren als onderdeel van het maken van een cluster of nadat het cluster actief is. 

In dit artikel gebruiken we de portal om de Toolkit te installeren nadat het cluster is gemaakt. Zie [HDInsight-clusters aanpassen met script actie](../hdinsight-hadoop-customize-cluster-linux.md)voor andere manieren om het aangepaste script uit te voeren.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Zie [HDInsight-clusters aanpassen met script actie](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)voor instructies over het gebruik van de Azure Portal om script actie uit te voeren. Zorg ervoor dat u de volgende invoer geeft om Microsoft Cognitive Toolkit te installeren.

* Geef een waarde op voor de naam van de script actie.

* Voer voor **bash-script-URI**`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`in.

* Zorg ervoor dat u het script alleen uitvoert op de hoofd-en worker-knoop punten en schakel alle andere selectie vakjes uit.

* Klik op **Maken**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>De Jupyter-notebook uploaden naar Azure HDInsight Spark-cluster

Als u de Microsoft Cognitive Toolkit met het Azure HDInsight Spark cluster wilt gebruiken, moet u de Jupyter notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** naar het Azure HDInsight Spark cluster laden. Dit notitie blok is beschikbaar op GitHub op [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Kloon de GitHub-opslag plaats [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Zie voor instructies voor het klonen van [een opslag plaats](https://help.github.com/articles/cloning-a-repository/).

2. Open vanuit het Azure Portal de Blade Spark-cluster die u al hebt ingericht, klik op **cluster dashboard**en klik vervolgens op **Jupyter notebook**.

    U kunt de Jupyter-notebook ook starten door naar de URL te gaan `https://<clustername>.azurehdinsight.net/jupyter/`. Vervang \<clustername > door de naam van uw HDInsight-cluster.

3. Klik in de rechter bovenhoek van de Jupyter-notebook op **uploaden** en navigeer vervolgens naar de locatie waar u de GitHub-opslag plaats hebt gekloond.

    ![Jupyter-notebook uploaden naar Azure HDInsight Spark-cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Jupyter-notebook uploaden naar Azure HDInsight Spark-cluster")

4. Klik opnieuw op **uploaden** .

5. Nadat het notitie blok is geüpload, klikt u op de naam van het notitie blok en volgt u de instructies in het notitie blok zelf voor het laden van de gegevensset en het uitvoeren van het artikel.

## <a name="see-also"></a>Zie ook
* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's
* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetriegegevens voor gegevens analyse met behulp van Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren
* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
