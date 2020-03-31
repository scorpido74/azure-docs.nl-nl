---
title: Resources beheren voor Apache Spark-cluster op Azure HDInsight
description: Meer informatie over het beheren van resources voor Spark-clusters op Azure HDInsight voor betere prestaties.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932094"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Resources beheren voor Apache Spark-cluster op Azure HDInsight

Meer informatie over hoe u toegang krijgt tot de interfaces zoals [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI en de Spark History [Server](./apache-azure-spark-history-server.md) die is gekoppeld aan uw [Apache Spark-cluster](https://spark.apache.org/) en hoe u de clusterconfiguratie afstemmen op optimale prestaties.

## <a name="open-the-spark-history-server"></a>De Spark History Server openen

Spark History Server is de web-gebruikersinterface voor voltooide en draaiende Spark-toepassingen. Het is een uitbreiding van Spark's Web UI. Zie [Spark History Server](./apache-azure-spark-history-server.md)voor volledige informatie.

## <a name="open-the-yarn-ui"></a>Open de garengebruikersinterface

U de YARN-gebruikersinterface gebruiken om toepassingen te controleren die momenteel op het Spark-cluster worden uitgevoerd.

1. Open het Spark-cluster vanuit de [Azure-portal.](https://portal.azure.com/) Zie [Clusters weergeven en weergeven](../hdinsight-administer-use-portal-linux.md#showClusters)voor meer informatie.

2. Selecteer **Garen**in **clusterdashboards**. Voer de beheerdersreferenties voor het Spark-cluster in wanneer u daarom wordt gevraagd.

    ![Garengebruikers lanceren](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > U ook de YARN UI van de Ambari UI lanceren. Navigeer vanuit de Ambari-gebruikersinterface naar de**gebruikersinterface** **van YARN** > **Quick Links** > **Active** > Resource Manager .

## <a name="optimize-clusters-for-spark-applications"></a>Clusters optimaliseren voor Spark-toepassingen

De drie belangrijkste parameters die kunnen worden gebruikt voor `spark.executor.instances` `spark.executor.cores`spark-configuratie, afhankelijk van de toepassingsvereisten, zijn , en `spark.executor.memory`. Een executor is een proces dat is gestart voor een Spark-toepassing. Het draait op het werkknooppunt en is verantwoordelijk voor het uitvoeren van de taken voor de toepassing. Het standaardaantal uitvoerders en de grootte van de executeur voor elk cluster wordt berekend op basis van het aantal werknemersknooppunten en de grootte van het werknemersknooppunt. Deze informatie wordt `spark-defaults.conf` opgeslagen in de clusterkopknooppunten.

De drie configuratieparameters kunnen worden geconfigureerd op clusterniveau (voor alle toepassingen die op het cluster worden uitgevoerd) of kunnen ook voor elke afzonderlijke toepassing worden opgegeven.

### <a name="change-the-parameters-using-ambari-ui"></a>De parameters wijzigen met ambari-gebruikersinterface

1. Navigeer vanuit de Ambari-gebruikersinterface naar **Spark2** > **Configs** > **Custom spark2-defaults**.

    ![Parameters instellen met Ambari aangepast](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Parameters instellen met Ambari aangepast")

1. De standaardwaarden zijn goed om vier Spark-toepassingen gelijktijdig op het cluster te laten uitvoeren. U deze waarden wijzigen vanuit de gebruikersinterface, zoals in de volgende schermafbeelding wordt weergegeven:

    ![Parameters instellen met Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Parameters instellen met Ambari")

1. Selecteer **Opslaan** om de configuratiewijzigingen op te slaan. Boven aan de pagina wordt u gevraagd om alle betrokken services opnieuw te starten. Selecteer **Opnieuw starten**.

    ![Services opnieuw opstarten](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>De parameters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter-notitieblok

Voor toepassingen die in de Jupyter-notebook worden uitgevoerd, u de `%%configure` magie gebruiken om de configuratiewijzigingen aan te brengen. Idealiter moet u dergelijke wijzigingen aan het begin van de toepassing aanbrengen, voordat u uw eerste codecel uitvoert. Dit zorgt ervoor dat de configuratie wordt toegepast op de Livy-sessie, wanneer deze wordt gemaakt. Als u de configuratie in een later stadium van de `-f` toepassing wilt wijzigen, moet u de parameter gebruiken. Echter, door dit te doen alle vooruitgang in de toepassing gaat verloren.

In het volgende fragment ziet u hoe u de configuratie voor een toepassing die in Jupyter wordt uitgevoerd, wijzigen.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Configuratieparameters moeten worden doorgegeven als json-tekenreeks en moeten na de magie op de volgende regel staan, zoals in de voorbeeldkolom wordt weergegeven.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>De parameters voor een aanvraag wijzigen die met spark-submit wordt ingediend

Volgende opdracht is een voorbeeld van het wijzigen van de `spark-submit`configuratieparameters voor een batchtoepassing die wordt ingediend met behulp van .

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>De parameters voor een toepassing wijzigen die met cURL wordt ingediend

De volgende opdracht is een voorbeeld van het wijzigen van de configuratieparameters voor een batchtoepassing die wordt ingediend met behulp van cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Deze parameters wijzigen op een Spark Thrift Server

Spark Thrift Server biedt JDBC/ODBC toegang tot een Spark-cluster en wordt gebruikt om Spark SQL-query's te onderhouden. Tools zoals Power BI, Tableau, enzovoort, gebruiken odbc-protocol om te communiceren met Spark Thrift Server om Spark SQL-query's uit te voeren als een Spark-toepassing. Wanneer een Spark-cluster wordt gemaakt, worden twee exemplaren van de Spark Thrift Server gestart, één op elk hoofdknooppunt. Elke Spark Thrift Server is zichtbaar als een Spark-toepassing in de YARN-gebruikersinterface.

Spark Thrift Server maakt gebruik van `spark.executor.instances` Spark dynamische executor toewijzing en dus het is niet gebruikt. In plaats daarvan `spark.dynamicAllocation.maxExecutors` gebruikt `spark.dynamicAllocation.minExecutors` en geeft Spark Thrift Server het aantal uitvoerders aan. De configuratieparameters `spark.executor.cores` `spark.executor.memory` en worden gebruikt om de grootte van de executeur te wijzigen. U deze parameters wijzigen zoals weergegeven in de volgende stappen:

* Breid de categorie **Advanced spark2-thrift-sparkconf** uit om de parameters `spark.dynamicAllocation.maxExecutors`bij te werken en `spark.dynamicAllocation.minExecutors`.

    ![Spark-spaarserver configureren](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Spark-spaarserver configureren")

* Vouw de categorie **Aangepaste spark2-thrift-sparkconf** uit om de parameters `spark.executor.cores`bij te werken en `spark.executor.memory`.

    ![Parameter Spark-spaarserver configureren](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Parameter Spark-spaarserver configureren")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Het stuurprogrammageheugen van de Spark Thrift Server wijzigen

Spark Thrift Server-stuurprogrammageheugen is geconfigureerd tot 25% van de RAM-grootte van het hoofdknooppunt, op voorwaarde dat de totale RAM-grootte van het hoofdknooppunt groter is dan 14 GB. U de Ambari-gebruikersinterface gebruiken om de configuratie van het geheugen van het stuurprogramma te wijzigen, zoals in de volgende schermafbeelding wordt weergegeven:

Navigeer vanaf de Ambari-gebruikersinterface naar **Spark2** > **Configs** > **Advanced spark2-env.** Geef dan de waarde op voor **spark_thrift_cmd_opts.**

## <a name="reclaim-spark-cluster-resources"></a>Spark-clusterbronnen terugwinnen

Vanwege de dynamische toewijzing van Spark zijn de resources die worden verbruikt door de thrift server de bronnen voor de twee toepassingsmodellen. Als u deze resources wilt terugvorderen, moet u de Thrift Server-services die op het cluster worden uitgevoerd, stoppen.

1. Selecteer **Spark2**in de gebruikersinterface van Ambari in het linkerdeelvenster .

2. Selecteer **spark2-zuinigheidsservers op**de volgende pagina .

    ![Zuinige server opnieuw opstarten1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Zuinige server opnieuw opstarten1")

3. U ziet de twee hoofdnoden waarop de Spark2 Thrift Server wordt uitgevoerd. Selecteer een van de hoofdnoden.

    ![Zuinige server opnieuw opstarten2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Zuinige server opnieuw opstarten2")

4. De volgende pagina bevat alle services die op die headnode draaien. Selecteer in de lijst de vervolgkeuzeknop naast Spark2 Thrift Server en selecteer **Stoppen**.

    ![Zuinige server opnieuw opstarten3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Zuinige server opnieuw opstarten3")
5. Herhaal deze stappen op de andere headnode ook.

## <a name="restart-the-jupyter-service"></a>De Jupyter-service opnieuw starten

Start de Ambari Web UI zoals weergegeven in het begin van het artikel. Selecteer in het linkernavigatiedeelvenster **Jupyter,** selecteer **Serviceacties**en selecteer **Alles opnieuw starten**. Dit begint de Jupyter service op alle hoofdnoden.

![Jupyter opnieuw starten](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter opnieuw starten")

## <a name="monitor-resources"></a>Resources controleren

Start de Garen gebruikersinterface zoals getoond in het begin van het artikel. Controleer in de tabel Clusterstatistieken boven aan het scherm de waarden van **geheugengebruikt** en **geheugentotaalkolommen.** Als de twee waarden dichtbij zijn, zijn er mogelijk niet genoeg resources om de volgende toepassing te starten. Hetzelfde geldt voor de kolommen **VCores Used** en **VCores Totaal.** Ook, in de hoofdweergave, als er een toepassing is gebleven in **geaccepteerde** status en niet overschakelen naar **lopende** of **mislukte** staat, kan dit ook een indicatie zijn dat het niet genoeg resources krijgt om te beginnen.

![Resourcelimiet](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Resourcelimiet")

## <a name="kill-running-applications"></a>Kill running applicaties

1. Selecteer In de gebruikersinterface garen in het linkerdeelvenster de optie **Uitvoeren**. Bepaal in de lijst met lopende toepassingen de te doden toepassing en selecteer de **ID.**

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Selecteer **Kill-toepassing** in de rechterbovenhoek en selecteer **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>Zie ook

* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Voor data-analisten

* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetrie data-analyse met Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Voor Apache Spark-ontwikkelaars

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
