---
title: Script actie voor python-pakketten met Jupyter in azure HDInsight
description: Stapsgewijze instructies voor het gebruik van script actie voor het configureren van Jupyter-notebooks die beschikbaar zijn met HDInsight Spark-clusters voor het gebruik van externe Python-pakketten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6342e6a75c8397712e028874b4d727bf3d6f5ff4
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087120"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Een Python-omgeving veilig beheren in Azure HDInsight met scriptactie

> [!div class="op_single_selector"]
> * [Cell Magic gebruiken](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Script actie gebruiken](apache-spark-python-package-installation.md)

HDInsight heeft twee ingebouwde python-installaties in het Spark-cluster, Anaconda python 2,7 en python 3,5. In sommige gevallen moeten klanten de python-omgeving aanpassen, zoals het installeren van externe Python-pakketten of een andere python-versie. In dit artikel laten we de best practice zien van het veilig beheren van python-omgevingen voor een [Apache Spark](https://spark.apache.org/) cluster op HDInsight.

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

## <a name="understand-default-python-installation"></a>Informatie over de standaard installatie van python

HDInsight Spark-cluster wordt gemaakt met Anaconda-installatie. Er zijn twee python-installaties in het cluster, Anaconda python 2,7 en python 3,5. In de volgende tabel ziet u de standaard python-instellingen voor Spark, livy en Jupyter.

| |Python 2,7|Python 3,5|
|----|----|----|
|Pad|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Standaard ingesteld op 2,7|N.v.t.|
|Livy|Standaard ingesteld op 2,7|N.v.t.|
|Jupyter|PySpark-kernel|PySpark3-kernel|

## <a name="safely-install-external-python-packages"></a>Externe Python-pakketten veilig installeren

HDInsight-cluster is afhankelijk van de ingebouwde python-omgeving, zowel python 2,7 als python 3,5. Rechtstreeks installeren van aangepaste pakketten in deze ingebouwde omgevingen kan leiden tot onverwachte wijzigingen in de bibliotheek versie en het cluster verder verstoren. Volg de onderstaande stappen om veilig aangepaste externe Python-pakketten voor uw Spark-toepassingen te installeren.

1. Maak een virtuele python-omgeving met behulp van Conda. Een virtuele omgeving biedt een geïsoleerde ruimte voor uw projecten zonder dat anderen dat doen. Bij het maken van de virtuele python-omgeving kunt u een python-versie opgeven die u wilt gebruiken. U moet nog steeds een virtuele omgeving maken, zelfs als u python 2,7 en 3,5 wilt gebruiken. Zo zorgt u ervoor dat de standaard omgeving van het cluster niet wordt verbroken. Voer script acties uit op uw cluster voor alle knoop punten met het onderstaande script om een virtuele python-omgeving te maken. 

    -   `--prefix` Hiermee geeft u een pad op waar de virtuele Conda-omgeving zich bevindt. Er zijn verschillende configuraties die verder moeten worden gewijzigd op basis van het pad dat hier wordt opgegeven. In dit voor beeld gebruiken we de py35new, omdat het cluster al een bestaande virtuele omgeving met de naam py35 bevat.
    -   `python=` geeft de python-versie op voor de virtuele omgeving. In dit voor beeld gebruiken we versie 3,5, dezelfde versie als het cluster dat in één is ingebouwd. U kunt ook andere python-versies gebruiken om de virtuele omgeving te maken.
    -   `anaconda` geeft u de package_spec op als Anaconda voor het installeren van Anaconda-pakketten in de virtuele omgeving.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Installeer indien nodig externe Python-pakketten in de gemaakte virtuele omgeving. Voer script acties uit op uw cluster voor alle knoop punten met het onderstaande script om externe Python-pakketten te installeren. U moet hier sudo-bevoegdheden hebben om bestanden te kunnen schrijven naar de map virtuele omgeving.

    U kunt de [pakket index](https://pypi.python.org/pypi) doorzoeken voor de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. U kunt bijvoorbeeld pakketten installeren die beschikbaar worden gesteld via [Conda-vervalsing](https://conda-forge.org/feedstocks/).

    -   `seaborn` is de naam van het pakket dat u wilt installeren.
    -   `-n py35new` geeft u de naam van de virtuele omgeving op die zojuist wordt gemaakt. Zorg ervoor dat u de naam wijzigt die overeenkomt met het maken van uw virtuele omgeving.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    Als u de naam van de virtuele omgeving niet weet, kunt u SSHen naar het hoofd knooppunt van het cluster en `/usr/bin/anaconda/bin/conda info -e` uitvoeren om alle virtuele omgevingen weer te geven.

3. Wijzig Spark-en livy-configuraties en ga naar de gemaakte virtuele omgeving.

    1. Open Ambari-gebruikers interface, ga naar Spark2-pagina, tabblad Configuratie.
    
        ![Spark-en livy-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Vouw Geavanceerd livy2-env uit en voeg onderaan de instructies toe. Als u de virtuele omgeving met een ander voor voegsel hebt geïnstalleerd, wijzigt u het pad dienovereenkomstig.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Livy-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Vouw Advanced spark2-env uit en vervang de bestaande instructie export PYSPARK_PYTHON onderaan. Als u de virtuele omgeving met een ander voor voegsel hebt geïnstalleerd, wijzigt u het pad dienovereenkomstig.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Spark-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Sla de wijzigingen op en start de betrokken services opnieuw. Voor deze wijzigingen moet de Spark2-service opnieuw worden gestart. De Ambari-gebruikers interface vraagt een vereiste herinnering opnieuw op te starten. Klik op opnieuw opstarten om alle betrokken services opnieuw te starten.

        ![Spark-configuratie wijzigen via Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  Als u de nieuwe virtuele omgeving die u hebt gemaakt, wilt gebruiken op Jupyter. U moet Jupyter-configuraties wijzigen en Jupyter opnieuw starten. Voer script acties uit op alle hoofd knooppunten met de instructie hieronder om Jupyter naar de nieuwe virtuele omgeving te wijzen. Zorg ervoor dat u het pad naar het voor voegsel dat u hebt opgegeven voor uw virtuele omgeving wijzigt. Nadat u deze script actie hebt uitgevoerd, start u de Jupyter-service opnieuw via de Ambari-gebruikers interface om deze wijziging beschikbaar te maken.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    U kunt de python-omgeving in Jupyter Notebook dubbel bevestigen door de volgende code uit te voeren:

    ![Python-versie controleren in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Bekend probleem

Er is een bekende fout voor Anaconda-versie 4.7.11, 4.7.12 en 4.8.0. Als uw script acties zijn vastgelopen op `"Collecting package metadata (repodata.json): ...working..."` en er een fout optreedt bij het `"Python script has been killed due to timeout after waiting 3600 secs"`. U kunt [Dit script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) downloaden en uitvoeren als script acties op alle knoop punten om het probleem op te lossen.

Als u uw Anaconda-versie wilt controleren, kunt u SSHen naar het cluster header-knoop punt en `/usr/bin/anaconda/bin/conda --v`uitvoeren.

## <a name="seealso"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario 's

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
