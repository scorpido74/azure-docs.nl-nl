---
title: 'Azure-toolkit voor IntelliJ: Fout opsporing van Spark-toepassingen op afstand via SSH '
description: Stapsgewijze richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in Azure-toolkit voor IntelliJ voor het opsporen van fouten op externe toepassingen op HDInsight-clusters via SSH
keywords: fout opsporing op afstand IntelliJ, externe fout opsporing IntelliJ, SSH, IntelliJ, hdinsight, debug IntelliJ, fout opsporing
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 844901d9c689d5a04312b52101572854f4d7fe6b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994024"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Fouten opsporen Apache Spark toepassingen op een HDInsight-cluster met Azure-toolkit voor IntelliJ via SSH

In dit artikel vindt u stapsgewijze richt lijnen voor het gebruik van HDInsight-Hulpprogram Ma's in [Azure-Toolkit voor IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) voor het op afstand opsporen van toepassingen op een HDInsight-cluster. Als u fouten wilt opsporen in uw project, kunt u ook de [fout opsporing HDInsight Spark-toepassingen met Azure-Toolkit voor IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video bekijken.

**Vereisten**
* **HDInsight-Hulpprogram ma's in azure-Toolkit voor IntelliJ**. Dit hulp programma maakt deel uit van Azure-toolkit voor IntelliJ. Zie [Install Azure-Toolkit voor IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)(Engelstalig) voor meer informatie. En **Azure-Toolkit voor IntelliJ**. Gebruik deze Toolkit om Apache Spark-toepassingen voor een HDInsight-cluster te maken. Volg de instructies in [Use Azure-Toolkit voor IntelliJ om Apache Spark-toepassingen voor een HDInsight-cluster te maken](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)voor meer informatie.

* **HDINSIGHT SSH-service met gebruikers naam en wachtwoord beheer**. Zie voor meer informatie [verbinding maken met HDInsight (Apache Hadoop) met SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) en [ssh-tunneling gebruiken om toegang te krijgen tot Ambari Web UI, JobHistory, NameNode, Apache Oozie en andere web-UIs](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Meer informatie over het uitvoeren van lokale uitvoering en fout opsporing
### <a name="scenario-1-create-a-spark-scala-application"></a>Scenario 1: Een Spark scala-toepassing maken 

1. Start IntelliJ IDEA en maak een project. Voer in het dialoogvenster **Nieuw project** de volgende handelingen uit:

   a. Selecteer **Azure Spark/HDInsight**. 

   b. Selecteer een Java-of scala-sjabloon op basis van uw voor keur. Selecteer een van de volgende opties:

   - **Spark-project (Java)**

   - **Spark Project (Scala)**

   - **Spark-project met voor beelden (scala)**

   - **Spark-project met fout fout opsporing voor beelden van taken (preview) (scala)**

     In dit voor beeld wordt een **Spark-project met samples (scala)** -sjabloon gebruikt.

   c. Selecteer in de lijst **Build-programma** een van de volgende opties, afhankelijk van uw behoeften:

   - **Maven**, voor de ondersteuning van de wizard Scala-project maken

   - **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project 

     ![Een project voor fout opsporing maken](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecteer **Volgende**.     
 
1. Ga als volgt te werk in het volgende **nieuwe project** venster:

   ![De Spark-SDK selecteren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Voer een project naam en Project locatie in.

   b. Selecteer in de vervolg keuzelijst **Project SDK** **Java 1,8** voor **Spark 2. x** -cluster of selecteer **Java 1,7** voor **Spark 1. x** -cluster.

   c. In de vervolg keuzelijst **Spark-versie** integreert de wizard scala project maken de juiste versie voor de Spark SDK en scala SDK. Als de versie van het Spark-cluster ouder is dan 2,0, selecteert u **Spark 1. x**. Selecteer anders **Spark 2. x.** In dit voorbeeld wordt **Spark 2.0.2 (Scala 2.11.8)** gebruikt.

   d. Selecteer **Finish**.

1. Selecteer **src** > Mainscala > om de code in het project te openen. In dit voor beeld wordt het **SparkCore_wasbloTest** -script gebruikt.

### <a name="prerequisite-for-windows"></a>Vereiste voor Windows
Terwijl u de lokale Spark scala-toepassing uitvoert op een Windows-computer, kunt u een uitzonde ring krijgen, zoals wordt uitgelegd in [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzonde ring treedt op omdat WinUtils. exe ontbreekt in Windows. 

U kunt deze fout oplossen door [het uitvoer bare bestand te downloaden](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) naar een locatie zoals **C:\WinUtils\bin**. Voeg vervolgens de omgevings variabele **HADOOP_HOME**toe en stel de waarde van de variabele in op **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scenario 2: Lokale uitvoering uitvoeren
1. Open het script **SparkCore_wasbloTest** , klik met de rechter muisknop op de script editor en selecteer vervolgens de optie **Run ' [Spark-taak] xxx '** om lokale uitvoering uit te voeren.
1. Zodra de lokale uitvoering is voltooid, kunt u het uitvoer bestand opslaan in de **__standaard__** **gegevens** > van de project Verkenner.

    ![Resultaat van lokale uitvoer](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)
1. Onze tools hebben de standaard configuratie voor lokale uitvoering automatisch ingesteld wanneer u de lokale uitvoering en de lokale fout opsporing uitvoert. Open de configuratie **[Spark op hdinsight] xxx** . in de rechter bovenhoek ziet u de **[Spark op hdinsight] xxx** die al is gemaakt onder **Apache Spark op hdinsight**. Schakel over naar het tabblad **lokaal uitvoeren** .

    ![Lokale uitvoerings configuratie](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Omgevings variabelen](#prerequisite-for-windows): Als u de systeem omgevings variabele **HADOOP_HOME** al hebt ingesteld op **C:\WinUtils**, kan deze automatisch detecteren dat deze niet hand matig hoeft toe te voegen.
    - [Locatie van WinUtils. exe](#prerequisite-for-windows): Als u de omgevings variabele systeem niet hebt ingesteld, kunt u de locatie vinden door op de knop te klikken.
    - Kies een van de twee opties en ze zijn niet nodig voor MacOS en Linux.
1. U kunt de configuratie ook hand matig instellen voordat u Local run en Local debug uitvoert. Selecteer in de vorige scherm afbeelding het plus teken ( **+** ). Selecteer vervolgens de optie **Apache Spark op HDInsight** . Voer informatie in voor de **naam**, de naam van de **hoofd klasse** die u wilt opslaan en klik vervolgens op de knop lokale uitvoering.

### <a name="scenario-3-perform-local-debugging"></a>Scenario 3: Lokale fout opsporing uitvoeren
1. Open het script **SparkCore_wasbloTest** en stel onderbrekings punten in.
1. Klik met de rechter muisknop op de script editor en selecteer vervolgens de optie **fout opsporing [Spark op HDInsight] xxx** om lokale fout opsporing uit te voeren.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Meer informatie over het uitvoeren en opsporen van fouten op afstand
### <a name="scenario-1-perform-remote-run"></a>Scenario 1: Extern uitvoeren uitvoeren

1. Selecteer het pictogram in de rechter bovenhoek om toegang te krijgen tot het menu voor het **bewerken van configuraties** . In dit menu kunt u de configuraties voor fout opsporing op afstand maken of bewerken.

   ![Configuraties bewerken](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Selecteer in het dialoog venster **configuraties voor uitvoeren/fout opsporing** het plus teken **+** (). Selecteer vervolgens de optie **Apache Spark op HDInsight** .

   ![Nieuwe configuratie toevoegen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Overschakelen naar **extern uitvoeren op** het tabblad cluster. Voer informatie in voor de **naam**, het **Spark-cluster**en de naam van de **hoofd klasse**. Klik vervolgens op **Geavanceerde configuratie (fout opsporing op afstand)** . Onze hulp middelen ondersteunen debug met **uitvoerende**software. De **numExectors**, de standaard waarde is 5. U kunt beter niet meer dan 3 instellen.

   ![Configuraties voor fout opsporing uitvoeren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Selecteer in het gedeelte **Geavanceerde configuratie (externe fout opsporing)** de optie **Spark externe fout opsporing inschakelen**. Voer de SSH-gebruikers naam in en voer een wacht woord in of gebruik een bestand met een persoonlijke sleutel. Als u fout opsporing op afstand wilt uitvoeren, moet u het instellen. U hoeft deze niet in te stellen als u alleen extern uitvoeren wilt gebruiken.

   ![Spark externe fout opsporing inschakelen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. De configuratie wordt nu opgeslagen met de naam die u hebt ingevoerd. Als u de configuratie gegevens wilt weer geven, selecteert u de naam van de configuratie. Als u wijzigingen wilt aanbrengen, selecteert u **configuraties bewerken**. 

1. Nadat u de configuratie-instellingen hebt voltooid, kunt u het project uitvoeren op het externe cluster of externe fout opsporing uitvoeren.
   
   ![Knop voor uitvoering op afstand](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klik op de knop **verbinding verbreken** als de inzendings logboeken niet worden weer gegeven in het linkerdeel venster. Het is echter nog steeds actief op de back-end.

   ![Resultaat van externe uitvoering](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Scenario 2: Fout opsporing op afstand uitvoeren
1. Stel breuk punten in en klik vervolgens op het pictogram **fout opsporing op afstand** . Het verschil met externe verzen ding is dat de SSH-gebruikers naam en het wacht woord moeten worden geconfigureerd.

   ![Het pictogram voor fout opsporing selecteren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Wanneer de uitvoering van het programma het breuk punt bereikt, ziet u een tabblad van het **stuur programma** **en twee tabbladen** voor uitvoeringen in het deel venster **fout opsporing** . Selecteer het pictogram **programma hervatten** om door te gaan met het uitvoeren van de code, waarna het volgende onderbrekings punt wordt bereikt. U moet overschakelen naar **het tabblad voor** de juiste uitvoerder om de doel-uitvoerder te vinden voor fout opsporing. U kunt de uitvoerings logboeken bekijken op het bijbehorende **console** tabblad.

   ![Tabblad fout opsporing](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scenario 3: Fout opsporing op afstand en probleem oplossing uitvoeren
1. Stel twee afbreek punten in en selecteer vervolgens het pictogram **fout opsporing** om het proces voor externe fout opsporing te starten.

1. De code stopt bij het eerste punt, en de para meter-en variabele gegevens worden weer gegeven in het deel venster **variabelen** . 

1. Selecteer het pictogram **programma hervatten** om door te gaan. De code wordt op het tweede punt gestopt. De uitzonde ring wordt naar verwachting gedetecteerd.

   ![Fout bij genereren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Selecteer opnieuw het pictogram **programma hervatten** . In het venster **HDInsight Spark-verzen ding** wordt de fout taak uitvoering is mislukt weer gegeven.

   ![Fout bij verzenden](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Als u de waarde van de variabele dynamisch wilt bijwerken met behulp van de IntelliJ-functie voor fout opsporing, selecteert u **fout opsporing** opnieuw. Het deel venster **variabelen** wordt weer gegeven. 

1. Klik met de rechter muisknop op het doel op het tabblad **fout opsporing** en selecteer vervolgens **waarde instellen**. Voer vervolgens een nieuwe waarde in voor de variabele. Selecteer vervolgens **Enter** om de waarde op te slaan. 

   ![Waarde instellen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecteer het pictogram **programma hervatten** om door te gaan met het programma uitvoeren. Deze keer wordt er geen uitzonde ring gedetecteerd. U kunt zien dat het project zonder uitzonde ringen correct wordt uitgevoerd.

   ![Debug zonder uitzonde ring](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Volgende stappen
* [Krijgt Apache Spark in azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Scala-project maken (video): [Apache Spark scala-toepassingen maken](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fout opsporing op afstand (video): [Azure-toolkit voor IntelliJ gebruiken om fouten in Apache Spark toepassingen op afstand op te lossen op een HDInsight-cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenario's
* [Apache Spark met BI: Interactieve gegevens analyse uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren
* [Een zelfstandige toepassing maken met behulp van Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen
* [Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](apache-spark-intellij-tool-plugin.md)
* [Azure-toolkit voor IntelliJ gebruiken om fouten op te lossen Apache Spark toepassingen op afstand via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Gebruik HDInsight-Hulpprogram Ma's in Azure-toolkit voor Eclipse om Apache Spark-toepassingen te maken](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter-notebook in het Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
