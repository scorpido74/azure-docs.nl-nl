---
title: Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight
description: Gebruik YARN UI, Spark UI en Spark History-server om taken te volgen en te debuggen die worden uitgevoerd op een Spark-cluster in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932139"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight

In dit artikel leert u hoe u [Apache Spark-taken](https://spark.apache.org/) bijhouden en debuggen die worden uitgevoerd op HDInsight-clusters met behulp van de [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI, Spark UI en de Spark History Server. U start een Spark-taak met behulp van een notitieblok dat beschikbaar is met het Spark-cluster, **Machine learning: Voorspellende analyse van voedselinspectiegegevens met MLLib.** U de volgende stappen gebruiken om een toepassing die u hebt ingediend, bij te houden met een andere aanpak, bijvoorbeeld **spark-submit.**

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* U had moeten beginnen met het uitvoeren van de notebook, **[Machine learning: Predictive analysis on food inspection data using MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Volg de koppeling voor instructies over het uitvoeren van dit notitieblok.  

## <a name="track-an-application-in-the-yarn-ui"></a>Een toepassing bijhouden in de YARN UI

1. Start de YARN UI. Selecteer **Garen** onder **Clusterdashboards**.

    ![Azure-portal start YARN-gebruikersinterface](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > U ook de YARN UI van de Ambari UI lanceren. Als u de Ambari-gebruikersinterface wilt starten, selecteert u **Ambari-home** onder **clusterdashboards**. Navigeer vanuit de Gebruikersinterface van Ambari naar > **GAREN-snelkoppelingen** > de actieve resourcemanager > **Resource Manager-gebruikersinterface**. **YARN**

2. Omdat u de Spark-taak hebt gestart met Jupyter-notitieblokken, heeft de toepassing de naam **remotesparkmagics** (dit is de naam voor alle toepassingen die vanuit de notitieblokken zijn gestart). Selecteer de toepassings-id op basis van de naam van de toepassing om meer informatie over de taak te krijgen. Hiermee wordt de toepassingsweergave gestart.

    ![Spark-geschiedenisserver Spark-toepassings-id zoeken](./media/apache-spark-job-debugging/find-application-id1.png)

    Voor dergelijke toepassingen die worden gestart vanuit de Jupyter-notitieblokken, wordt de status altijd **uitgevoerd** totdat u het notitieblok verlaat.

3. Vanuit de toepassingsweergave u verder inzoomen om de containers te achterhalen die zijn gekoppeld aan de toepassing en de logboeken (stdout/stderr). U de Spark-gebruikersinterface ook starten door te klikken op de koppeling die overeenkomt met de **URL bijhouden**, zoals hieronder wordt weergegeven.

    ![Spark history server download container logs](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Een toepassing bijhouden in de Spark-gebruikersinterface

In de Spark-gebruikersinterface u inzoomen op de Spark-taken die worden voortgebracht door de toepassing die u eerder hebt gestart.

1. Als u de Spark-gebruikersinterface wilt starten, selecteert u in de toepassingsweergave de koppeling tegen de **URL bijhouden**, zoals weergegeven in de bovenstaande schermopname. U alle Spark-taken zien die worden gestart door de toepassing die wordt uitgevoerd in het Jupyter-notitieblok.

    ![Tabblad Vacatures spark-geschiedenisserver](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selecteer het tabblad **Executors** om verwerkings- en opslaggegevens voor elke uitvoerder te bekijken. U de oproepstack ook ophalen door de koppeling **Thread Dump te** selecteren.

    ![Tabblad Spark-geschiedenisserveruitvoerders](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selecteer het tabblad **Stadia** om de fasen te zien die aan de toepassing zijn gekoppeld.

    ![Tabblad Spark-geschiedenisserverfasen](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-fasen weergeven")

    Elke fase kan meerdere taken hebben waarvoor u uitvoeringsstatistieken bekijken, zoals hieronder wordt weergegeven.

    ![Tabbladgegevens van spark-geschiedenisserverfasen](./media/apache-spark-job-debugging/view-spark-stages-details.png "Details spark-fasen weergeven")

4. Vanaf de pagina fasedetails u DAG Visualisatie starten. Vouw de link **DAG Visualisatie** boven aan de pagina uit, zoals hieronder wordt weergegeven.

    ![Vonkfasen DAG-visualisatie weergeven](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG of Direct Aclyic Graph vertegenwoordigt de verschillende fasen in de toepassing. Elk blauw vak in de grafiek vertegenwoordigt een Spark-bewerking die vanuit de toepassing wordt aangeroepen.

5. Vanaf de pagina fasedetails u ook de tijdlijnweergave van de toepassing starten. Vouw de koppeling **Gebeurtenistijdlijn** boven aan de pagina uit, zoals hieronder wordt weergegeven.

    ![Tijdlijn spark-fasen-gebeurtenis weergeven](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Hiermee worden de Spark-gebeurtenissen weergegeven in de vorm van een tijdlijn. De tijdlijnweergave is beschikbaar op drie niveaus, voor taken, binnen een taak en binnen een taak. De afbeelding hierboven legt de tijdlijnweergave voor een bepaalde fase vast.

   > [!TIP]  
   > Als u het selectievakje **Zoomen inschakelen** inschakelt, u naar links en rechts over de tijdlijnweergave scrollen.

6. Andere tabbladen in de Spark-gebruikersinterface bieden ook nuttige informatie over de Spark-instantie.

   * Tabblad Opslag - Als uw toepassing een RDD maakt, u informatie vinden over deze gegevens op het tabblad Opslag.
   * Tabblad Omgeving - Op het tabblad vindt u nuttige informatie over uw Spark-instantie, zoals:
     * Scala-versie
     * Gebeurtenislogboekmap die is gekoppeld aan het cluster
     * Aantal executorkernen voor de toepassing
     * Enz.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Informatie over voltooide taken vinden met de Spark History Server

Zodra een taak is voltooid, blijft de informatie over de taak bestaan in de Spark History Server.

1. Als u de Spark History Server wilt starten, selecteert u op de pagina **Overzicht** de **Spark-geschiedenisserver** onder **Clusterdashboards**.

    ![Spark-geschiedenisserver voor Azure-portal starten](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark History Server starten1")

   > [!TIP]  
   > U ook de Spark History Server-gebruikersinterface starten vanaf de Ambari-gebruikersinterface. Als u de Ambari-gebruikersinterface wilt starten, selecteert u in het overzichtsblad **Ambari-home** onder **Clusterdashboards**. Navigeer vanuit de Ambari-gebruikersinterface naar de spark2-gebruikersinterface voor**spark2-koppelingen** >  **Spark2** > **spark2-geschiedenisserver**.

2. U ziet alle voltooide toepassingen vermeld. Selecteer een toepassings-id om in te zoomen op een toepassing voor meer informatie.

    ![Voltooide toepassingen spark-geschiedenisserver](./media/apache-spark-job-debugging/view-completed-applications.png "Spark History Server 2 starten")

## <a name="see-also"></a>Zie ook

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Foutopsporing Apache Spark-taken met uitgebreide Spark History Server](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Voor data-analisten

* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetrie data-analyse met Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Voor Spark-ontwikkelaars

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
