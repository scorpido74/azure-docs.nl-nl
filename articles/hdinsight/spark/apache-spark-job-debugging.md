---
title: Fouten opsporen Apache Spark taken die worden uitgevoerd op Azure HDInsight
description: De GARENs-UI, Spark-gebruikers interface en Spark-geschiedenis server gebruiken voor het bijhouden en opsporen van fouten in taken die worden uitgevoerd op een Spark-cluster in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932139"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Fouten opsporen Apache Spark taken die worden uitgevoerd op Azure HDInsight

In dit artikel leert u hoe u [Apache Spark](https://spark.apache.org/) taken die worden uitgevoerd op HDInsight-clusters kunt volgen en opsporen met behulp van de [Apache Hadoop garen](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) -UI, Spark-gebruikers interface en de Spark-geschiedenis server. U start een Spark-taak met behulp van een notitie blok dat beschikbaar is in het Spark-cluster, **machine learning: voorspellende analyse van gegevens van levensmiddelen inspecties met behulp van MLLib**. U kunt de volgende stappen gebruiken om een toepassing bij te houden die u hebt verzonden met behulp van een andere methode, bijvoorbeeld **Spark-verzen**ding.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* U moet het notitie blok, **[machine learning: voorspellende analyse van gegevens van de levensmiddelen inspectie starten met behulp van MLLib](apache-spark-machine-learning-mllib-ipython.md)** . Volg de koppeling voor instructies over het uitvoeren van dit notitie blok.  

## <a name="track-an-application-in-the-yarn-ui"></a>Een toepassing volgen in de garen-gebruikers interface

1. Start de garen-gebruikers interface. Selecteer **garens** onder **cluster dashboards**.

    ![Azure Portal GARENs-UI starten](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > U kunt ook de gebruikers interface van garen starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, selecteert u **Ambari Home** onder **cluster dashboards**. Ga in de Ambari-gebruikers interface naar **garens** > **snelle koppelingen** > de actieve Resource Manager > **Resource Manager-gebruikers interface**.

2. Omdat u de Spark-taak met Jupyter-notebooks hebt gestart, heeft de toepassing de naam **remotesparkmagics** (dit is de naam voor alle toepassingen die vanuit de notitie blokken worden gestart). Selecteer de toepassings-ID voor de toepassings naam om meer informatie over de taak weer te geven. Hiermee opent u de weer gave van de toepassing.

    ![Spark-geschiedenis server zoeken naar Spark-toepassings-ID](./media/apache-spark-job-debugging/find-application-id1.png)

    Voor dergelijke toepassingen die worden gestart vanuit de Jupyter-notebooks, wordt de status altijd **uitgevoerd** totdat u het notitie blok sluit.

3. Vanuit de toepassings weergave kunt u verder inzoomen op de containers die zijn gekoppeld aan de toepassing en de logboeken (stdout/stderr). U kunt ook de Spark-gebruikers interface starten door te klikken op de koppeling die overeenkomt met de **tracerings-URL**, zoals hieronder wordt weer gegeven.

    ![Container logboeken van de Spark-geschiedenis server downloaden](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Een toepassing volgen in de Spark-gebruikers interface

In de Spark-gebruikers interface kunt u inzoomen op de Spark-taken die worden uitgevoerd door de toepassing die u eerder hebt gestart.

1. Als u de Spark-gebruikers interface wilt starten, selecteert u in de toepassings weergave de koppeling met de **tracerings-URL**, zoals wordt weer gegeven in de bovenstaande scherm opname. U kunt alle Spark-taken zien die worden gestart door de toepassing die wordt uitgevoerd in de Jupyter-notebook.

    ![Het tabblad Server taken van Spark-geschiedenis](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selecteer het tabblad **uitvoerende uitvoeringen** om de verwerkings-en opslag informatie voor elke uitvoerder te bekijken. U kunt de aanroep stack ook ophalen door de **thread dump** koppeling te selecteren.

    ![Tabblad voor het uitvoeren van de Spark-geschiedenis server](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selecteer het tabblad **stadia** om de fasen te zien die zijn gekoppeld aan de toepassing.

    ![Het tabblad Server fasen van Spark-geschiedenis](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-fasen weer geven")

    Elke fase kan meerdere taken bevatten waarvoor u uitvoerings statistieken kunt bekijken, zoals hieronder wordt weer gegeven.

    ![Details van het tabblad Server fasen in Spark-geschiedenis](./media/apache-spark-job-debugging/view-spark-stages-details.png "Details van Spark-fasen weer geven")

4. Op de pagina Details van fase kunt u DAG visualisatie starten. Vouw de koppeling **dag visualisatie** boven aan de pagina uit, zoals hieronder wordt weer gegeven.

    ![Visualisatie van Spark-fasen DAG weer geven](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG of direct Aclyic Graph vertegenwoordigt de verschillende fasen in de toepassing. Elk blauw vak in de grafiek vertegenwoordigt een Spark-bewerking die vanuit de toepassing is aangeroepen.

5. Op de pagina Details van fase kunt u ook de weer gave toepassings tijdlijn starten. Vouw de koppeling voor de **tijdlijn gebeurtenis** toe boven aan de pagina, zoals hieronder wordt weer gegeven.

    ![Gebeurtenis tijdlijn van Spark-fasen weer geven](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Hiermee worden de Spark-gebeurtenissen in de vorm van een tijd lijn weer gegeven. De tijdlijn weergave is beschikbaar op drie niveaus, in taken, binnen een taak en binnen een fase. In de bovenstaande afbeelding wordt de tijdlijn weergave voor een bepaalde fase vastgelegd.

   > [!TIP]  
   > Als u het selectie vakje **zoomen inschakelen** inschakelt, kunt u naar links en rechts schuiven in de tijdlijn weergave.

6. Andere tabbladen in de Spark-gebruikers interface geven ook nuttige informatie over de Spark-instantie.

   * Tabblad opslag: als uw toepassing een RDD maakt, vindt u informatie over die op het tabblad opslag.
   * Tabblad omgeving: dit tabblad bevat nuttige informatie over uw Spark-exemplaar, zoals:
     * Scala-versie
     * De map met gebeurtenis logboeken die aan het cluster zijn gekoppeld
     * Aantal uitvoer kernen voor de toepassing
     * Enz.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Informatie over voltooide taken zoeken met behulp van de Spark-geschiedenis server

Zodra een taak is voltooid, wordt de informatie over de taak opgeslagen in de Spark-geschiedenis server.

1. Als u de Spark-geschiedenis server wilt starten, selecteert u op de pagina **overzicht** de optie **Spark-geschiedenis server** onder **cluster dashboards**.

    ![Azure Portal Spark-geschiedenis server starten](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark geschiedenis server1 starten")

   > [!TIP]  
   > U kunt ook de gebruikers interface van de Spark-geschiedenis server starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, selecteert u op de Blade overzicht de optie **Ambari Home** onder **cluster dashboards**. Ga in de Ambari-gebruikers interface naar **Spark2** > **snelle koppelingen** > **gebruikers interface van Spark2 geschiedenis server**.

2. U ziet alle voltooide toepassingen die worden weer gegeven. Selecteer een toepassings-ID om in te zoomen op een toepassing voor meer informatie.

    ![Voltooide Spark-geschiedenis server toepassingen](./media/apache-spark-job-debugging/view-completed-applications.png "Spark-geschiedenis server2 starten")

## <a name="see-also"></a>Zie ook

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Fouten opsporen Apache Spark taken met uitgebreide Spark-geschiedenis server](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Voor gegevens analisten

* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetriegegevens voor gegevens analyse met behulp van Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Voor Spark-ontwikkel aars

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
