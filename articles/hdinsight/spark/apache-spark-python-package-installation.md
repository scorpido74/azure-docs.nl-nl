---
title: Scriptactie voor Python-pakketten met Jupyter op Azure HDInsight
description: Stapsgewijze instructies voor het gebruik van scriptactie om Jupyter-laptops te configureren die beschikbaar zijn met HDInsight Spark-clusters om externe python-pakketten te gebruiken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 659af8b85cb3736d663e79676b04af8041aeabfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129598"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Een Python-omgeving veilig beheren in Azure HDInsight met scriptactie

> [!div class="op_single_selector"]
> * [Celmagie gebruiken](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Scriptactie gebruiken](apache-spark-python-package-installation.md)

HDInsight heeft twee ingebouwde Python-installaties in het Spark-cluster, Anaconda Python 2.7 en Python 3.5. In sommige gevallen moeten klanten de Python-omgeving aanpassen, zoals het installeren van externe Python-pakketten of een andere Python-versie. In dit artikel tonen we de beste praktijken van het veilig beheren van Python-omgevingen voor een [Apache Spark-cluster](./apache-spark-overview.md) op HDInsight.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies. Als u nog geen Spark-cluster op HDInsight hebt, u scriptacties uitvoeren tijdens het maken van een cluster. Ga naar de documentatie over [het gebruik van aangepaste scriptacties.](../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Ondersteuning voor open-source software die wordt gebruikt op HDInsight-clusters

De Microsoft Azure HDInsight-service maakt gebruik van een ecosysteem van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteuningsniveau voor open-sourcetechnologieën. Zie [De veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq/)voor meer informatie. De HDInsight-service biedt een extra ondersteuningsniveau voor ingebouwde componenten.

Er zijn twee soorten opensourcecomponenten die beschikbaar zijn in de HDInsight-service:

|Onderdeel |Beschrijving |
|---|---|
|Ingebouwd|Deze componenten zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kernfunctionaliteit van het cluster. Apache Hadoop YARN Resource Manager, de Apache Hive-querytaal (HiveQL) en de Mahout-bibliotheek behoren bijvoorbeeld tot deze categorie. Een volledige lijst met clustercomponenten is beschikbaar in [Wat nieuw is in de Apache Hadoop-clusterversies van HDInsight.](../hdinsight-component-versioning.md)|
|Aangepast telefoonnummer|U, als gebruiker van het cluster, elk onderdeel dat beschikbaar is in de community of door u is gemaakt, in uw werkbelasting installeren of gebruiken.|

> [!IMPORTANT]
> Onderdelen die bij het HDInsight-cluster worden geleverd, worden volledig ondersteund. Microsoft Support helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste componenten ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft-ondersteuning kan het probleem mogelijk oplossen OF ze kunnen u vragen om beschikbare kanalen in te schakelen voor de open source-technologieën waar diepgaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel communitysites die kunnen worden gebruikt, zoals: [https://stackoverflow.com](https://stackoverflow.com) [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), . Ook Apache projecten hebben [https://apache.org](https://apache.org)projectsites op , bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Standaardpython-installatie begrijpen

HDInsight Spark cluster is gemaakt met Anaconda installatie. Er zijn twee Python-installaties in het cluster, Anaconda Python 2.7 en Python 3.5. De onderstaande tabel toont de standaard Python-instellingen voor Spark, Livy en Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|Pad|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Standaard ingesteld op 2,7|N.v.t.|
|Livy|Standaard ingesteld op 2,7|N.v.t.|
|Jupyter|PySpark-kernel|PySpark3-kernel|

## <a name="safely-install-external-python-packages"></a>Externe Python-pakketten veilig installeren

HdInsight-cluster is afhankelijk van de ingebouwde Python-omgeving, zowel Python 2.7 als Python 3.5. Het direct installeren van aangepaste pakketten in die standaard ingebouwde omgevingen kan onverwachte wijzigingen in de bibliotheekversie veroorzaken en het cluster verder breken. Volg de volgende stappen om op maat gemaakte externe Python-pakketten voor uw Spark-toepassingen veilig te installeren.

1. Maak de virtuele omgeving van Python met behulp van conda. Een virtuele omgeving biedt een geïsoleerde ruimte voor uw projecten zonder anderen te breken. Bij het maken van de virtuele Python-omgeving u de python-versie opgeven die u wilt gebruiken. Houd er rekening mee dat u nog steeds een virtuele omgeving moet maken, ook al wilt u Python 2.7 en 3.5 gebruiken. Dit is om ervoor te zorgen dat de standaardomgeving van het cluster niet wordt verbroken. Voer scriptacties uit op uw cluster voor alle knooppunten met onderstaand script om een virtuele Python-omgeving te maken.

    -   `--prefix`hiermee wordt een pad opgegeven waar een virtuele conda-omgeving leeft. Er zijn verschillende configs die verder moeten worden gewijzigd op basis van het pad dat hier is opgegeven. In dit voorbeeld gebruiken we de py35new, omdat het cluster al een bestaande virtuele omgeving heeft die py35 heet.
    -   `python=`hiermee wordt de Python-versie voor de virtuele omgeving opgegeven. In dit voorbeeld gebruiken we versie 3.5, dezelfde versie als het cluster dat in één is ingebouwd. U ook andere Python-versies gebruiken om de virtuele omgeving te maken.
    -   `anaconda`geeft de package_spec als anaconda om Anaconda-pakketten in de virtuele omgeving te installeren.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installeer indien nodig externe Python-pakketten in de gemaakte virtuele omgeving. Voer scriptacties uit op uw cluster voor alle knooppunten met onderstaand script om externe Python-pakketten te installeren. Je moet sudo privilege hier hebben om bestanden te schrijven naar de virtuele omgeving map.

    U zoeken in de [pakketindex](https://pypi.python.org/pypi) voor de volledige lijst met pakketten die beschikbaar zijn. U ook een lijst met beschikbare pakketten uit andere bronnen krijgen. U bijvoorbeeld pakketten installeren die beschikbaar worden gesteld via [conda-forge.](https://conda-forge.org/feedstocks/)

    Gebruik onderstaande opdracht als u een bibliotheek met de nieuwste versie wilt installeren:

    - Gebruik conda-kanaal:

        -   `seaborn`is de pakketnaam die u wilt installeren.
        -   `-n py35new`de naam van de virtuele omgeving opgeven die net wordt gemaakt. Zorg ervoor dat u de naam dienovereenkomstig wijzigt op basis van het maken van uw virtuele omgeving.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Of gebruik PyPi repo, wijzigen `seaborn` en `py35new` overeenkomstig:
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    Gebruik onderstaande opdracht als u een bibliotheek met een specifieke versie wilt installeren:

    - Gebruik conda-kanaal:

        -   `numpy=1.16.1`is de naam en versie van het pakket die u wilt installeren.
        -   `-n py35new`de naam van de virtuele omgeving opgeven die net wordt gemaakt. Zorg ervoor dat u de naam dienovereenkomstig wijzigt op basis van het maken van uw virtuele omgeving.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Of gebruik PyPi repo, wijzigen `numpy==1.16.1` en `py35new` overeenkomstig:

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    Als u de naam van de virtuele omgeving niet kent, u `/usr/bin/anaconda/bin/conda info -e` SSH naar het hoofdknooppunt van het cluster leiden en uitvoeren om alle virtuele omgevingen weer te geven.

3. Verander Spark en Livy configs en wijs naar de gecreëerde virtuele omgeving.

    1. Open Ambari UI, ga naar Spark2-pagina, tabblad Configs.

        ![Verandering Spark en Livy config via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Vouw Geavanceerde livy2-env uit en voeg hieronder onderstaande instructies toe onderaan. Als u de virtuele omgeving met een ander voorvoegsel hebt geïnstalleerd, wijzigt u het pad overeenkomstig.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Verandering Livy config via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Breid Advanced spark2-env uit en vervang de bestaande exportPYSPARK_PYTHON-instructie onderaan. Als u de virtuele omgeving met een ander voorvoegsel hebt geïnstalleerd, wijzigt u het pad overeenkomstig.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Verander Spark config via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Sla de wijzigingen op en start de getroffen services opnieuw op. Deze wijzigingen moeten opnieuw worden gestart met de Spark2-service. Ambari UI vraagt een vereiste herinnering voor het opnieuw starten, klik op Opnieuw starten om alle betrokken services opnieuw te starten.

        ![Verander Spark config via Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Als u de nieuwe gecreëerde virtuele omgeving op Jupyter wilt gebruiken. Je moet Jupyter configs veranderen en Jupyter opnieuw opstarten. Voer scriptacties uit op alle kopknooppunten met onderstaande instructie om Jupyter naar de nieuwe virtuele omgeving te wijzen. Zorg ervoor dat u het pad wijzigt naar het voorvoegsel dat u hebt opgegeven voor uw virtuele omgeving. Nadat u deze scriptactie hebt uitgevoerd, start u de Jupyter-service opnieuw op via de Ambari-gebruikersinterface om deze wijziging beschikbaar te maken.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    U de Python-omgeving in Jupyter Notebook dubbel bevestigen door onderstaande code uit te voeren:

    ![Python-versie controleren in Jupyter-notitieblok](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Bekend probleem

Er is een bekende bug voor Anaconda versie 4.7.11, 4.7.12 en 4.8.0. Als u ziet dat `"Collecting package metadata (repodata.json): ...working..."` uw script `"Python script has been killed due to timeout after waiting 3600 secs"`acties opknoping op en niet met . U [dit script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) downloaden en uitvoeren als scriptacties op alle knooppunten om het probleem op te lossen.

Als u uw Anaconda-versie wilt controleren, u `/usr/bin/anaconda/bin/conda --v`SSH naar het clusterheaderknooppunt en uitvoeren.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
