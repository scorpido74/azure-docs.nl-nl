---
title: 'Azure Toolkit: Apache Spark-apps op afstand debuggen - Azure HDInsight'
description: Meer informatie over het gebruik van HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ om spark-toepassingen die via VPN op HDInsight-clusters worden uitgevoerd, op afstand te debuggen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935023"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen in HDInsight via VPN

We raden u aan [Apache Spark-toepassingen](https://spark.apache.org/) op afstand te debuggen via SSH. Zie Apache [Spark-toepassingen op afstand debuggen op een HDInsight-cluster met Azure Toolkit voor IntelliJ via SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

In dit artikel vindt u stapsgewijze richtlijnen voor het gebruik van de HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ om een Spark-taak in te dienen op een HDInsight Spark-cluster en deze vervolgens op afstand te debuggen vanaf uw desktopcomputer. Als u deze taken wilt uitvoeren, moet u de volgende stappen op hoog niveau uitvoeren:

1. Maak een extern azure-netwerk van site-to-site of van punt tot site. De stappen in dit document gaan ervan uit dat u een site-to-site-netwerk gebruikt.
1. Maak een Spark-cluster in HDInsight dat deel uitmaakt van het virtuele netwerk van site-to-site.
1. Controleer de verbinding tussen het clusterhoofdknooppunt en het bureaublad.
1. Maak een Scala-toepassing in IntelliJ IDEA en configureer deze vervolgens voor foutopsporing op afstand.
1. Voer de toepassing uit en debugt deze.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Een gratis proefversie van Azure voor](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)meer informatie.
* **Een Apache Spark cluster in HDInsight**. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* **Oracle Java ontwikkelingskit**. U het installeren vanaf de [Oracle-website.](https://aka.ms/azure-jdks)
* **IntelliJ IDEE**. In dit artikel wordt versie 2017.1 gebruikt. U het installeren vanaf de [JetBrains website.](https://www.jetbrains.com/idea/download/)
* **HDInsight-hulpprogramma's in Azure-toolkit voor IntelliJ.** HDInsight-tools voor IntelliJ zijn beschikbaar als onderdeel van Azure Toolkit voor IntelliJ. Zie [Azure Toolkit voor IntelliJ installeren voor](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation)instructies voor het installeren van Azure Toolkit.
* **Meld u aan bij uw Azure-abonnement via IntelliJ IDEA**. Volg de instructies in [Azure Toolkit gebruiken voor IntelliJ om Apache Spark-toepassingen te maken voor een HDInsight-cluster.](apache-spark-intellij-tool-plugin.md)
* **Tijdelijke oplossing voor uitzonderingen**. Tijdens het uitvoeren van de Spark Scala-toepassing voor foutopsporing op afstand op een Windows-computer, krijgt u mogelijk een uitzondering. Deze uitzondering wordt uitgelegd in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) en treedt op als gevolg van een ontbrekend WinUtils.exe-bestand in Windows. Als u deze fout wilt omzeilen, moet u [Winutils.exe](https://github.com/steveloughran/winutils) downloaden naar een locatie zoals **C:\WinUtils\bin**. Voeg een **HADOOP_HOME** omgevingsvariabele toe en stel de waarde van de variabele in op **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Stap 1: Een virtueel Azure-netwerk maken

Volg de instructies van de volgende koppelingen om een virtueel Azure-netwerk te maken en controleer vervolgens de verbinding tussen uw desktopcomputer en het virtuele netwerk:

* [Een VNet maken met een site-to-site VPN-verbinding met behulp van de Azure-portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Een VNet maken met een site-to-site VPN-verbinding met PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Een point-to-site-verbinding configureren met PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Stap 2: Een HDInsight Spark-cluster maken

We raden u aan ook een Apache Spark-cluster in Azure HDInsight te maken dat deel uitmaakt van het virtuele Azure-netwerk dat u hebt gemaakt. Gebruik de beschikbare informatie in [Clusters maken op Linux in HDInsight.](../hdinsight-hadoop-provision-linux-clusters.md) Als onderdeel van de optionele configuratie selecteert u het virtuele Azure-netwerk dat u in de vorige stap hebt gemaakt.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Stap 3: De verbinding tussen het clusterhoofdknooppunt en uw bureaublad verifiëren

1. Haal het IP-adres van het hoofdknooppunt. Ambari-gebruikersinterface openen voor het cluster. Selecteer **Dashboard**in het clusterblad .

    ![Dashboard selecteren in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Selecteer **hosts**in de gebruikersinterface van Ambari .

    ![Hosts selecteren in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. U ziet een lijst met hoofdknooppunten, werknemersknooppunten en knooppunten voor dierenverzorgers. De kopknooppunten hebben een **hn*** voorvoegsel. Selecteer het eerste hoofdknooppunt.

    ![Vind het hoofdknooppunt in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. Kopieer in het **deelvenster Overzicht** onder aan de pagina die wordt geopend het **IP-adres** van het hoofdknooppunt en de **hostnaam**.

    ![Zoek het IP-adres in Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Voeg het IP-adres en de hostnaam van het hoofdknooppunt toe aan het **hosts-bestand** op de computer waar u wilt uitvoeren en de buginde op afstand de Spark-taak. Hiermee u communiceren met het hoofdknooppunt met behulp van het IP-adres en de hostnaam.

   a. Open een Kladblokbestand met verhoogde machtigingen. Selecteer **In** het menu Bestand de optie **Openen**en zoek vervolgens de locatie van het hosts-bestand. Op een Windows-computer is de locatie **C:\Windows\System32\Drivers\etc\hosts**.

   b. Voeg de volgende **hosts** gegevens toe aan het hosts-bestand:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Controleer vanaf de computer die u hebt verbonden met het virtuele Azure-netwerk dat wordt gebruikt door het HDInsight-cluster, of u de hoofdknooppunten pingen met behulp van het IP-adres en de hostnaam.

1. Gebruik SSH om verbinding te maken met het clusterhoofdknooppunt door de instructies in [Verbinding maken met een HDInsight-cluster met Behulp van SSH .](../hdinsight-hadoop-linux-use-ssh-unix.md) Ping vanuit het clusterhoofdknooppunt het IP-adres van de desktopcomputer. Test de verbinding met beide IP-adressen die aan de computer zijn toegewezen:

    - Eén voor de netwerkverbinding
    - Eén voor het virtuele Azure-netwerk

1. Herhaal de stappen voor het andere hoofdknooppunt.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Stap 4: Maak een Apache Spark Scala-toepassing met HDInsight-hulpprogramma's in Azure Toolkit voor IntelliJ en configureer deze voor foutopsporing op afstand

1. Open IntelliJ IDEA en maak een nieuw project. Voer in het dialoogvenster **Nieuw project** de volgende handelingen uit:

    ![De nieuwe projectsjabloon selecteren in IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecteer **HDInsight** > **Spark op HDInsight (Scala)**.

    b. Selecteer **Volgende**.
1. Ga in het volgende dialoogvenster **Nieuw project** als volgt te werk en selecteer **Voltooien:**

    - Geef een projectnaam en locatie op.

    - Selecteer in de vervolgkeuzelijst **Project SDK** de optie **Java 1.8** voor het Spark 2.x-cluster of selecteer **Java 1.7** voor het Spark 1.x-cluster.

    - In de vervolgkeuzelijst **Spark-versie** integreert de wizard Scala voor het maken van projecten de juiste versie voor de Spark SDK en de Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.0.2 (Scala 2.11.8)** gebruikt.
  
   ![De sdk- en Spark-versie van het project selecteren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

    a. Selecteer **Projectstructuur**in het menu **Bestand** .

    b. Selecteer **artefacten** in het dialoogvenster **Projectstructuur** om het standaardartefact weer te geven dat is gemaakt. U ook uw eigen artefact maken**+** door het plusteken te selecteren ( ).

   ![IntelliJ IDEA artefacten maken pot](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Bibliotheken toevoegen aan uw project. Ga als volgt te werk om een bibliotheek toe te voegen:

    a. Klik met de rechtermuisknop op de projectnaam in de projectstructuur en selecteer **Module-instellingen openen**.

    b. Selecteer in het dialoogvenster **Projectstructuur** **bibliotheken,****+** selecteer het ( ) symbool en selecteer **Vervolgens Uit Maven**.

    ![IntelliJ IDEA download bibliotheek](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Zoek in het dialoogvenster **Bibliotheek downloaden van De Opslagplaats van Maven** naar de volgende bibliotheken:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Kopieer `yarn-site.xml` `core-site.xml` en vanaf het clusterhoofdknooppunt en voeg ze toe aan het project. Gebruik de volgende opdrachten om de bestanden te kopiëren. U [Cygwin](https://cygwin.com/install.html) gebruiken `scp` om de volgende opdrachten uit te voeren om de bestanden van de clusterhoofdknooppunten te kopiëren:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Omdat we het IP-adres van het clusterhoofdknooppunt en hostnamen voor het `scp` hosts-bestand op het bureaublad al hebben toegevoegd, kunnen we de opdrachten op de volgende manier gebruiken:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Als u deze bestanden aan uw project wilt toevoegen, kopieert `<your project directory>\src`u ze bijvoorbeeld onder de map **/src** in uw projectstructuur.

1. Werk `core-site.xml` het bestand bij om de volgende wijzigingen aan te brengen:

   a. Vervang de versleutelde sleutel. Het `core-site.xml` bestand bevat de versleutelde sleutel voor het opslagaccount dat aan het cluster is gekoppeld. Vervang `core-site.xml` in het bestand dat u aan het project hebt toegevoegd de versleutelde sleutel door de werkelijke opslagsleutel die is gekoppeld aan het standaardopslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie .

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Verwijder de volgende `core-site.xml`vermeldingen uit :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Sla het bestand op.

1. Voeg de hoofdklasse toe voor uw toepassing. Klik in de **Project Explorer**met de rechtermuisknop op **src,** wijs **Nieuw**aan en selecteer vervolgens de **klasse Scala**.

    ![IntelliJ IDEA Selecteer de hoofdklasse](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Geef in het dialoogvenster **Nieuwe scalaklasse maken** een naam op, selecteer **Object** in het vak **Vriendelijk** en selecteer **OK**.

    ![IntelliJ IDEA Maak nieuwe Scala klasse](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. Plak `MyClusterAppMain.scala` in het bestand de volgende code. Met deze code wordt de `executeJob` Spark-context gemaakt en wordt een methode uit het `SparkSample` object geopend.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Herhaal stap 8 en 9 om `*SparkSample`een nieuw Object Scala toe te voegen, genaamd . Voeg de volgende code toe aan deze klasse. Deze code leest de gegevens van de HVAC.csv (beschikbaar in alle HDInsight Spark clusters). Hiermee worden de rijen opgehaald die slechts één cijfer in de zevende kolom in het CSV-bestand hebben, en wordt de uitvoer naar **/HVACOut** geschreven onder de standaardopslagcontainer voor het cluster.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Herhaal stap 8 en 9 om `RemoteClusterDebugging`een nieuwe klasse toe te voegen met de naam . Deze klasse implementeert het Spark-testframework dat wordt gebruikt om de toepassingen te debuggen. Voeg de volgende `RemoteClusterDebugging` code toe aan de klasse:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Er zijn een paar belangrijke dingen op te merken:

      * Zorg `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`er bijvoorbeeld voor dat de JAR voor vonkassemblage beschikbaar is op de clusteropslag op het opgegeven pad.
      * Geef `setJars`bijvoorbeeld de locatie op waar het artefact JAR is gemaakt. Typisch is `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`het.

1. Klik`*RemoteClusterDebugging` in de klasse `test` met de rechtermuisknop op het trefwoord en selecteer **Vervolgens RemoteClusterDebugging-configuratie maken**.

    ![IntelliJ IDEA Een externe configuratie maken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Geef in het dialoogvenster **Configuratie van RemoteClusterDebugging maken** een naam op voor de configuratie en selecteer Vervolgens De soort **testen** als de **naam Testen**. Laat alle andere waarden als de standaardinstellingen. Selecteer **Apply** en vervolgens **OK**.

    ![Configuratie van RemoteClusterDebugging maken](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. U ziet nu een vervolgkeuzelijst voor **configuratie op afstand** in de menubalk.

    ![IntelliJ De vervolgkeuzelijst Remote run](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Stap 5: De toepassing uitvoeren in de foutopsporingsmodus

1. Open `SparkSample.scala` in uw IntelliJ IDEA-project een `val rdd1`breekpunt naast . Selecteer **lijn in functie taak**uitvoeren in het menu **Breekpunt maken voor** pop-up .

    ![IntelliJ IDEA Voeg een breekpunt toe](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Als u de toepassing wilt uitvoeren, selecteert u de knop **Foutopsporing uitvoeren** naast de vervolgkeuzelijst **Configuratie voor extern uitvoeren.**

    ![IntelliJ IDEA Selecteer de knop Foutopsporing uitvoeren](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Wanneer de uitvoering van het programma het breekpunt bereikt, ziet u een tabblad **Foutopsporing** in het onderste deelvenster.

    ![IntelliJ IDEA Weergave het tabblad Foutopsporing](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Als u een horloge**+** wilt toevoegen, selecteert u het pictogram ( )

    ![IntelliJ debug-add-watch-variabele](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    In dit voorbeeld is de `rdd1` toepassing verbroken voordat de variabele werd gemaakt. Door dit horloge te gebruiken, kunnen we `rdd`de eerste vijf rijen in de variabele zien. Selecteer **Enter**.

    ![IntelliJ Voer het programma uit in de foutopsporingsmodus](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Wat u in de vorige afbeelding ziet, is dat u bij runtime terabytes aan gegevens opvragen en debuggen hoe uw toepassing vordert. In de uitvoer in de vorige afbeelding u bijvoorbeeld zien dat de eerste rij van de uitvoer een koptekst is. Op basis van deze uitvoer u uw toepassingscode wijzigen om de koptekstrij zo nodig over te slaan.

1. U nu het pictogram **Programma hervatten** selecteren om door te gaan met het uitvoeren van uw toepassing.

    ![IntelliJ IDEA Selecteer Cv-programma](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Als de toepassing is voltooid, ziet u de uitvoer als volgt:

    ![IntelliJ IDEA-foutopsporing console-uitvoer](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Volgende stappen

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
* [Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen op afstand te debuggen via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HdInsight-hulpprogramma's gebruiken in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Apache Zeppelin-laptops gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels beschikbaar voor Jupyter-laptop in een Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken bijhouden en debuggen die worden uitgevoerd op een Apache Spark-cluster in HDInsight](apache-spark-job-debugging.md)
