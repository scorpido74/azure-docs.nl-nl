---
title: 'Azure Toolkit voor IntelliJ: Spark-apps debugen met SSH - HDInsight'
description: Stapsgewijze richtlijnen voor het gebruik van HDInsight-tools in Azure Toolkit voor IntelliJ om toepassingen op afstand te debuggen op HDInsight-clusters via SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: a012c3ce8f7c9e105a42d8383a502f3608c84070
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732913"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debug Apache Spark-toepassingen op een HDInsight-cluster met Azure Toolkit voor IntelliJ via SSH

In dit artikel vindt u stapsgewijze richtlijnen voor het gebruik van HDInsight-hulpprogramma's in [Azure Toolkit voor IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) om toepassingen op afstand te debuggen op een HDInsight-cluster. Als u uw project wilt debuggen, u ook de [Debug HDInsight Spark-toepassingen bekijken met Azure Toolkit voor IntelliJ-video.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Een Cluster Apache Spark maken](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Voor Windows-gebruikers: Terwijl u de lokale Spark Scala-toepassing op een Windows-computer uitvoert, krijgt u mogelijk een uitzondering, zoals uitgelegd in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzondering treedt op omdat WinUtils.exe ontbreekt op Windows.

    Als u deze fout wilt oplossen, downloadt u [Winutils.exe](https://github.com/steveloughran/winutils) naar een locatie zoals **C:\WinUtils\bin**. Voeg vervolgens de omgevingsvariabele **toe HADOOP_HOME**en stel de waarde van de variabele in op **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (De Community editie is gratis.).

* [Azure Toolkit voor IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Scala plugin voor IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Een Spark Scala-toepassing maken

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

1. Selecteer **Apache Spark/HDInsight** in het linkerdeelvenster.

1. Selecteer **Spark-project met voorbeelden (Scala)** in het hoofdvenster.

1. Selecteer in de vervolgkeuzelijst **Build tool** een van de volgende opties:

    * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
    * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

     ![Intellij Create New Project Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecteer **Next**.

1. Geef in het volgende venster **Nieuw project** de volgende informatie op:

    |Eigenschap |Beschrijving |
    |---|---|
    |Projectnaam|Voer een naam in. Deze wandeling `myApp`door gebruik .|
    |Projectlocatie|Voer de gewenste locatie in om uw project in op te slaan.|
    |Project SDK|Als u leeg bent, selecteert u **Nieuw...** en navigeert u naar uw JDK.|
    |Spark version|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x.**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

   ![Intellij New Project selecteert Spark-versie](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecteer **Finish**. Het kan enkele minuten duren voordat het project beschikbaar is. Let op de rechterbenedenhoek voor vooruitgang.

1. Breid uw project uit en navigeer naar het**belangrijkste** > voorbeeld van **src** > **scala.** > **sample** Dubbelklik op **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Lokale run uitvoeren

1. Klik in het **SparkCore_WasbIOTest** script met de rechtermuisknop op de scripteditor en selecteer vervolgens de optie **'SparkCore_WasbIOTest'** uitvoeren om lokale run uit te voeren.

1. Zodra de lokale run is voltooid, u het uitvoerbestand opslaan in de huidige **projectexplorergegevens** > **__standaard zien.__**

    ![Intellij Project lokaal run resultaat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Onze hulpprogramma's hebben de standaard configuratie van lokale uitvoering automatisch ingesteld wanneer u de lokale uitvoering en lokale foutopsporing uitvoert. Open de configuratie **[Spark on HDInsight] XXX** in de rechterbovenhoek, zie je de **[Spark on HDInsight]XXX** al gemaakt onder **Apache Spark op HDInsight**. Overschakelen naar het tabblad **Lokaal uitvoeren.**

    ![Intellij Run debug configuraties lokale run](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Omgevingsvariabelen:](#prerequisites)Als u de variabele systeemomgeving al **HADOOP_HOME** instelt op **C:\WinUtils,** kan deze automatisch detecteren dat u niet handmatig hoeft toe te voegen.
    - [WinUtils.exe Locatie:](#prerequisites)Als u de systeemomgevingsvariabele niet hebt ingesteld, u de locatie vinden door op de knop te klikken.
    - Kies gewoon een van de twee opties en ze zijn niet nodig op MacOS en Linux.

1. U de configuratie ook handmatig instellen voordat u lokale uitvoering en lokale foutopsporing uitvoert. Selecteer in de vorige schermafbeelding het**+** plusteken ( ). Selecteer vervolgens de optie **Apache Spark op HDInsight.** Voer gegevens in voor **Naam**, **Hoofdklassenaam** om op te slaan en klik op de knop Lokale run.

## <a name="perform-local-debugging"></a>Lokale foutopsporing uitvoeren

1. Open het **SparkCore_wasbloTest** script, stel breekpunten in.

1. Klik met de rechtermuisknop op de scripteditor en selecteer de optie **Foutopsporing '[Spark op HDInsight]XXX'** om lokale foutopsporing uit te voeren.

## <a name="perform-remote-run"></a>Remote run uitvoeren

1. Navigeer naar Configuraties bewerken **uitvoeren...** > **Edit Configurations...**. In dit menu u de configuraties voor het opsporen van afstandsbedieningmaken of bewerken.

1. Selecteer in het dialoogvenster **Configuraties uitvoeren/foutopsporing** het**+** plusteken ( ). Selecteer vervolgens de optie **Apache Spark op HDInsight.**

   ![Intellij Nieuwe configuratie toevoegen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Overschakelen naar Extern uitvoeren op het tabblad **Cluster.** Voer gegevens in voor **naam,** **sparkcluster**en **naam van de hoofdklasse**. Klik vervolgens op **Geavanceerde configuratie (Remote Debugging)**. Onze tools ondersteunen foutopsporing **met uitvoerders.** De **numExectors**, de standaardwaarde is 5. Je beter niet hoger dan 3 zetten.

   ![Intellij Run debug configuraties](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Selecteer in het gedeelte **Geavanceerde configuratie (Remote Debugging)** de optie **Detectie van spark-afstandsbediening inschakelen**. Voer de SSH-gebruikersnaam in en voer een wachtwoord in of gebruik een privésleutelbestand. Als u externe foutopsporing wilt uitvoeren, moet u deze instellen. Het is niet nodig om het in te stellen als je gewoon op afstand wilt draaien.

   ![Intellij Advanced Configuration enable spark remote debug Intellij Advanced Configuration enable spark remote debug Intellij Advanced Configuration enable spark remote debug Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. De configuratie wordt nu opgeslagen met de door u opgegeven naam. Als u de configuratiegegevens wilt weergeven, selecteert u de configuratienaam. Als u wijzigingen wilt aanbrengen, selecteert u **Configuraties bewerken**.

1. Nadat u de configuratie-instellingen hebt voltooid, u het project uitvoeren tegen het externe cluster of het foutopsporing op afstand uitvoeren.

   ![Intellij Debug Remote Spark Job Remote run-knop](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klik op de knop **Verbinding verbreken** dat de indieningslogboeken niet in het linkerdeelvenster worden weergegeven. Echter, het is nog steeds draait op de backend.

   ![Intellij Debug Remote Spark Job Remote run resultaat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Foutopsporing op afstand uitvoeren

1. Stel breekpunten in en klik op het pictogram **Foutopsporing op afstand.** Het verschil met indiening op afstand is dat SSH gebruikersnaam / wachtwoord moeten worden geconfigureerd.

   ![Pictogram Foutopsporing van Intellij Debug Remote Spark Job](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Wanneer de uitvoering van het programma het breekpunt bereikt, ziet u een tabblad **Stuurprogramma** en twee **tabbladen executor** in het deelvenster **Foutopsporing.** Selecteer het pictogram **Programma hervatten** om de code te blijven uitvoeren, die vervolgens het volgende breekpunt bereikt. U moet overschakelen naar het juiste **tabblad Executor** om de doeluitvoerder te vinden om te debuggen. U de uitvoeringslogboeken bekijken op het bijbehorende **tabblad Console.**

   ![Intellij Debug Remote Spark Job Debugging tab](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Externe foutopsporing en foutherstellen uitvoeren

1. Stel twee breekpunten in en selecteer vervolgens het pictogram **Foutopsporing** om het foutopsporingsproces op afstand te starten.

1. De code stopt bij het eerste breekpunt en de parameter en variabele informatie worden weergegeven in het deelvenster **Variabelen.**

1. Selecteer het pictogram **Programma hervatten** om door te gaan. De code stopt bij het tweede punt. De uitzondering wordt gevangen zoals verwacht.

   ![Intellij Debug Remote Spark Job gooien fout](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecteer het pictogram **Programma hervatten** opnieuw. In het venster **HDInsight Spark Submission** wordt een fout 'taakuitvoeren mislukt' weergegeven.

   ![Intellij Debug Remote Spark Job Error indiening](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Als u de variabele waarde dynamisch wilt bijwerken met behulp van de foutopsporingsmogelijkheid van IntelliJ, selecteert u **Foutopsporing** opnieuw. Het deelvenster **Variabelen** wordt opnieuw weergegeven.

1. Klik met de rechtermuisknop op het doel op het tabblad **Foutopsporing** en selecteer **Waarde instellen**. Voer vervolgens een nieuwe waarde in voor de variabele. Selecteer vervolgens **Enter** om de waarde op te slaan.

   ![Intellij Debug Remote Spark Job set waarde](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecteer het pictogram **Programma hervatten** om het programma te blijven uitvoeren. Deze keer wordt er geen uitzondering gemaakt. U zien dat het project zonder uitzonderingen wordt uitgevoerd.

   ![Intellij Debug Remote Spark Job zonder uitzondering](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Scala-project maken (video): [Apache Spark Scala-toepassingen maken](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Externe foutopsporing (video): [Gebruik Azure Toolkit voor IntelliJ om Apache Spark-toepassingen op afstand te debuggen op een HDInsight-cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevensanalyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de temperatuur van gebouwen te analyseren met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen te maken voor een HDInsight-cluster](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HdInsight-hulpprogramma's gebruiken in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in het Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
