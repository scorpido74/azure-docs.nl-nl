---
title: Debug Spark-taak met IntelliJ Azure Toolkit (preview) - HDInsight
description: Richtlijnen met HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ om toepassingen te debuggen
keywords: debug op afstand intellij, remote debugging intellij, ssh, intellij, hdinsight, debug intellij, debugging
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494605"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Fout mislukte foutopsporing van problemen met Azure Toolkit voor IntelliJ (voorbeeld)

In dit artikel vindt u stapsgewijze richtlijnen voor het gebruik van HDInsight-hulpprogramma's in [Azure Toolkit voor IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) om **Spark Failure Debug-toepassingen** uit te voeren.

## <a name="prerequisites"></a>Vereisten

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). In deze zelfstudie wordt gebruikgemaakt van Java-versie 8.0.202.
  
* IntelliJ IDEA. Dit artikel maakt gebruik van [IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure-toolkit voor IntelliJ. Zie [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (De Azure Toolkit voor IntelliJ installeren).

* Maak verbinding met uw HDInsight-cluster. Zie [Verbinding maken met uw HDInsight-cluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Zie [Microsoft Azure Storage Explorer downloaden](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Een project maken met foutopsporingssjabloon

Maak een spark2.3.2-project om foutopsporing voort te zetten, het foutopsporingsvoorbeeldbestand voor foutopsporing in dit document op te nemen.

1. Open IntelliJ IDEA. Open het venster **Nieuw project.**

   a. Selecteer **Azure Spark/HDInsight** in het linkerdeelvenster.

   b. Selecteer **Spark-project met voorbeeld van foutopsporing(voorbeeld(voorbeeld)(Scala)** in het hoofdvenster.

     ![Intellij Maak een debugproject](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecteer **Volgende**.

2. Ga in het venster **Nieuw project** de volgende stappen uit:

   ![Intellij New Project selecteert Spark-versie](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Voer een projectnaam en projectlocatie in.

   b. Selecteer Java **1.8** voor het cluster **Project** **SDK.**

   c. Selecteer **spark 2.3.2 (Scala 2.11.8)** in de vervolgkeuzelijst **Spark-versie** .

   d. Selecteer **Finish**.

3. Selecteer **src** > **main** > **scala** om uw code in het project te openen. In dit voorbeeld wordt het **script AgeMean_Div()** gebruikt.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Een Spark Scala/Java-toepassing uitvoeren op een HDInsight-cluster

Maak een spark Scala/Java-toepassing en voer de toepassing uit op een Spark-cluster door de volgende stappen uit te voeren:

1. Klik **op Configuratie toevoegen** om het venster Configuratie **uitvoeren/foutopsporing te** openen.

   ![HDI Intellij Voeg configuratie toe](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Selecteer in het dialoogvenster **Configuraties uitvoeren/foutopsporing** het**+** plusteken ( ). Selecteer vervolgens de optie **Apache Spark op HDInsight.**

   ![Intellij Nieuwe configuratie toevoegen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Overschakelen naar Extern uitvoeren op het tabblad **Cluster.** Voer gegevens in voor **naam,** **sparkcluster**en **naam van de hoofdklasse**. Onze tools ondersteunen foutopsporing **met uitvoerders.** De **numExectors**, de standaardwaarde is 5 en u beter niet hoger dan 3 instellen. Om de looptijd te **verkorten,** u **spark.yarn.maxAppAttempts** toevoegen aan taakconfiguraties en de waarde instellen op 1. Klik op de knop **OK** om de configuratie op te slaan.

   ![Intellij Run debug configuraties nieuw](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. De configuratie wordt nu opgeslagen met de door u opgegeven naam. Als u de configuratiegegevens wilt weergeven, selecteert u de configuratienaam. Als u wijzigingen wilt aanbrengen, selecteert u **Configuraties bewerken**.

5. Nadat u de configuratie-instellingen hebt voltooid, u het project uitvoeren op het externe cluster.

   ![Intellij Debug Remote Spark Job Remote run-knop](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. U de toepassings-id controleren vanuit het uitvoervenster.

   ![Intellij Debug Remote Spark Job Remote run resultaat](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Mislukt taakprofiel downloaden

Als het indienen van de taak mislukt, u het mislukte taakprofiel downloaden naar de lokale machine voor verdere foutopsporing.

1. Open **Microsoft Azure Storage Explorer**, zoek het HDInsight-account van het cluster voor de mislukte taak en download de mislukte taakbronnen vanaf de desbetreffende locatie: **\hdp\spark2-events\\.spark-failures\\\<application ID>** naar een lokale map. In het **venster Activiteiten** wordt de voortgang van het downloaden weergegeven.

   ![Downloadfout in Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Succes van Azure Storage Explorer downloaden](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Lokale foutopsporingsomgeving en foutopsporing configureren bij mislukte

1. Open het oorspronkelijke project of maak een nieuw project en koppel het aan de oorspronkelijke broncode. Alleen spark2.3.2 versie wordt ondersteund voor fout debugging op dit moment.

1. In IntelliJ IDEA maakt u een foutbug-config-bestand **voor spark-fout,** selecteert u het FTD-bestand uit de eerder gedownloade mislukte taakbronnen voor het locatieveld Context **van de spark-taakfout.**

   ![configuratie van kretastoringen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klik op de knop Lokale run op de werkbalk, de fout wordt weergegeven in het venster Uitvoeren.

   ![run-failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![run-failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Stel het breekpunt in zoals het logboek aangeeft en klik vervolgens op lokale foutopsporingsknop om lokale debugging te doen, net zoals uw normale Scala/ Java-projecten in IntelliJ.

1. Als het project na het debuggen is voltooid, u de mislukte taak opnieuw indienen bij uw spark op het HDInsight-cluster.

## <a name="next-steps"></a><a name="seealso"></a>Volgende stappen

* [Overzicht: Apache Spark-toepassingen debuggen](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Scala-project maken (video): [Apache Spark Scala-toepassingen maken](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Externe foutopsporing (video): [Gebruik Azure Toolkit voor IntelliJ om Apache Spark-toepassingen op afstand te debuggen op een HDInsight-cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: doe interactieve data-analyse met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de temperatuur van gebouwen te analyseren met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen te maken voor een HDInsight-cluster](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Gebruik HDInsight Tools voor IntelliJ met Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HdInsight-hulpprogramma's gebruiken in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in het Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
