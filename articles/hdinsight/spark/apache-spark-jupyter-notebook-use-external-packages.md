---
title: Aangepaste Maven-pakketten gebruiken met Jupyter in Spark - Azure HDInsight
description: Stapsgewijze instructies voor het configureren van Jupyter-laptops die beschikbaar zijn met HDInsight Spark-clusters om aangepaste Maven-pakketten te gebruiken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561730"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Externe pakketten gebruiken met Jupyter-laptops in Apache Spark-clusters op HDInsight

> [!div class="op_single_selector"]
> * [Celmagie gebruiken](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Scriptactie gebruiken](apache-spark-python-package-installation.md)

Meer informatie over het configureren van een [Jupyter-notitieblok](https://jupyter.org/) in Apache Spark-cluster op HDInsight voor het gebruik van externe, door de community bijgedragen **Apache-maven-pakketten** die niet out-of-the-box in het cluster zijn opgenomen.

U zoeken in de [Maven repository](https://search.maven.org/) voor de volledige lijst van pakketten die beschikbaar zijn. U ook een lijst met beschikbare pakketten uit andere bronnen krijgen. Een volledige lijst met door de community bijgedragen pakketten is bijvoorbeeld beschikbaar bij [Spark-pakketten.](https://spark-packages.org/)

In dit artikel leert u hoe u het [spark-csv-pakket](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) gebruiken met het Jupyter-notitieblok.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight. Zie [Gegevens laden en query's uitvoeren met Apache Spark op HDInsight](./apache-spark-load-data-run-query.md)voor meer informatie.

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit geldt `wasb://` voor Azure `abfs://` Storage, Azure Data `adl://` Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data `wasbs://` Lake `abfss://`Storage Gen2, is de URI respectievelijk secure transfer of , respectievelijk Zie ook, [secure transfer](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Externe pakketten gebruiken met Jupyter-notebooks

1. Navigeer `https://CLUSTERNAME.azurehdinsight.net/jupyter` naar `CLUSTERNAME` waar de naam van uw Spark-cluster is.

1. Maak een nieuwe notebook. Selecteer **Nieuw**en selecteer **Vervolgens Vonk**.

    ![Een nieuw Spark Jupyter-notitieblok maken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Een nieuwe Jupyter-notebook maken")

1. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Selecteer de naam van het notitieblok bovenaan en voer een vriendelijke naam in.

    ![Een naam opgeven voor de notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Een naam opgeven voor de notebook")

1. U gebruikt de `%%configure` magie om de notebook te configureren om een extern pakket te gebruiken. In notitieblokken die externe pakketten gebruiken, moet u de magie in de `%%configure` eerste codecel aanroepen. Dit zorgt ervoor dat de kernel is geconfigureerd om het pakket te gebruiken voordat de sessie begint.

    >[!IMPORTANT]  
    >Als u vergeet de kernel in de eerste `%%configure` cel `-f` te configureren, u de parameter met de parameter gebruiken, maar dat start de sessie opnieuw en alle voortgang gaat verloren.

    | HDInsight-versie | Opdracht |
    |-------------------|---------|
    | Voor HDInsight 3.5 en HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Voor HDInsight 3.3 en HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Het fragment hierboven verwacht de maven coördinaten voor het externe pakket in Maven Central Repository. In dit `com.databricks:spark-csv_2.11:1.5.0` fragment is de maven coördinaat voor **spark-csv** pakket. Zo bouw je de coördinaten voor een pakket.

    a. Zoek het pakket in de Maven Repository. Voor dit artikel gebruiken we [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Verzamel in de opslagplaats de waarden voor **GroupId,** **ArtifactId**en **Version**. Zorg ervoor dat de waarden die u verzamelt overeenkomen met uw cluster. In dit geval gebruiken we een Scala 2.11- en Spark 1.5.0-pakket, maar het kan zijn dat u verschillende versies moet selecteren voor de juiste Scala- of Spark-versie in uw cluster. U de Scala-versie op `scala.util.Properties.versionString` uw cluster vinden door te draaien op de Spark Jupyter-kernel of op Spark submit. U de Spark-versie op `sc.version` uw cluster achterhalen door op Jupyter-notitieblokken te draaien.

    ![Externe pakketten gebruiken met Jupyter-laptop](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter-laptop")

    c. Concatenate de drie waarden, gescheiden door een dikke darm (**:**).

        com.databricks:spark-csv_2.11:1.5.0

1. Voer de codecel `%%configure` uit met de magie. Hiermee configureert u de onderliggende Livy-sessie om het pakket te gebruiken dat u hebt opgegeven. In de volgende cellen in het notitieblok u het pakket nu gebruiken, zoals hieronder wordt weergegeven.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Voor HDInsight 3.4 en lager gebruik je het volgende fragment.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. U vervolgens de fragmenten uitvoeren, zoals hieronder weergegeven, om de gegevens te bekijken van het gegevensframe dat u in de vorige stap hebt gemaakt.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Gebruik externe python-pakketten met Jupyter-laptops in Apache Spark-clusters op HDInsight Linux](apache-spark-python-package-installation.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
