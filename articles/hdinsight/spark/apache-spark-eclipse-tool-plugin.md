---
title: 'Azure Toolkit voor Eclipse: Maak Scala-apps voor HDInsight Spark'
description: Gebruik HDInsight Tools in Azure Toolkit voor Eclipse om Spark-toepassingen die in Scala zijn geschreven te ontwikkelen en deze rechtstreeks vanuit de Eclipse IDE naar een HDInsight Spark-cluster te verzenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272108"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit voor Eclipse gebruiken om Apache Spark-toepassingen te maken voor een HDInsight-cluster

Gebruik HDInsight-tools in Azure Toolkit voor [Eclipse](https://www.eclipse.org/) om [Apache Spark-toepassingen](https://spark.apache.org/) die in [Scala](https://www.scala-lang.org/) zijn geschreven te ontwikkelen en deze rechtstreeks vanuit de Eclipse IDE naar een Azure HDInsight Spark-cluster te verzenden. U de PLUG-in HDInsight Tools op een aantal verschillende manieren gebruiken:

* Een Scala Spark-toepassing ontwikkelen en indienen op een HDInsight Spark-cluster.
* Toegang krijgen tot uw Azure HDInsight Spark-clusterbronnen.
* Om een Scala Spark applicatie lokaal te ontwikkelen en uit te voeren.

## <a name="prerequisites"></a>Vereisten

* Apache Spark cluster op HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). Dit artikel maakt gebruik van de Eclipse IDE voor Java-ontwikkelaars.

## <a name="install-required-plug-ins"></a>Vereiste plug-ins installeren

### <a name="install-azure-toolkit-for-eclipse"></a>De Azure Toolkit voor Eclipse installeren

Zie [Azure Toolkit voor Eclipse installeren voor](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation)installatie-instructies.

### <a name="install-the-scala-plug-in"></a>De Scala-plug-in installeren

Wanneer u Eclipse opent, detecteert HDInsight Tools automatisch of u de Scala-plug-in hebt geïnstalleerd. Selecteer **OK** om door te gaan en volg de instructies om de plug-in van de Eclipse Marketplace te installeren. Start de IDE opnieuw op nadat de installatie is voltooid.

![Automatische installatie van de Scala plug-in](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Plug-ins bevestigen

1. Navigeer naar **Help** > **Eclipse Marketplace...**.

1. Selecteer het tabblad **Geïnstalleerd.**

1. Je moet op zijn minst zien:
    * Azure Toolkit \<voor Eclipse-versie>.
    * Scala IDE \<versie>.

## <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Start Eclipse IDE.

1.  > Navigeer naar Weergave **Vensterweergave** >  **Show View****Andere...**  >  **Log hier in**

1. Navigeer in het dialoogvenster **Weergave weergeven** naar **Azure** > **Azure Explorer**en selecteer **Openen**.

   ![Apache Spark Eclipse show weergave](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. Klik in **Azure Explorer**met de rechtermuisknop op het **Azure-knooppunt** en selecteer **Aanmelden**.

1. Kies **in** het dialoogvenster Aanmelden voor Azure de verificatiemethode, selecteer **Aanmelden**en voltooi het aanmeldingsproces.

   ![Apache Spark Eclipse Azure-teken](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Nadat u bent aangemeld, worden in het dialoogvenster **Uw abonnementen** alle Azure-abonnementen weergegeven die aan de referenties zijn gekoppeld. Druk op **Selecteren** om het dialoogvenster te sluiten.

   ![Dialoogvenster Abonnementen selecteren](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Navigeer vanuit **Azure Explorer**naar **Azure** >  **HDInsight** om de HDInsight Spark-clusters onder uw abonnement te bekijken.

   ![HDInsight Spark-clusters in Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. U een clusternaamknooppunt verder uitbreiden om de bronnen (bijvoorbeeld opslagaccounts) te zien die aan het cluster zijn gekoppeld.

   ![Een clusternaam uitbreiden om bronnen te zien](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Een cluster koppelen

U een normaal cluster koppelen met de door Ambari beheerde gebruikersnaam. Op dezelfde manier u voor een HDInsight-cluster met een domein `user1@contoso.com`koppelen met behulp van het domein en de gebruikersnaam, zoals.

1. Klik in **Azure Explorer**met de rechtermuisknop op **HDInsight**en selecteer **Een cluster koppelen**.

   ![Menu Azure Explorer-koppelingscluster](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Voer **clusternaam,** **gebruikersnaam**en **wachtwoord**in en selecteer **OK**. Voer desgewenst Opslagaccount, opslagsleutel en selecteer Opslagcontainer voor opslagverkenner om in de linkerstructuurweergave te werken

   ![Dialoogvenster Nieuw HDInsight-cluster koppelen](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > We gebruiken de gekoppelde opslagsleutel, gebruikersnaam en wachtwoord als het cluster zowel is aangemeld bij azure-abonnement als een cluster met elkaar verbonden.
   > ![Azure Explorer-opslagaccounts](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Voor de gebruiker van het toetsenbord moet u, wanneer de huidige focus zich op **de opslagsleutel bevindt,** **Ctrl+TAB** gebruiken om u te concentreren op het volgende veld in het dialoogvenster.

1. U het gekoppelde cluster onder **HDInsight**zien. Nu u een aanvraag indienen bij dit gekoppelde cluster.

   ![Azure Explorer hdi gekoppeld cluster](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. U ook de koppeling van een cluster loskoppelen van **Azure Explorer.**

   ![Azure Explorer-niet-gekoppeld cluster](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Een Spark Scala-project instellen voor een HDInsight Spark-cluster

1. Selecteer in de Eclipse **IDE-werkruimte File** > **New** > **Project...**.

1. Selecteer **hdinsight projectspark** > **op HDInsight (Scala)** in de wizard Nieuw **project.** Selecteer **vervolgens Volgende**.

   ![Het Spark on HDInsight (Scala) project selecteren](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Geef in het dialoogvenster **Nieuw HDInsight Scala Project** de volgende waarden op en selecteer **Volgende:**
   * Voer een naam in voor het project.
   * Zorg er in het **JRE-gebied** voor dat **JRE van een uitvoeringsomgeving** gebruiken is ingesteld op **JavaSE-1.7** of hoger.
   * In het gebied **sparkbibliotheek** u Maven gebruiken kiezen **om de spark SDK-optie te configureren.**  Onze tool integreert de juiste versie voor Spark SDK en Scala SDK. U ook handmatig **Spark SDK toevoegen** kiezen, Spark SDK handmatig downloaden en toevoegen.

   ![Nieuw dialoogvenster HDInsight Scala Project](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. Bekijk in het volgende dialoogvenster de details en selecteer **Voltooien.**

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Een Scala-toepassing maken voor een HDInsight Spark-cluster

1. Vouw vanuit **Package Explorer**het project uit dat u eerder hebt gemaakt. Klik met de rechtermuisknop op **src,** selecteer **Nieuwe** > **Andere...**.

1. Selecteer **scala-object Scala** > **van**wizard smaken in het dialoogvenster **Een wizard selecteren** . Selecteer **vervolgens Volgende**.

   ![Een wizard Een Scala-object maken selecteren](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. Voer in het dialoogvenster **Nieuw bestand maken** een naam voor het object in en selecteer **Voltooien.** Er wordt een teksteditor geopend.

   ![Nieuwe wizard Bestand Maken nieuw bestand](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. Vervang in de teksteditor de huidige inhoud door de onderstaande code:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Voer de toepassing uit op een HDInsight Spark-cluster:

   a. Klik vanuit Package Explorer met de rechtermuisknop op de projectnaam en selecteer **Spark-toepassing verzenden naar HDInsight**.

   b. Geef in het dialoogvenster **Vonkindiening** de volgende waarden op en selecteer **Verzenden:**

   * Selecteer **voor clusternaam**het CLUSTER HDInsight Spark waarop u uw toepassing wilt uitvoeren.
   * Selecteer een artefact uit het Eclipse-project of selecteer er een op een harde schijf. De standaardwaarde is afhankelijk van het item waarop u met de rechtermuisknop klikt vanuit Package Explorer.
   * In de vervolgkeuzelijst **Hoofdklasse-klassenaam** geeft de wizard Indiening alle objectnamen van uw project weer. Selecteer of voer een in die u wilt uitvoeren. Als u een artefact hebt geselecteerd op een harde schijf, moet u de naam van de hoofdklasse handmatig invoeren. 
   * Omdat de toepassingscode in dit voorbeeld geen opdrachtregelargumenten of referentie-JARs of -bestanden vereist, u de resterende tekstvakken leeg laten.

     ![Dialoogvenster Apache Spark-indiening](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Op het tabblad **Spark-indiening** moet de voortgang worden weergegeven. U de toepassing stoppen door de rode knop te selecteren in het venster **Vonkindiening.** U ook de logboeken voor deze specifieke toepassing bekijken door het wereldbolpictogram te selecteren (aangeduid met het blauwe vak in de afbeelding).

   ![Venster Apache Spark-indiening](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>HDInsight Spark-clusters openen en beheren met HDInsight-hulpprogramma's in Azure Toolkit voor Eclipse

U verschillende bewerkingen uitvoeren met HDInsight Tools, waaronder toegang tot de taakuitvoer.

### <a name="access-the-job-view"></a>Toegang tot de taakweergave

1. Vouw **hdinsight**in **Azure Explorer**uit en vervolgens de naam spark-cluster en selecteer **Vervolgens Taken**.

   ![Knooppunt van de functie-eclipse van Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Selecteer het knooppunt **Vacatures.** Als de Java-versie lager is dan **1.8,** herinnering aan HDInsight Tools dat u de **E(fx)clipse** plug-in installeert. Selecteer **OK** om door te gaan en volg de wizard om deze te installeren vanaf de Eclipse Marketplace en start Eclipse opnieuw.

   ![Ontbrekende plugin E(fx)clipse installeren](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Open de taakweergave vanuit het knooppunt **Vacatures.** In het rechterdeelvenster worden op het tabblad **Spark-taakweergave** alle toepassingen weergegeven die op het cluster zijn uitgevoerd. Selecteer de naam van de toepassing waarvoor u meer details wilt zien.

   ![Apache Eclipse view job logs details Apache Eclipse view job logs details Apache Eclipse view job logs details Apache Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   U dan een van deze acties ondernemen:

   * Zweef op de werkgrafiek. Het toont basisinformatie over de lopende taak. Selecteer de taakgrafiek en u de fasen en informatie zien die elke taak genereert.

     ![Informatie over de fase fase van de fase van de fase van de werkgrafiek van Apache Spark](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecteer het tabblad **Logboek** om veelgebruikte logboeken weer te geven, waaronder **Driver Stderr,** **Driver Stdout**en **Directory Info**.

     ![Apache Spark Eclipse job log info](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Open de Spark history UI en de Apache Hadoop YARN UI (op toepassingsniveau) door de hyperlinks boven aan het venster te selecteren.

### <a name="access-the-storage-container-for-the-cluster"></a>Toegang tot de opslagcontainer voor het cluster

1. Vouw in Azure Explorer het **hoofdknooppunt HDInsight** uit om een lijst met HDInsight Spark-clusters te bekijken die beschikbaar zijn.

1. Vouw de clusternaam uit om het opslagaccount en de standaardopslagcontainer voor het cluster te bekijken.

   ![Opslagaccount en standaardopslagcontainer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Selecteer de naam van de opslagcontainer die aan het cluster is gekoppeld. Dubbelklik in het rechterdeelvenster op de map **HVACOut.** Open een van de **deelbestanden** om de uitvoer van de toepassing te bekijken.

### <a name="access-the-spark-history-server"></a>Toegang tot de Spark-geschiedenisserver

1. Klik in Azure Explorer met de rechtermuisknop op de naam van het Spark-cluster en selecteer vervolgens **de gebruikersinterface van Spark-geschiedenis openen**. Wanneer u wordt gevraagd, voert u de beheerdersreferenties voor het cluster in. U hebt deze opgegeven tijdens het inrichten van het cluster.

1. In het dashboard van de Spark-geschiedenisserver gebruikt u de toepassingsnaam om te zoeken naar de toepassing die u zojuist hebt uitgevoerd. In de vorige code stelt u de `val conf = new SparkConf().setAppName("MyClusterApp")`toepassingsnaam in met behulp van . Dus, uw Spark applicatie naam was **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Start de Apache Ambari portal

1. Klik in Azure Explorer met de rechtermuisknop op de naam van het Spark-cluster en selecteer Vervolgens **Clusterbeheerportal openen (Ambari).**

1. Wanneer u wordt gevraagd, voert u de beheerdersreferenties voor het cluster in. U hebt deze opgegeven tijdens het inrichten van het cluster.

### <a name="manage-azure-subscriptions"></a>Azure-abonnementen beheren

Standaard worden in HDInsight Tool in Azure Toolkit for Eclipse de Spark-clusters van al uw Azure-abonnementen weergegeven. Indien nodig u de abonnementen opgeven waarvoor u toegang wilt krijgen tot het cluster.

1. Klik in Azure Explorer met de rechtermuisknop op het **Azure-hoofdknooppunt** en selecteer Abonnementen **beheren**.

1. Schakel in het dialoogvenster de selectievakjes uit voor het abonnement dat u niet wilt openen en selecteer **Sluiten**. U **ook Afmelden** selecteren als u zich wilt afmelden bij uw Azure-abonnement.

## <a name="run-a-spark-scala-application-locally"></a>Een Spark Scala-toepassing lokaal uitvoeren

U HDInsight-hulpprogramma's in Azure Toolkit voor Eclipse gebruiken om Spark Scala-toepassingen lokaal op uw werkstation uit te voeren. Deze toepassingen hebben doorgaans geen toegang nodig tot clusterbronnen zoals een opslagcontainer en u ze lokaal uitvoeren en testen.

### <a name="prerequisite"></a>Vereiste

Terwijl u de lokale Spark Scala-toepassing uitvoert op een Windows-computer, krijgt u mogelijk een uitzondering zoals uitgelegd in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Deze uitzondering treedt op omdat **WinUtils.exe** ontbreekt in Windows.

Als u deze fout wilt oplossen, hebt u [Winutils.exe](https://github.com/steveloughran/winutils) nodig naar een locatie als **C:\WinUtils\bin**en voegt u de omgevingsvariabele **toe HADOOP_HOME** en stelt u de waarde van de variabele in op **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Een lokale Spark Scala-toepassing uitvoeren

1. Start Eclipse en maak een project. Maak in het dialoogvenster **Nieuw project** de volgende keuzes en selecteer **Volgende**.

1. Selecteer in de wizard **Nieuw project** **hdinsight projectspark** > **op HDInsight Local Run Sample (Scala)**. Selecteer **vervolgens Volgende**.

   ![Nieuw project selecteert een wizarddialoogvenster](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Volg de stappen 3 tot en met 6 van de eerdere sectie [Setup a Spark Scala-project voor een HDInsight Spark-cluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)om de projectdetails te geven.

1. De sjabloon voegt een voorbeeldcode **(LogQuery)** toe onder de **src-map** die u lokaal op uw computer uitvoeren.

   ![Locatie van de lokale scalatoepassing van LogQuery](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Klik met de rechtermuisknop op **LogQuery.scala** en selecteer **Uitvoeren als** > **1 Scala-toepassing**. Als u deze uitvoer op het tabblad **Console** vindt, wordt dit weergegeven:

   ![Resultaat lokale run van spark-toepassingen](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Rol met alleen voor lezers

Wanneer gebruikers taak indienen bij een cluster met alleen toestemming voor een leesfunctie, is Ambari-referenties vereist.

### <a name="link-cluster-from-context-menu"></a>Koppelingscluster vanuit contextmenu

1. Meld u aan met een account met alleen een lezer.

2. Vouw **HDInsight** uit vanuit **Azure Explorer**om HDInsight-clusters in uw abonnement weer te geven. De clusters met de vermelding **"Rol:Reader"** hebben alleen toestemming voor alleen een lezerrol.

    ![HDInsight Spark-clusters in Azure Explorer-rollezer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Klik met de rechtermuisknop op het cluster met alleen voor lezers machtiging. Selecteer **Dit cluster koppelen** in het contextmenu om het cluster te koppelen. Voer de gebruikersnaam en het wachtwoord van Ambari in.

    ![HDInsight Spark-clusters in Azure Explorer-koppeling](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Als het cluster is gekoppeld, wordt HDInsight vernieuwd.
   De fase van het cluster wordt gekoppeld.
  
    ![HDInsight Spark-clusters in Azure Explorer gekoppeld](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Koppelingscluster door het knooppunt Vacatures uit te breiden

1. Klik op het knooppunt **Vacatures,** het venster **Toegang tot clustertaak geweigerd** wordt weergegeven.

2. Klik **op Dit cluster koppelen** om het cluster te koppelen.

    ![HDInsight Spark-clusters in Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Koppelingscluster vanuit het venster Spark-indiening

1. Maak een HDInsight Project.

2. Klik met de rechtermuisknop op het pakket. Selecteer vervolgens **Spark-toepassing verzenden naar HDInsight**.

   ![HDInsight Spark-clusters in Azure Explorer verzenden](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Selecteer een cluster met alleen voor een lezer-functie voor **clusternaam**. Waarschuwingsbericht verschijnt. U op **Dit cluster koppelen** om het cluster te koppelen.

   ![HDInsight Spark-clusters in Azure Explorer koppelen dit](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Opslagaccounts weergeven

* Voor clusters met alleen voor lezers rollen, klikt u op **Knooppunt Opslagaccounts,** het venster **Geweigerde opslagtoegang** verschijnt.

   ![HDInsight Spark-clusters in Azure Explorer-opslag](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![HDInsight Spark-clusters in Azure Explorer geweigerd](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* Klik voor gekoppelde clusters op **knooppunt Opslagaccounts,** het venster **Geweigerde opslagtoegang** wordt weergegeven.

   ![HDInsight Spark-clusters in Azure Explorer geweigerd2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Bekende problemen

Bij het gebruik van **Link A Cluster,** zou ik raden u aan om de referentie van de opslag te bieden.

![koppelingcluster met verduisteringen van opslagreferenties](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Er zijn twee modi om de taken in te dienen. Als opslagreferenties worden opgegeven, wordt de batchmodus gebruikt om de taak in te dienen. Anders wordt de interactieve modus gebruikt. Als het cluster bezet is, u de onderstaande fout krijgen.

![eclipse krijgen fout wanneer cluster bezet](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "eclipse krijgen fout wanneer cluster bezet")

![eclipse krijgen fout bij cluster bezig garen](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "eclipse krijgen fout bij cluster bezig garen")

## <a name="see-also"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Azure Toolkit voor IntelliJ gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster op HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
