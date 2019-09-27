---
title: Aangepaste maven-pakketten gebruiken met Jupyter in Spark in azure HDInsight
description: Stapsgewijze instructies voor het configureren van Jupyter-notebooks die beschikbaar zijn met HDInsight Spark-clusters voor het gebruik van aangepaste maven-pakketten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: cd88c85c927c635269d814c20b15f574212e1a6d
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337688"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Externe pakketten met Jupyter-notebooks gebruiken in Apache Spark clusters in HDInsight

> [!div class="op_single_selector"]
> * [Cell Magic gebruiken](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Script actie gebruiken](apache-spark-python-package-installation.md)

Meer informatie over het configureren van een [Jupyter notebook](https://jupyter.org/) in Apache Spark cluster op HDInsight voor het gebruik van externe, door de Community bijgedragen Apache **maven** -pakketten die niet zijn opgenomen in het cluster. 

U kunt in de [maven-opslag plaats](https://search.maven.org/) zoeken naar de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. Een volledige lijst met door de Community bijgedragen pakketten is bijvoorbeeld beschikbaar in [Spark-pakketten](https://spark-packages.org/).

In dit artikel leert u hoe u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket kunt gebruiken met de Jupyter-notebook.

## <a name="prerequisites"></a>Vereisten

U moet het volgende hebben:

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="use-external-packages-with-jupyter-notebooks"></a>Externe pakketten gebruiken met Jupyter-notebooks

1. Klik vanuit de [Azure Portal](https://portal.azure.com/), vanaf het startboard, op de tegel voor uw Spark-cluster (als u deze aan het startboard hebt vastgemaakt). U kunt ook naar uw cluster navigeren onder **Bladeren** > **HDInsight-clusters**.

1. Klik vanuit de blade Spark-cluster op **Snelkoppelingen**. Klik vervolgens vanuit het **Cluster-dashboard** op **Jupyter Notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    > [!NOTE]  
    > Mogelijk bereikt u de Jupyter-notebook voor uw cluster ook door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Maak een nieuwe notebook. Klik op **Nieuw**en klik vervolgens op **Spark**.
   
    ![Een nieuwe Jupyter]-notebook van Spark maken(./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "een nieuw Jupyter-notebook maken")

1. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Klik bovenaan op de naam van de notebook en wijzig deze in een beschrijvende naam.
   
    ![Een naam opgeven voor de notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Een naam opgeven voor de notebook")

1. U gebruikt de `%%configure` Magic om het notitie blok te configureren voor het gebruik van een extern pakket. Zorg er in notitie blokken die gebruikmaken van externe pakketten voor dat u de `%%configure` Magic aanroept in de code van de eerste cel. Dit zorgt ervoor dat de kernel zo is geconfigureerd dat het pakket wordt gebruikt voordat de sessie wordt gestart.

    >[!IMPORTANT]  
    >Als u vergeet de kernel in de eerste cel te configureren, kunt u de `%%configure` gebruiken met de para meter `-f`, maar wordt de sessie opnieuw gestart en gaat alle voortgang verloren.

    | HDInsight-versie | Opdracht |
    |-------------------|---------|
    |Voor HDInsight 3,3 en HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Voor HDInsight 3,5 en HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. Het bovenstaande fragment verwacht de Maven-coördinaten voor het externe pakket in de centrale opslag plaats maven. In dit fragment is `com.databricks:spark-csv_2.10:1.4.0` de Maven-coördinaat voor het **Spark-CSV-** pakket. Hier kunt u de coördinaten voor een pakket maken.
   
    a. Zoek het pakket in de Maven-opslag plaats. Voor dit artikel gebruiken we [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Verzamel de waarden voor **GroupId**, **ArtifactId**en **Version**uit de opslag plaats. Zorg ervoor dat de waarden die u verzamelt, overeenkomen met uw cluster. In dit geval gebruiken we een scala 2,10-en Spark 1.4.0-pakket, maar u moet mogelijk verschillende versies selecteren voor de juiste scala-of Spark-versie in uw cluster. U kunt de scala-versie in uw cluster vinden door `scala.util.Properties.versionString` uit te voeren in de Spark Jupyter-kernel of op Spark-verzen ding. U kunt de Spark-versie in uw cluster vinden door `sc.version` op Jupyter-notebooks uit te voeren.
   
    ![Externe pakketten gebruiken met Jupyter notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter notebook")
   
    c. De drie waarden samen voegen, gescheiden door een dubbele punt ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Voer de code-cel uit met de `%%configure` Magic. Hiermee configureert u de onderliggende livy-sessie voor het gebruik van het pakket dat u hebt ingevoerd. In de volgende cellen in het notitie blok kunt u het pakket nu gebruiken, zoals hieronder wordt weer gegeven.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Voor HDInsight 3,6 moet u het volgende code fragment gebruiken.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. U kunt vervolgens de fragmenten uitvoeren, zoals hieronder wordt weer gegeven, om de gegevens weer te geven van de data frame die u in de vorige stap hebt gemaakt.

        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Zie ook

* [Krijgt Apache Spark in azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: Interactieve gegevens analyse uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Externe Python-pakketten gebruiken met Jupyter-notebooks in Apache Spark clusters in HDInsight Linux](apache-spark-python-package-installation.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
