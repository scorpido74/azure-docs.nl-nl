---
title: 'Azure Toolkit voor IntelliJ: Spark-app - HDInsight'
description: Gebruik de Azure Toolkit voor IntelliJ om Spark-toepassingen te ontwikkelen die in Scala zijn geschreven en deze in te dienen bij een HDInsight Spark-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a3884fdfbbc215c305053d8615d690880f4026ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314145"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor HDInsight-cluster te maken

In dit artikel wordt uitgelegd hoe u Apache Spark-toepassingen op Azure HDInsight ontwikkelen met behulp van de Azure Toolkit-plug-in voor de IntelliJ IDE. **Azure Toolkit** [Azure HDInsight](../hdinsight-overview.md) is een beheerde, open-source analyseservice in de cloud. Met de service u open-source frameworks gebruiken, zoals Hadoop, Apache Spark, Apache Hive en Apache Kafka.

U de Azure Toolkit-plug-in op een aantal manieren gebruiken: **Azure Toolkit**

* Ontwikkel en dien een Scala Spark-toepassing in bij een HDInsight Spark-cluster.
* Toegang tot uw Azure HDInsight Spark-clusterbronnen.
* Ontwikkelen en lokaal een Scala Spark-toepassing uitvoeren.

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * De Azure Toolkit voor IntelliJ-plug-in gebruiken
> * Apache Spark-toepassingen ontwikkelen
> * Een toepassing verzenden naar azure HDInsight-cluster

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Dit artikel maakt gebruik van Java versie 8.0.202.

* IntelliJ IDEA. Dit artikel maakt gebruik van [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure-toolkit voor IntelliJ.  Zie [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (De Azure Toolkit voor IntelliJ installeren).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala-invoegtoepassing voor IntelliJ IDEA installeren

Stappen om de Scala-plug-in te installeren:

1. Open IntelliJ IDEA.

2. Navigeer in het welkomstscherm naar**Plug-ins** **configureren** > om het venster **Plug-ins** te openen.

    ![IntelliJ IDEA maakt scala plugin mogelijk](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selecteer **Install** voor de Scala-invoegtoepassing die in het nieuwe venster wordt weergegeven.  

    ![IntelliJ IDEA installeert scala plugin](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Als de invoegtoepassing is geïnstalleerd, moet u de IDE opnieuw starten.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Een Spark Scala-toepassing maken voor een HDInsight Spark-cluster

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

2. Selecteer **Azure Spark/HDInsight** in het linkerdeelvenster.

3. Selecteer **Spark Project (Scala)** in het hoofdvenster.

4. Selecteer in de vervolgkeuzelijst **Gereedschap bouwen** een van de volgende opties:
   * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

     ![Dialoogvenster IntelliJ IDEA New Project](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecteer **Volgende**.

6. Geef in het venster **New project** de volgende gegevens op:  

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Projectnaam| Voer een naam in.  In dit `myApp`artikel wordt gebruik gebruikt van .|  
    |Project&nbsp;location| Voer de locatie in om uw project op te slaan.|
    |Project SDK| Dit veld kan leeg zijn bij uw eerste gebruik van IDEA.  Selecteer **New...** en ga naar uw JDK.|
    |Spark version|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

    ![De Apache Spark SDK selecteren](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecteer **Finish**.  Het kan enkele minuten duren voordat het project beschikbaar is.

8. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

   a. Navigeer in de menubalk naar > **Bestandsprojectstructuur...**. **File**

   b. Selecteer **Artefacten**in het venster **Projectstructuur** .  

   c. Selecteer **Annuleren** na het bekijken van het artefact.

      ![Artefact-gegevens in het dialoogvenster](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Voeg de broncode van uw toepassing toe door de volgende stappen uit te voeren:

    a. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala**.  

    b. Klik met de rechtermuisknop op **de scala**en navigeer vervolgens naar **de nieuwe** > **scalaklasse**.

   ![Opdrachten voor het maken van een Klasse Scala vanuit Project](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Geef in het dialoogvenster **Nieuwe scalaklasse maken** een naam op, selecteer **Object** in de vervolgkeuzelijst **Vriendelijk** en selecteer **OK**.

     ![Dialoogvenster Nieuwe Scala-klasse maken](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Het **bestand myApp.scala** wordt vervolgens in de hoofdweergave geopend. Vervang de standaardcode door de onderstaande code:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    De code leest de gegevens van HVAC.csv (beschikbaar op alle HDInsight Spark-clusters), haalt de rijen op die `/HVACOut` slechts één cijfer in de zevende kolom in het CSV-bestand hebben en schrijft de uitvoer naar onder de standaardopslagcontainer voor het cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Verbinding maken met uw HDInsight-cluster

De gebruiker kan [zich aanmelden bij een Azure-abonnement](#sign-in-to-your-azure-subscription)of een [HDInsight-cluster koppelen.](#link-a-cluster) Gebruik de Ambari gebruikersnaam/wachtwoord of domein samengevoegde referentie om verbinding te maken met uw HDInsight-cluster.

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Navigeer in de menubalk naar**Windows** > **Azure Explorer** **weergeven.** > 

   ![IntelliJ IDEA toont azure explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Klik in Azure Explorer met de rechtermuisknop op het **Azure-knooppunt** en selecteer **Aanmelden**.

   ![IntelliJ IDEA explorer klik met de rechtermuisknop op azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Kies **in** het dialoogvenster Aanmelden voor Azure de optie **Apparaataanmelding**en selecteer **Aanmelden**.

    !['IntelliJ IDEA azure login device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Klik in het dialoogvenster **Aanmelding van Azure-apparaten** op **Kopiëren&openen**.

   !['IntelliJ IDEA azure device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Plak de code in de browserinterface en klik op **Volgende.**

   !['Microsoft voert codedialoogvenster voor HDI in'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Voer uw Azure-referenties in en sluit de browser.

   !['Microsoft voert e-maildialoog in voor HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Nadat u bent aangemeld, bevat het dialoogvenster **Abonnementen selecteren** alle Azure-abonnementen die aan de referenties zijn gekoppeld. Selecteer uw abonnement en selecteer vervolgens de knop **Selecteren.**

    ![Het dialoogvenster Abonnementen selecteren](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Vouw **HDInsight** uit vanuit **Azure Explorer**om de HDInsight Spark-clusters in uw abonnementen weer te geven.

    ![IntelliJ IDEA Azure Explorer hoofdweergave](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Als u de resources (bijvoorbeeld opslagaccounts) wilt weergeven die aan het cluster zijn gekoppeld, u een knooppunt met clusternaam verder uitbreiden.

    ![Azure Explorer-opslagaccounts](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Een cluster koppelen

U een HDInsight-cluster koppelen met de door Apache Ambari beheerde gebruikersnaam. Op dezelfde manier u voor een HDInsight-cluster met een domein `user1@contoso.com`koppelen met behulp van het domein en de gebruikersnaam, zoals. Ook u livy service cluster koppelen.

1. Navigeer in de menubalk naar**Windows** > **Azure Explorer** **weergeven.** > 

1. Klik in Azure Explorer met de rechtermuisknop op het **HDInsight-knooppunt** en selecteer **Een cluster koppelen**.

   ![Contextmenu Azure Explorer-koppelingscluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. De beschikbare opties in het venster **A koppelen** zijn afhankelijk van de waarde die u selecteert in de vervolgkeuzelijst **Koppelingresourcetype.**  Voer uw waarden in en selecteer **OK**.

    * **HDInsight-cluster**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Brontype koppelen|Selecteer **HDInsight-cluster** in de vervolgkeuzelijst.|
        |Clusternaam/URL| Voer de clusternaam in.|
        |Verificatietype| Vertrekken als **basisverificatie**|
        |Gebruikersnaam| Voer de gebruikersnaam van het cluster in, standaard is beheerder.|
        |Wachtwoord| Voer een wachtwoord in voor de gebruikersnaam.|

        ![IntelliJ IDEA koppeling een cluster dialoogvenster](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy Service**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Brontype koppelen|Selecteer **Livy Service** in de vervolgkeuzelijst.|
        |Livy Eindpunt| Voer Livy-eindpunt in|
        |Clusternaam| Voer de clusternaam in.|
        |Gareneindpunt|Optioneel.|
        |Verificatietype| Vertrekken als **basisverificatie**|
        |Gebruikersnaam| Voer de gebruikersnaam van het cluster in, standaard is beheerder.|
        |Wachtwoord| Voer een wachtwoord in voor de gebruikersnaam.|

        ![Dialoogvenster IntelliJ IDEA-koppeling Livy-cluster](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. U uw gekoppelde cluster bekijken vanaf het **HDInsight-knooppunt.**

   ![Azure Explorer gekoppeld cluster1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. U ook de koppeling van een cluster loskoppelen van **Azure Explorer.**

   ![Azure Explorer-niet-gekoppeld cluster](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Een Spark Scala-toepassing uitvoeren op een HDInsight Spark-cluster

Nadat u een Scala-toepassing hebt aanmaken, u deze indienen bij het cluster.

1. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala** > **myApp**.  Klik met de rechtermuisknop op **myApp**en selecteer **Spark-toepassing verzenden** (deze wordt waarschijnlijk onder aan de lijst geplaatst).

      ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Selecteer 1 in het dialoogvenster **Spark-toepassing** **verzenden. Vonk op HDInsight**.

3. Geef in het **configuratievenster Bewerken** de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |Spark clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u uw toepassing wilt uitvoeren.|
    |Selecteer een artefact dat u wilt verzenden|Laat de standaardinstelling.|
    |Naam van de hoofdklasse|De standaardwaarde is de hoofdklasse van het geselecteerde bestand. U de klasse wijzigen door de ellips te**selecteren...**  en het kiezen van een andere klasse.|
    |Taakconfiguraties|U de standaardtoetsen en- of waarden wijzigen. Zie [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)voor meer informatie.|
    |Opdrachtregelargumenten|U argumenten invoeren die worden gescheiden door ruimte voor de hoofdklasse indien nodig.|
    |Verwezen potten en gerefereerde bestanden|U de paden voor de eventuele jars en bestanden waarnaar wordt verwezen, invoeren. U ook door bestanden bladeren in het virtuele bestandssysteem van Azure, dat momenteel alleen het ADLS Gen 2-cluster ondersteunt. Voor meer informatie: [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zie [ook, Hoe u bronnen uploaden naar cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Opslag voor het uploaden van vacatures|Uitvouwen om extra opties te onthullen.|
    |Opslagtype|Selecteer **Azure Blob gebruiken om te uploaden** vanuit de vervolgkeuzelijst.|
    |Opslagaccount|Voer uw opslagaccount in.|
    |Opslagsleutel|Voer uw opslagsleutel in.|
    |Opslagcontainer|Selecteer uw opslagcontainer in de vervolgkeuzelijst zodra **opslagaccount** en **opslagsleutel** zijn ingevoerd.|

    ![Het dialoogvenster Spark-indiening](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecteer **SparkJobRun** om uw project naar het geselecteerde cluster te verzenden. Op het tabblad **Externe vonktaak op het** tabblad Cluster wordt de voortgang van de uitvoering van taken onderin weergegeven. U de toepassing stoppen door op de rode knop te klikken.

    ![Venster Apache Spark-indiening](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Apache Spark-toepassingen lokaal of op afstand debuggen op een HDInsight-cluster

We raden ook een andere manier aan om de Spark-toepassing bij het cluster in te dienen. U dit doen door de parameters in te stellen in de **IDE-configuratie-iDE-configuratie uitvoeren/foutopsporing.** Zie [Apache Spark-toepassingen lokaal of op afstand debuggen op een HDInsight-cluster met Azure Toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-clusters openen en beheren met Azure Toolkit voor IntelliJ

U verschillende bewerkingen uitvoeren met Azure Toolkit voor IntelliJ.  De meeste bewerkingen worden gestart vanuit **Azure Explorer.**  Navigeer in de menubalk naar**Windows** > **Azure Explorer** **weergeven.** > 

### <a name="access-the-job-view"></a>Toegang tot de taakweergave

1. Navigeer vanuit Azure Explorer naar **HDInsight** > \<Your Cluster> > **Jobs.**

    ![IntelliJ Azure Explorer-taakweergaveknooppunt](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. In het rechterdeelvenster worden op het tabblad **Spark-taakweergave** alle toepassingen weergegeven die op het cluster zijn uitgevoerd. Selecteer de naam van de toepassing waarvoor u meer details wilt zien.

    ![Gegevens over de functieweergave van spark](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Als u basistaakgegevens wilt weergeven, geeft u de plaats boven de taakgrafiek. Als u de grafiek met fasen en informatie wilt weergeven die elke taak genereert, selecteert u een knooppunt in de taakgrafiek.

    ![Details van de taakweergave van spark](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Als u veelgebruikte logboeken wilt weergeven, zoals *Driver Stderr,* *Driver Stdout*en *Directory Info,* selecteert u het tabblad **Logboek.**

    ![Spark Job View Log details](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. U de Spark-geschiedenis-gebruikersinterface en de YARN-gebruikersinterface bekijken (op toepassingsniveau). Selecteer een koppeling boven aan het venster.

### <a name="access-the-spark-history-server"></a>Toegang tot de Spark-geschiedenisserver

1. Vouw **hdinsight**uit in Azure Explorer, klik met de rechtermuisknop op de naam spark-cluster en selecteer vervolgens **de gebruikersinterface van Spark-geschiedenis openen**.  
2. Wanneer u wordt gevraagd, voert u de beheerdersreferenties van het cluster in, die u hebt opgegeven toen u het cluster instelt.

3. Op het dashboard van de Spark-geschiedenisserver u de naam van de toepassing gebruiken om te zoeken naar de toepassing die u zojuist hebt uitgevoerd. In de vorige code stelt u de `val conf = new SparkConf().setAppName("myApp")`toepassingsnaam in met behulp van . Uw Spark-toepassingsnaam is **myApp**.

### <a name="start-the-ambari-portal"></a>Start de Ambari-portal

1. Vouw **hdinsight**uit in Azure Explorer, klik met de rechtermuisknop op de naam spark-cluster en selecteer **Vervolgens Clusterbeheerportal (Ambari)** openen.  

2. Wanneer u wordt gevraagd, voert u de beheerdersreferenties voor het cluster in. U hebt deze referenties opgegeven tijdens het clusterinstallatieproces.

### <a name="manage-azure-subscriptions"></a>Azure-abonnementen beheren

Standaard worden in Azure Toolkit voor IntelliJ de Spark-clusters van al uw Azure-abonnementen weergegeven. Indien nodig u de abonnementen opgeven die u wilt openen.  

1. Klik in Azure Explorer **Azure** met de rechtermuisknop op het Azure-hoofdknooppunt en selecteer **Vervolgens Abonnementen selecteren**.  

2. Schakel **in** het venster Abonnementen selecteren de selectievakjes uit naast de abonnementen die u niet wilt openen en selecteer **Sluiten**.

## <a name="spark-console"></a>Spark-console

U Spark Local Console(Scala) uitvoeren of de interactieve sessieconsole Spark Livy (Scala) uitvoeren.

### <a name="spark-local-consolescala"></a>Lokale console spark(Scala)

Zorg ervoor dat je tevreden bent met de WINUTILS. EXE-voorwaarde.

1. Navigeer in de menubalk naar > **Configuraties** **bewerken...**.

2. Navigeer in het venster **Configuratie van uitvoeren/debuggen** in het linkerdeelvenster naar **Apache Spark op HDInsight** > **[Spark on HDInsight] myApp**.

3. Selecteer in het hoofdvenster het **`Locally Run`** tabblad.

4. Geef de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |De belangrijkste klasse van de baan|De standaardwaarde is de hoofdklasse van het geselecteerde bestand. U de klasse wijzigen door de ellips te**selecteren...**  en het kiezen van een andere klasse.|
    |Omgevingsvariabelen|Zorg ervoor dat de waarde voor HADOOP_HOME juist is.|
    |WINUTILS.exe-locatie|Zorg ervoor dat het pad correct is.|

    ![Configuratie van lokale consoleset](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala** > **myApp**.  

6. Navigeer in de menubalk naar **Tools** > **Spark Console** > **Run Spark Local Console(Scala).**

7. Vervolgens kunnen twee dialoogvensters worden weergegeven om u te vragen of u afhankelijkheden automatisch wilt oplossen. Selecteer dan **Automatisch oplossen**.

    ![IntelliJ IDEA Spark Auto Fix, dialoogvenster1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix dialoogvenster2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. De console moet er hetzelfde uitzien als de onderstaande afbeelding. Druk in het `sc.appName`type consolevenster en druk op ctrl+Enter.  Het resultaat wordt getoond. U de lokale console beëindigen door op de rode knop te klikken.

    ![IntelliJ IDEA lokale console resultaat](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactieve Sessie Console(Scala)

1. Navigeer in de menubalk naar > **Configuraties** **bewerken...**.

2. Navigeer in het venster **Configuratie van uitvoeren/debuggen** in het linkerdeelvenster naar **Apache Spark op HDInsight** > **[Spark on HDInsight] myApp**.

3. Selecteer in het hoofdvenster het **`Remotely Run in Cluster`** tabblad.

4. Geef de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |Spark clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u uw toepassing wilt uitvoeren.|
    |Naam van de hoofdklasse|De standaardwaarde is de hoofdklasse van het geselecteerde bestand. U de klasse wijzigen door de ellips te**selecteren...**  en het kiezen van een andere klasse.|

    ![Configuratie van interactieve consoleset](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala** > **myApp**.  

6. Navigeer in de menubalk naar **Tools** > **Spark Console** > **Run Spark Livy Interactive Session Console(Scala).**

7. De console moet er hetzelfde uitzien als de onderstaande afbeelding. Druk in het `sc.appName`type consolevenster en druk op ctrl+Enter.  Het resultaat wordt getoond. U de lokale console beëindigen door op de rode knop te klikken.

    ![IntelliJ IDEA Interactieve Console Resultaat](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Selectie verzenden naar Spark-console

Het is handig voor u om het scriptresultaat te voorzien door een code te verzenden naar de lokale console of Livy Interactive Session Console (Scala). U bepaalde code markeren in het Scala-bestand en vervolgens met de rechtermuisknop **selectie naar sparkconsole verzenden**. De geselecteerde code wordt naar de console verzonden. Het resultaat wordt weergegeven na de code in de console. De console controleert de fouten als deze bestaan.  

   ![Selectie verzenden naar Spark-console](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integreren met HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Maak verbinding met uw HDInsight ESP-cluster met ID Broker (HIB)

U de normale stappen volgen om u aan te melden bij een Azure-abonnement om verbinding te maken met uw HDInsight ESP-cluster met ID Broker (HIB). Na het aanmelden ziet u de clusterlijst in Azure Explorer. Zie [Verbinding maken met uw HDInsight-cluster voor](#connect-to-your-hdinsight-cluster)meer instructies.

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Een Spark Scala-toepassing uitvoeren op een HDInsight ESP-cluster met ID Broker (HIB)

U de normale stappen volgen om de taak in te dienen bij hdinsight ESP-cluster met ID Broker (HIB). Raadpleeg [Een Spark Scala-toepassing uitvoeren op een HDInsight Spark-cluster](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) voor meer instructies.

We uploaden de benodigde bestanden naar een map met de naam van uw aanmeldingsaccount en u het uploadpad in het configuratiebestand zien.

   ![uploadpad in de configuratie](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-console op een HDInsight ESP-cluster met ID Broker (HIB)

U Spark Local Console(Scala) uitvoeren of Spark Livy Interactive Session Console (Scala) uitvoeren op een HDInsight ESP-cluster met ID Broker (HIB). Raadpleeg [Spark Console](#spark-console) voor meer instructies.

   > [!NOTE]  
   > Voor het HDInsight ESP-cluster met Id Broker (HIB) [koppelt u een cluster](#link-a-cluster) en [debugt Apache Spark-toepassingen op afstand](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) momenteel niet.

## <a name="reader-only-role"></a>Rol met alleen voor lezers

Wanneer gebruikers taak indienen bij een cluster met alleen toestemming voor een leesfunctie, is Ambari-referenties vereist.

### <a name="link-cluster-from-context-menu"></a>Koppelingscluster vanuit contextmenu

1. Meld u aan met een account met alleen een lezer.

2. Vouw **HDInsight** uit vanuit **Azure Explorer**om HDInsight-clusters in uw abonnement weer te geven. De clusters met de vermelding **"Rol:Reader"** hebben alleen toestemming voor alleen een lezerrol.

    !['IntelliJ Azure Explorer-rol:Reader'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Klik met de rechtermuisknop op het cluster met alleen voor lezers machtiging. Selecteer **Dit cluster koppelen** in het contextmenu om het cluster te koppelen. Voer de gebruikersnaam en het wachtwoord van Ambari in.

    ![IntelliJ Azure Explorer koppelt dit cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Als het cluster is gekoppeld, wordt HDInsight vernieuwd.
   De fase van het cluster wordt gekoppeld.
  
    ![IntelliJ Azure Explorer gekoppeld dialoogvenster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Koppelingscluster door het knooppunt Vacatures uit te breiden

1. Klik op het knooppunt **Vacatures,** het venster **Toegang tot clustertaak geweigerd** wordt weergegeven.

2. Klik **op Dit cluster koppelen** om het cluster te koppelen.

    ![dialoogvenster toegang tot clustertaak geweigerd](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Koppelingscluster vanuit het venster Configuratievan uitvoeren/foutopsporing

1. Maak een HDInsight-configuratie. Selecteer vervolgens **Extern uitvoeren in cluster**.

2. Selecteer een cluster met alleen voor de lezer staande rolmachtigingen voor **Spark-clusters (alleen Linux).** Waarschuwingsbericht verschijnt. U op **Dit cluster koppelen** klikken om het cluster te koppelen.

   ![IntelliJ IDEA run/debug configuratie maken](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Opslagaccounts weergeven

* Voor clusters met alleen voor lezers rollen, klikt u op **Knooppunt Opslagaccounts,** het venster **Geweigerde opslagtoegang** verschijnt. U op **Azure Storage Explorer openen** klikken om Storage Explorer te openen.

   !['IntelliJ IDEA-toegang tot opslagruimte geweigerd'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Knop IntelliJ IDEA-opslagtoegang geweigerd](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Klik voor gekoppelde clusters op **knooppunt Opslagaccounts,** het venster **Geweigerde opslagtoegang** wordt weergegeven. U op **Azure Storage openen** klikken om Storage Explorer te openen.

   !['IntelliJ IDEA Storage Access Denied2'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Knop IntelliJ IDEA-opslagtoegang denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Bestaande IntelliJ IDEA-toepassingen converteren naar Azure Toolkit voor IntelliJ

U de bestaande Spark Scala-toepassingen die u in IntelliJ IDEA hebt gemaakt, converteren naar Azure Toolkit voor IntelliJ. U de plug-in vervolgens gebruiken om de toepassingen in te dienen bij een HDInsight Spark-cluster.

1. Open het bijbehorende `.iml` bestand voor een bestaande Spark Scala-toepassing die is gemaakt via IntelliJ IDEA.

2. Op basisniveau is een **module-element** zoals de volgende tekst:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   Bewerk het element `UniqueKey="HDInsightTool"` dat u wilt toevoegen, zodat het **module-element** op de volgende tekst lijkt:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Sla de wijzigingen op. Uw toepassing moet nu compatibel zijn met Azure Toolkit voor IntelliJ. U het testen door met de rechtermuisknop op de projectnaam in Project te klikken. Het pop-upmenu heeft nu de optie **Spark-toepassing verzenden naar HDInsight**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u het cluster dat u met de volgende stappen hebt gemaakt:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die worden weergegeven de **...** naast het cluster dat u voor dit artikel hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

![Azure-portal verwijdert HDInsight-cluster](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de Azure Toolkit voor IntelliJ-plug-in gebruiken om Apache Spark-toepassingen te ontwikkelen die zijn geschreven in [Scala.](https://www.scala-lang.org/) Vervolgens diende ze rechtstreeks vanuit de geïntegreerde ontwikkelomgeving van IntelliJ (IDE) in bij een HDInsight Spark-cluster. Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Apache Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Apache Spark-gegevens analyseren met Power BI](apache-spark-use-bi-tools.md)
