---
title: Fouten opsporen Apache Spark taken die worden uitgevoerd op Azure HDInsight
description: De GARENs-UI, Spark-gebruikers interface en Spark-geschiedenis server gebruiken voor het bijhouden en opsporen van fouten in taken die worden uitgevoerd op een Spark-cluster in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 0e80aa44652efbc58f8259944058aabe59ca5d1a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338465"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Fouten opsporen Apache Spark taken die worden uitgevoerd op Azure HDInsight

In dit artikel leert u hoe u [Apache Spark](https://spark.apache.org/) taken die worden uitgevoerd op HDInsight-clusters kunt volgen en opsporen met behulp van de [Apache Hadoop garen](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) -UI, Spark-gebruikers interface en de Spark-geschiedenis server. U start een Spark-taak met behulp van een notitie blok dat **beschikbaar is in het Spark-cluster, machine learning: Voorspellende analyse van gegevens over levensmiddelen inspecties**met behulp van MLLib. U kunt de volgende stappen gebruiken om een toepassing bij te houden die u hebt verzonden met behulp van een andere methode, bijvoorbeeld **Spark-verzen**ding.

## <a name="prerequisites"></a>Vereisten

U moet het volgende hebben:

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* U moet het notitie blok hebben gestart,  **[machine learning: Voorspellende analyse van gegevens over levensmiddelen inspecties](apache-spark-machine-learning-mllib-ipython.md)met behulp van MLLib**. Volg de koppeling voor instructies over het uitvoeren van dit notitie blok.  

## <a name="track-an-application-in-the-yarn-ui"></a>Een toepassing volgen in de garen-gebruikers interface

1. Start de garen-gebruikers interface. Klik op **garens** onder **cluster dashboards**.

    ![Azure Portal GARENs-UI starten](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > U kunt ook de gebruikers interface van garen starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, klikt u op **Ambari start** onder **cluster dashboards**. Klik in de Ambari-gebruikers interface op **garens**, klik op **snelle koppelingen**, klik op actieve Resource Manager en klik vervolgens op **Resource Manager-gebruikers interface**.

2. Omdat u de Spark-taak met Jupyter-notebooks hebt gestart, heeft de toepassing de naam **remotesparkmagics** (dit is de naam voor alle toepassingen die vanuit de notitie blokken worden gestart). Klik op de toepassings-ID voor de toepassings naam om meer informatie over de taak weer te geven. Hiermee opent u de weer gave van de toepassing.

    ![Spark-geschiedenis server zoeken naar Spark-toepassings-ID](./media/apache-spark-job-debugging/find-application-id1.png)

    Voor dergelijke toepassingen die worden gestart vanuit de Jupyter-notebooks, wordt de status altijd **uitgevoerd** totdat u het notitie blok sluit.
3. Vanuit de toepassings weergave kunt u verder inzoomen op de containers die zijn gekoppeld aan de toepassing en de logboeken (stdout/stderr). U kunt ook de Spark-gebruikers interface starten door te klikken op de koppeling die overeenkomt met de **tracerings-URL**, zoals hieronder wordt weer gegeven.

    ![Container logboeken van de Spark-geschiedenis server downloaden](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Een toepassing volgen in de Spark-gebruikers interface

In de Spark-gebruikers interface kunt u inzoomen op de Spark-taken die worden uitgevoerd door de toepassing die u eerder hebt gestart.

1. Als u de Spark-gebruikers interface wilt starten, klikt u in de toepassings weergave op de koppeling met de **tracerings-URL**, zoals wordt weer gegeven in de bovenstaande scherm opname. U kunt alle Spark-taken zien die worden gestart door de toepassing die wordt uitgevoerd in de Jupyter-notebook.

    ![Het tabblad Server taken van Spark-geschiedenis](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Klik op het tabblad **uitvoerende uitvoeringen** om de verwerkings-en opslag informatie voor elke uitvoerder te bekijken. U kunt de aanroep stack ook ophalen door te klikken op de koppeling **thread dump** .

    ![Tabblad voor het uitvoeren van de Spark-geschiedenis server](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Klik op het tabblad **fasen** om de fasen te zien die zijn gekoppeld aan de toepassing.

    ![Tabblad in Spark-geschiedenis server stadia](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-fasen weer geven")

    Elke fase kan meerdere taken bevatten waarvoor u uitvoerings statistieken kunt bekijken, zoals hieronder wordt weer gegeven.

    ![Details van het tabblad Server stadia Spark-geschiedenis](./media/apache-spark-job-debugging/view-spark-stages-details.png "weer geven details van Spark-fasen")

4. Op de pagina Details van fase kunt u DAG visualisatie starten. Vouw de koppeling **dag visualisatie** boven aan de pagina uit, zoals hieronder wordt weer gegeven.

    ![Visualisatie van Spark-fasen DAG weer geven](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG of direct Aclyic Graph vertegenwoordigt de verschillende fasen in de toepassing. Elk blauw vak in de grafiek vertegenwoordigt een Spark-bewerking die vanuit de toepassing is aangeroepen.

5. Op de pagina Details van fase kunt u ook de weer gave toepassings tijdlijn starten. Vouw de koppeling voor de **tijdlijn gebeurtenis** toe boven aan de pagina, zoals hieronder wordt weer gegeven.

    ![Gebeurtenis tijdlijn van Spark-fasen weer geven](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Hiermee worden de Spark-gebeurtenissen in de vorm van een tijd lijn weer gegeven. De tijdlijn weergave is beschikbaar op drie niveaus, in taken, binnen een taak en binnen een fase. In de bovenstaande afbeelding wordt de tijdlijn weergave voor een bepaalde fase vastgelegd.

   > [!TIP]  
   > Als u het selectie vakje **zoomen inschakelen** inschakelt, kunt u naar links en rechts schuiven in de tijdlijn weergave.

6. Andere tabbladen in de Spark-gebruikers interface geven ook nuttige informatie over de Spark-instantie.

   * Tabblad opslag: als uw toepassing een Rdd's maakt, vindt u informatie over die op het tabblad opslag.
   * Tabblad omgeving: dit tabblad bevat een groot aantal nuttige informatie over uw Spark-exemplaar, zoals:
     * Scala-versie
     * De map met gebeurtenis logboeken die aan het cluster zijn gekoppeld
     * Aantal uitvoer kernen voor de toepassing
     * Enz.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Informatie over voltooide taken zoeken met behulp van de Spark-geschiedenis server

Zodra een taak is voltooid, wordt de informatie over de taak opgeslagen in de Spark-geschiedenis server.

1. Als u de Spark-geschiedenis server wilt starten, klikt u op de Blade overzicht op **Spark-geschiedenis server** onder **cluster dashboards**.

    ![Azure Portal Spark-geschiedenis server starten](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark-geschiedenis starten server1")

   > [!TIP]  
   > U kunt ook de gebruikers interface van de Spark-geschiedenis server starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, klikt u op de Blade overzicht op **Ambari start** onder **cluster dashboards**. Klik in de Ambari-gebruikers interface op **Spark**, klik op **snelle koppelingen**en klik vervolgens op de **gebruikers interface van de Spark-geschiedenis server**.

2. U ziet alle voltooide toepassingen die worden weer gegeven. Klik op een toepassings-ID om in te zoomen op een toepassing voor meer informatie.

    ![Voltooide Spark-geschiedenis server toepassingen](./media/apache-spark-job-debugging/view-completed-applications.png "starten Spark-geschiedenis server2")

## <a name="see-also"></a>Zie ook

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Fouten opsporen Apache Spark taken met uitgebreide Spark-geschiedenis server](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Voor gegevens analisten

* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetriegegevens voor gegevens analyse met behulp van Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Caffe gebruiken in Azure HDInsight Spark voor gedistribueerde diep gaande lessen](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Voor Spark-ontwikkel aars

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
