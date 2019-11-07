---
title: Script actie voor python-pakketten met Jupyter in azure HDInsight
description: Stapsgewijze instructies voor het gebruik van script actie voor het configureren van Jupyter-notebooks die beschikbaar zijn met HDInsight Spark-clusters voor het gebruik van externe Python-pakketten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: e344035f05e192de1779a60fc99a7e0144566654
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682190"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>Script actie voor het installeren van externe Python-pakketten voor Jupyter-notebooks in Apache Spark in HDInsight

> [!div class="op_single_selector"]
> * [Cell Magic gebruiken](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Script actie gebruiken](apache-spark-python-package-installation.md)

Meer informatie over het gebruik van script acties voor het configureren van een [Apache Spark](https://spark.apache.org/) cluster op HDInsight voor het gebruik van externe, door de Community bijgedragen **python** -pakketten die niet zijn opgenomen in het cluster.

> [!NOTE]  
> U kunt ook een Jupyter-notebook configureren met behulp van `%%configure` Magic om externe pakketten te gebruiken. Zie voor instructies [externe pakketten met Jupyter-notebooks gebruiken in Apache Spark clusters op HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

U kunt de [pakket index](https://pypi.python.org/pypi) doorzoeken voor de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. U kunt bijvoorbeeld pakketten installeren die beschikbaar worden gesteld via [Conda-vervalsing](https://conda-forge.org/feedstocks/).

In dit artikel leert u hoe u het [tensor flow](https://www.tensorflow.org/) -pakket kunt installeren met behulp van script acties op uw cluster en dit te gebruiken via de Jupyter-notebook als voor beeld.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

   > [!NOTE]  
   > Als u nog geen Spark-cluster in HDInsight Linux hebt, kunt u script acties uitvoeren tijdens het maken van het cluster. Raadpleeg de documentatie over [het gebruik van aangepaste script acties](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Ondersteuning voor open-source software die wordt gebruikt in HDInsight-clusters

De Microsoft Azure HDInsight-service gebruikt een ecosysteem van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteunings niveau voor open-source technologieën. Zie de [website met veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor meer informatie. De HDInsight-service biedt een extra ondersteunings niveau voor ingebouwde componenten.

Er zijn twee soorten open source-onderdelen die beschikbaar zijn in de HDInsight-service:

* **Ingebouwde onderdelen** : deze onderdelen zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kern functionaliteit van het cluster. Bijvoorbeeld Apache Hadoop GARENs Resource Manager, de Apache Hive query language (HiveQL) en de mahout-bibliotheek behoren tot deze categorie. Een volledige lijst met cluster onderdelen is beschikbaar in [Wat is er nieuw in de Apache Hadoop cluster versies van HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Aangepaste onderdelen** : u kunt als gebruiker van het cluster elk onderdeel dat beschikbaar is in de Community installeren of gebruiken, of door u gemaakt.

> [!IMPORTANT]
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund. Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Micro soft ondersteuning kan het probleem mogelijk oplossen of u wordt gevraagd beschik bare kanalen te betrekken voor de open source-technologieën waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Apache-projecten hebben project sites op [https://apache.org](https://apache.org), bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Externe pakketten gebruiken met Jupyter-notebooks

1. Navigeer vanuit het [Azure Portal](https://portal.azure.com/)naar uw cluster.  

2. Als uw cluster is geselecteerd, selecteert u in het linkerdeel venster onder **instellingen**de optie **script acties**.

3. Selecteer **+ Nieuw verzenden**.

4. Voer de volgende waarden in voor het venster **actie script verzenden** :  

    |Parameter | Waarde |
    |---|---|
    |Script type | Selecteer **-aangepast** in de vervolg keuzelijst.|
    |Naam |Voer `tensorflow` in het tekstvak in.|
    |Bash-script-URI |Voer `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` in het tekstvak in. |
    |Knooppunt type (n) | Schakel de selectie vakjes **Head**en **worker** in. |

    `tensorflowinstall.sh` bevat de volgende opdrachten:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. Selecteer **Maken**.  Raadpleeg de documentatie over [het gebruik van aangepaste script acties](../hdinsight-hadoop-customize-cluster-linux.md).

6. Wacht totdat het script is voltooid.  In het deel venster **script acties** worden **nieuwe script acties vermeld, die kunnen worden verzonden nadat de huidige cluster bewerking is voltooid** terwijl het script wordt uitgevoerd.  Een voortgangs balk kan worden weer gegeven vanuit het venster Ambari gebruikers interface- **achtergrond bewerkingen** .

7. Open een PySpark Jupyter-notebook.  Zie [een Jupyter-notebook maken in Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) voor instructies.

    ![Een nieuwe Jupyter-notebook maken](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Een nieuwe Jupyter-notebook maken")

8. U gaat nu `import tensorflow` en een Hello World-voor beeld uitvoeren. Voer de volgende code in:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Het resultaat ziet er als volgt uit:
    
    ![Tensor flow code-uitvoering](./media/apache-spark-python-package-installation/tensorflow-execution.png "Tensor flow-code uitvoeren")

> [!NOTE]  
> Er zijn twee python-installaties in het cluster. Spark maakt gebruik van de Anaconda python-installatie die zich bevindt op `/usr/bin/anaconda/bin` en wordt standaard ingesteld op de python 2,7-omgeving. Als u python 3. x wilt gebruiken en pakketten in de PySpark3-kernel wilt installeren, gebruikt u het pad naar het uitvoer bare `conda` voor die omgeving en gebruikt u de para meter `-n` om de omgeving op te geven. De opdracht `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`, installeert het `ggplot` pakket bijvoorbeeld in de python 3,5-omgeving met behulp van het `conda-forge` kanaal.

## <a name="seealso"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Externe pakketten met Jupyter-notebooks gebruiken in Apache Spark clusters in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
