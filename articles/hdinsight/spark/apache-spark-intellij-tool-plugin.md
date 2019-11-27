---
title: 'Zelf studie-Azure-toolkit voor IntelliJ: Spark-app-HDInsight'
description: 'Zelf studie: gebruik de Azure-toolkit voor IntelliJ voor het ontwikkelen van Spark-toepassingen die zijn geschreven in scala en verzend deze naar een HDInsight Spark-cluster.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: b417823d0ec7ed838186d53c1bb25400a148e0e9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533921"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Zelf studie: Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken

In deze zelf studie ziet u hoe u de Azure-toolkit voor IntelliJ-invoeg toepassing gebruikt om Apache Spark toepassingen te ontwikkelen die in [scala](https://www.scala-lang.org/)zijn geschreven en deze vervolgens rechtstreeks vanuit de IntelliJ Integrated Development Environment (IDE) naar een HDInsight Spark-cluster te verzenden. U kunt de invoeg toepassing op een paar manieren gebruiken:

* Ontwikkel en verzend een scala Spark-toepassing op een HDInsight Spark-cluster.
* Toegang tot uw Azure HDInsight Spark cluster resources.
* Ontwikkel en voer lokaal een scala Spark-toepassing uit.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Azure-toolkit voor IntelliJ-invoeg toepassing gebruiken
> * Apache Spark-toepassingen ontwikkelen
> * Toepassing verzenden naar Azure HDInsight-cluster

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  In deze zelfstudie wordt gebruikgemaakt van Java-versie 8.0.202.

* IntelliJ-idee. In dit artikel wordt gebruikgemaakt van [IntelliJ-idee Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure-toolkit voor IntelliJ.  Zie [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (De Azure Toolkit voor IntelliJ installeren).

## <a name="install-scala-plugin-for-intellij-idea"></a>Scala-invoegtoepassing voor IntelliJ IDEA installeren

Voer de volgende stappen uit om de Scala-invoegtoepassing te installeren:

1. Open IntelliJ IDEA.

2. Ga op het welkomstscherm naar **Configure** > **Plugins** om het venster **Plugins** te openen.

    ![IntelliJ-idee scala-invoeg toepassing inschakelen](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Select **Install** voor de Scala-invoegtoepassing die in het nieuwe venster wordt weergegeven.  

    ![IntelliJ-idee scala-invoeg toepassing installeren](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Als de invoegtoepassing is geïnstalleerd, moet u de IDE opnieuw starten.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Een Spark scala-toepassing maken voor een HDInsight Spark-cluster

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.

2. Selecteer **Azure Spark/HDInsight** in het linkerdeelvenster.

3. Selecteer **Spark Project (Scala)** in het hoofdvenster.

4. Selecteer in de vervolgkeuzelijst **Build tool** een van de volgende opties:
   * **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   * **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

     ![Het dialoog venster IntelliJ-idee nieuw project](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecteer **Next**.

6. Geef in het venster **New project** de volgende gegevens op:  

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Projectnaam| Voer een naam in.  In deze zelfstudie wordt `myApp` gebruikt.|  
    |Project&nbsp;location| Voer de gewenste locatie in om uw project in op te slaan.|
    |Project SDK| Dit kan leeg zijn bij het eerste gebruik van het idee.  Selecteer **New...** en ga naar uw JDK.|
    |Spark version|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Selecteer **Spark 1.x** als de Spark-clusterversie ouder is dan 2.0. Selecteer anders **Spark 2.x**. In dit voorbeeld wordt **Spark 2.3.0 (Scala 2.11.8)** gebruikt.|

    ![De Apache Spark SDK selecteren](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecteer **Finish**.  Het kan enkele minuten duren voordat het project beschikbaar wordt.

8. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

   a. Navigeer in de menu balk naar **bestand** > **project structuur...** .

   b. Selecteer **artefacten**in het venster **project structuur** .  

   c. Selecteer **Annuleren** nadat u het artefact hebt weer gegeven.

      ![Artefact gegevens in het dialoog venster](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Voeg de bron code van uw toepassing toe door de volgende handelingen uit te voeren:

    a. Ga vanuit Project naar **mijntoep** > **src** > **Main** > **scala**.  

    b. Klik met de rechter muisknop op **scala**en navigeer vervolgens naar de **nieuwe** > **scala-klasse**.

   ![Opdrachten voor het maken van een scala-klasse van project](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Geef in het dialoog venster **nieuwe scala-klasse maken** een naam op, selecteer **object** in de vervolg keuzelijst **type** en selecteer vervolgens **OK**.

     ![Het dialoog venster nieuwe scala-klasse maken](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Het bestand **MyApp. scala** wordt vervolgens in de hoofd weergave geopend. Vervang de standaard code door de code die hieronder wordt gevonden:  

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

    De code leest de gegevens van HVAC. CSV (beschikbaar op alle HDInsight Spark-clusters), haalt de rijen met slechts één cijfer in de zevende kolom in het CSV-bestand op en schrijft de uitvoer naar `/HVACOut` onder de standaard opslag container voor het cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Verbinding maken met uw HDInsight-cluster
De gebruiker kan [zich aanmelden bij het Azure-abonnement](#sign-in-to-your-azure-subscription)of [een HDInsight-cluster koppelen](#link-a-cluster) met behulp van Ambari gebruikers naam/wacht woord of referenties die lid zijn van een domein om verbinding te maken met uw HDInsight-cluster.

### <a name="sign-in-to-your-azure-subscription"></a>Meld u aan bij uw Azure-abonnement

1. Ga in de menu balk **naar > ** **hulp programma Windows** > **Azure Verkenner**.

   ![IntelliJ-idee Azure Explorer weer geven](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Klik in azure Verkenner met de rechter muisknop op het **Azure** -knoop punt en selecteer **Aanmelden**.

   ![IntelliJ-ideeën Verkenner met de rechter muisknop op Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Kies **apparaat aanmelden**in het dialoog venster **Azure-aanmelding** en selecteer **Aanmelden**.

    ![Aanmelding bij IntelliJ voor Azure-aanmelding bij apparaat](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Klik in het dialoog venster **Aanmelden bij Azure-apparaat** op **kopiëren & openen**.

   ![IntelliJ-idee Azure-apparaat aanmelden](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Plak de code in de browser interface en klik vervolgens op **volgende**.

   ![Het dialoog venster Code van micro soft invoeren voor HDI](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Voer uw Azure-referenties in en sluit de browser.

   ![Micro soft e-mail dialoog venster voor HDI invoeren](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Nadat u bent aangemeld, worden in het dialoog venster **abonnementen selecteren** een lijst weer gegeven met alle Azure-abonnementen die zijn gekoppeld aan de referenties. Selecteer uw abonnement en selecteer vervolgens de knop **selecteren** .

    ![Het dialoog venster abonnementen selecteren](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Vouw in **Azure Verkenner** **hdinsight** uit om de hdinsight Spark-clusters in uw abonnementen weer te geven.

    ![Hoofd weergave van IntelliJ-idee Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Als u de resources wilt weer geven (bijvoorbeeld opslag accounts) die aan het cluster zijn gekoppeld, kunt u een cluster naam knooppunt verder uitbreiden.

    ![Azure Explorer-opslag accounts](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Een cluster koppelen

U kunt een HDInsight-cluster koppelen met behulp van de Apache Ambari Managed username. Op dezelfde manier kunt u voor een HDInsight-cluster dat is gekoppeld aan een domein een koppeling maken met behulp van het domein en de gebruikers naam, zoals `user1@contoso.com`. U kunt ook een livy-service cluster koppelen.

1. Ga in de menu balk **naar > ** **hulp programma Windows** > **Azure Verkenner**.

1. Klik in azure Verkenner met de rechter muisknop op het **HDInsight** -knoop punt en selecteer vervolgens **koppelen van een cluster**.

   ![Context menu van Azure Explorer-koppelings cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. De beschik bare opties in de **koppeling een cluster** venster variëren, afhankelijk van welke waarde u selecteert in de vervolg keuzelijst **resource type koppelen** .  Voer uw waarden in en selecteer **OK**.

    * **HDInsight-cluster**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Resource type voor koppeling|Selecteer **HDInsight-cluster** in de vervolg keuzelijst.|
        |Cluster naam/URL| Voer de cluster naam in.|
        |Verificatie type| Als **basis verificatie** laten staan|
        |Gebruikersnaam| Voer de gebruikers naam van het cluster in, de standaard instelling is admin.|
        |Wachtwoord| Voer het wacht woord in voor de gebruikers naam.|

        ![IntelliJ-idee een cluster dialoogvenster koppelen](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy-service**  
  
        |Eigenschap |Waarde |
        |----|----|
        |Resource type voor koppeling|Selecteer **livy-service** in de vervolg keuzelijst.|
        |Livy-eind punt| Livy-eind punt invoeren|
        |Clusternaam| Voer de cluster naam in.|
        |Eind punt van garen|Optioneel.|
        |Verificatie type| Als **basis verificatie** laten staan|
        |Gebruikersnaam| Voer de gebruikers naam van het cluster in, de standaard instelling is admin.|
        |Wachtwoord| Voer het wacht woord in voor de gebruikers naam.|

        ![Dialoog venster IntelliJ-idee livy-cluster](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. U kunt het gekoppelde cluster bekijken vanuit het **HDInsight** -knoop punt.

   ![Gekoppelde cluster1 van Azure Verkenner](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. U kunt ook een cluster ontkoppelen van **Azure Explorer**.

   ![Een cluster dat is gekoppeld aan Azure Verkenner](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Een Spark scala-toepassing uitvoeren op een HDInsight Spark-cluster

Nadat u een scala-toepassing hebt gemaakt, kunt u deze verzenden naar het cluster.

1. Ga vanuit Project naar **mijntoep** > **src** > **Main** > **scala** > **Mijntoep**.  Klik met de rechter muisknop op **MyApp**en selecteer vervolgens **Spark-toepassing verzenden** (deze staat waarschijnlijk onder aan de lijst).

      ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Selecteer in het dialoog venster **Spark-toepassing verzenden** de optie **1. Spark in HDInsight**.

3. Geef in het venster **configuratie bewerken** de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u de toepassing wilt uitvoeren.|
    |Een artefact selecteren om in te dienen|De standaard instelling ongewijzigd.|
    |Naam van hoofd klasse|De standaard waarde is de hoofd klasse van het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken ( **...** ) te selecteren.  en kies een andere klasse.|
    |Taak configuraties|U kunt de standaard sleutels en/of-waarden wijzigen. Zie [Apache Livy rest API](https://livy.incubator.apache.org./docs/latest/rest-api.html)voor meer informatie.|
    |Opdracht regel argumenten|Als dat nodig is, kunt u argumenten door geven die worden gescheiden door de ruimte voor de hoofd klasse.|
    |Potten waarnaar wordt verwezen en waarnaar wordt verwezen|U kunt de paden voor de potten en bestanden waarnaar wordt verwezen, opgeven. U kunt ook bladeren door bestanden in het virtuele bestands systeem van Azure, dat momenteel alleen ondersteuning biedt voor ADLS gen 2-cluster. Voor meer informatie: [Apache Spark configuratie](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zie ook [How to upload resources to cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Upload opslag van taken|Uitvouwen om extra opties weer te geven.|
    |Opslag type|Selecteer **Azure-Blob gebruiken om te uploaden** uit de vervolg keuzelijst.|
    |Opslagaccount|Voer uw opslag account in.|
    |Opslag sleutel|Voer uw opslag sleutel in.|
    |Opslag container|Selecteer uw opslag container in de vervolg keuzelijst zodra het **opslag account** en de **opslag sleutel** zijn ingevoerd.|

    ![Het dialoog venster voor de verzen ding van Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecteer **SparkJobRun** om uw project naar het geselecteerde cluster te verzenden. Op de **externe Spark-taak in** het tabblad cluster wordt de voortgang van de taak uitvoering onderaan weer gegeven. U kunt de toepassing stoppen door te klikken op de knop rood. Zie de sectie ' HDInsight Spark-clusters openen en beheren met Azure-toolkit voor IntelliJ ' verderop in dit artikel voor meer informatie over het openen van de taak uitvoer.  

    ![Inzendings venster Apache Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Fouten opsporen Apache Spark toepassingen lokaal of op afstand op een HDInsight-cluster

We raden ook een andere manier aan om de Spark-toepassing naar het cluster te verzenden. U kunt dit doen door de para meters in te stellen op de IDE voor **uitvoeren/fouten opsporen** . Zie voor meer informatie [fouten opsporen Apache Spark toepassingen lokaal of op afstand op een HDInsight-cluster met Azure-Toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>HDInsight Spark-clusters openen en beheren met behulp van Azure-toolkit voor IntelliJ

U kunt verschillende bewerkingen uitvoeren met behulp van Azure-toolkit voor IntelliJ.  De meeste bewerkingen worden gestart vanuit **Azure Verkenner**.  Ga in de menu balk **naar > ** **hulp programma Windows** > **Azure Verkenner**.

### <a name="access-the-job-view"></a>De taak weergave openen

1. Ga in azure Verkenner naar **HDInsight** > \<uw Cluster > > **taken**.

    ![IntelliJ Azure Explorer-taak weergave knooppunt](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. In het rechterdeel venster worden op het tabblad **Spark-taak weergave** alle toepassingen weer gegeven die op het cluster zijn uitgevoerd. Selecteer de naam van de toepassing waarvoor u meer details wilt weer geven.

    ![Details van de toepassing Spark-taak weergave](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Beweeg de muis aanwijzer over de taak grafiek om de informatie over elementaire actieve taken weer te geven. Selecteer een knoop punt in de taak grafiek om de fasen diagram en informatie te bekijken die elke taak genereert.

    ![Details taak fase Spark-taak weergave](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Als u veelgebruikte logboeken wilt weer geven, zoals *stuur programma stderr*, *stuur programma stdout*en *Directory gegevens*, selecteert u het tabblad **logboek** .

    ![Logboek Details van Spark-taak weergave](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. U kunt ook de gebruikers interface van Spark en de gebruikers interface van het garen weer geven (op toepassings niveau) door boven aan het venster een koppeling te selecteren.

### <a name="access-the-spark-history-server"></a>Toegang tot de Spark-geschiedenis server

1. Vouw in azure Explorer **HDInsight**uit, klik met de rechter muisknop op de naam van uw Spark-cluster en selecteer vervolgens **openen Spark-gebruikers interface**.  
2. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties van het cluster in, die u hebt opgegeven bij het instellen van het cluster.

3. Op het Spark-geschiedenis server dashboard kunt u de naam van de toepassing gebruiken om te zoeken naar de toepassing die u zojuist hebt uitgevoerd. In de voor gaande code stelt u de naam van de toepassing in met behulp van `val conf = new SparkConf().setAppName("myApp")`. Daarom is de Spark-toepassings naam **Mijntoep**.

### <a name="start-the-ambari-portal"></a>De Ambari-Portal starten

1. Vouw in azure Explorer **HDInsight**uit, klik met de rechter muisknop op de naam van uw Spark-cluster en selecteer vervolgens **cluster Beheerportal openen (Ambari)** .  

2. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties voor het cluster in. U hebt deze referenties opgegeven tijdens het installatie proces van het cluster.

### <a name="manage-azure-subscriptions"></a>Azure-abonnementen beheren

Azure-toolkit voor IntelliJ worden standaard de Spark-clusters uit al uw Azure-abonnementen weer gegeven. Indien nodig kunt u de abonnementen opgeven die u wilt gebruiken.  

1. Klik in azure Verkenner met de rechter muisknop op het **Azure** -hoofd knooppunt en selecteer vervolgens **abonnementen selecteren**.  

2. Schakel in het venster **abonnementen selecteren** de selectie vakjes uit naast de abonnementen die u niet wilt openen en selecteer vervolgens **sluiten**.

## <a name="spark-console"></a>Spark-console

U kunt een lokale Spark-console (scala) uitvoeren of een Spark livy-console (scala) uitvoeren.

### <a name="spark-local-consolescala"></a>Spark lokale console (scala)

Zorg ervoor dat u tevreden bent over de WINUTILS. EXE-vereiste.

1. Navigeer in de menu balk naar **uitvoeren** > - **configuraties bewerken...** .

2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op Hdinsight** >  **[Spark op hdinsight] MyApp**.

3. Selecteer in het hoofd venster het tabblad **lokaal uitvoeren** .

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Hoofd klasse van taak|De standaard waarde is de hoofd klasse van het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken ( **...** ) te selecteren.  en kies een andere klasse.|
    |Omgevingsvariabelen|Zorg ervoor dat de waarde voor HADOOP_HOME juist is.|
    |Locatie van WINUTILS. exe|Zorg ervoor dat het pad juist is.|

    ![Configuratie van lokale console instellen](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Ga vanuit Project naar **mijntoep** > **src** > **Main** > **scala** > **Mijntoep**.  

6. Navigeer in de menu balk naar **Hulpprogram ma's** > **Spark-console** > een **Spark lokale console uit te voeren (scala)** .

7. Vervolgens worden er twee dialoog vensters weer gegeven waarin u wordt gevraagd of u afhankelijkheden automatisch wilt oplossen. Als dit het geval is, selecteert u **automatisch oplossen**.

    ![IntelliJ-idee Spark automatisch oplossen Dialog1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ-idee Spark automatisch oplossen dialog2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. De-console moet er ongeveer als volgt uitzien. Typ `sc.appName`in het console venster en druk vervolgens op CTRL + ENTER.  Het resultaat wordt weer gegeven. U kunt de lokale console beëindigen door op de knop rood te klikken.

    ![Resultaat van lokale console voor IntelliJ-ideeën](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Livy (scala) voor de interactieve sessie van Spark

1. Navigeer in de menu balk naar **uitvoeren** > - **configuraties bewerken...** .

2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op Hdinsight** >  **[Spark op hdinsight] MyApp**.

3. Selecteer in het hoofd venster het tabblad op **afstand uitvoeren op het cluster** .

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-clusters (alleen Linux)|Selecteer het HDInsight Spark-cluster waarop u de toepassing wilt uitvoeren.|
    |Naam van hoofd klasse|De standaard waarde is de hoofd klasse van het geselecteerde bestand. U kunt de klasse wijzigen door het weglatings teken ( **...** ) te selecteren.  en kies een andere klasse.|

    ![Configuratie van een interactieve console instellen](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Ga vanuit Project naar **mijntoep** > **src** > **Main** > **scala** > **Mijntoep**.  

6. Ga in de menu balk naar **Hulpprogram ma's** > **spark-console** > **Voer Spark livy Interactive Session console (scala)** uit.

7. De-console moet er ongeveer als volgt uitzien. Typ `sc.appName`in het console venster en druk vervolgens op CTRL + ENTER.  Het resultaat wordt weer gegeven. U kunt de lokale console beëindigen door op de knop rood te klikken.

    ![Resultaat van interactieve console van IntelliJ-ideeën](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Selectie naar Spark-console verzenden

Het is handig om te voorzien in het script resultaat door het verzenden van code naar de lokale console of livy Interactive Session console (scala). U kunt bepaalde code markeren in het scala-bestand en vervolgens met de rechter muisknop op **selectie verzenden klikken naar Spark-console**. De geselecteerde code wordt naar de console verzonden en wordt uitgevoerd. Het resultaat wordt weer gegeven na de code in de-console. De console controleert de fouten als deze zich voordoen.  

   ![Selectie naar Spark-console verzenden](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integreren met HDInsight Identity Broker (HIB) 

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Verbinding maken met uw HDInsight ESP-cluster met id Broker (HIB)
U kunt de gebruikelijke stappen volgen om u aan te melden bij het Azure-abonnement om verbinding te maken met uw HDInsight ESP-cluster met id Broker (HIB). Nadat u zich hebt aangemeld, ziet u de cluster lijst in azure Verkenner. Zie [verbinding maken met uw HDInsight-cluster](#connect-to-your-hdinsight-cluster)voor meer instructies.

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Een Spark scala-toepassing uitvoeren op een HDInsight ESP-cluster met id Broker (HIB)
U kunt de normale stappen volgen voor het verzenden van een taak naar het HDInsight ESP-cluster met id Broker (HIB). Raadpleeg [een Spark scala-toepassing uitvoeren op een HDInsight Spark-cluster](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) voor meer instructies.

De benodigde bestanden worden geüpload naar een map met de naam met uw aanmeldings account en u ziet het pad naar de upload in het configuratie bestand.

   ![pad naar uploaden in de configuratie](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-console in een HDInsight ESP-cluster met id Broker (HIB)
U kunt Spark Local Console (scala) uitvoeren of Spark livy Interactive Session console (scala) uitvoeren op een HDInsight ESP-cluster met id Broker (HIB). Raadpleeg de [Spark-console](#spark-console) voor meer instructies.

   > [!NOTE]  
   > Voor het HDInsight ESP-cluster met id Broker (HIB) kunt u [een cluster koppelen](#link-a-cluster) en [fouten opsporen Apache Spark toepassingen op afstand](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) niet worden ondersteund.


## <a name="reader-only-role"></a>Alleen-lezen rol

Wanneer gebruikers een taak verzenden naar een cluster met de machtiging alleen lezer, zijn Ambari referenties vereist.

### <a name="link-cluster-from-context-menu"></a>Cluster koppelen via het snelmenu

1. Meld u aan met een account met alleen-lezen rollen.

2. Vouw in **Azure Verkenner** **HDInsight** uit om hdinsight-clusters in uw abonnement weer te geven. De gemarkeerde clusters **"Role: Reader"** hebben alleen de rol alleen-lezen.

    ![IntelliJ Azure Explorer-rol: lezer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Klik met de rechter muisknop op het cluster met de machtiging alleen lezer. Selecteer **dit cluster koppelen in het** context menu om het cluster te koppelen. Voer de Ambari-gebruikers naam en het wacht woord in.

    ![IntelliJ Azure Explorer koppelen aan dit cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Als het cluster is gekoppeld, wordt HDInsight vernieuwd.
   De fase van het cluster wordt gekoppeld.
  
    ![IntelliJ Azure Verkenner gekoppeld dialoog venster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Knoop punt voor koppeling van cluster door taken uitbreiden

1. Klik op het knoop punt **taken** , het venster **toegang tot cluster taken geweigerd** verschijnt.

2. Klik op **dit cluster koppelen om het** cluster te koppelen.

    ![dialoog venster deined voor toegang tot cluster taken](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Het venster cluster koppelen via configuratie van uitvoering/fout opsporing

1. Een HDInsight-configuratie maken. Selecteer vervolgens **extern uitvoeren in het cluster**.

2. Selecteer een cluster met een machtiging voor alleen-lezen rollen voor **Spark-clusters (alleen voor Linux)** . Er wordt een waarschuwings bericht weer gegeven. U kunt klikken op **dit cluster koppelen om het** cluster te koppelen.

   ![IntelliJ-idee uitvoeren/fouten opsporen-configuratie maken](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Opslag accounts weer geven

* Voor clusters met de machtiging alleen lezer, klikt u op het knoop punt **opslag accounts** , het venster **opslag toegang geweigerd** verschijnt. U kunt klikken op **openen Azure Storage Explorer** om Storage Explorer te openen.

   ![IntelliJ-idee opslag toegang geweigerd](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Knop voor IntelliJ-idee opslag toegang geweigerd](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Voor gekoppelde clusters klikt u op het knoop punt **opslag accounts** , het venster **opslag toegang geweigerd** verschijnt. U kunt klikken op **openen Azure Storage** om Storage Explorer te openen.

   ![IntelliJ-idee opslag toegang Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Knop voor IntelliJ-idee opslag toegang Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Bestaande IntelliJ-ideeën converteren voor het gebruik van Azure-toolkit voor IntelliJ

U kunt de bestaande Spark scala-toepassingen die u in IntelliJ-idee hebt gemaakt, converteren om compatibel te zijn met Azure-toolkit voor IntelliJ. U kunt vervolgens de-invoeg toepassing gebruiken om de toepassingen naar een HDInsight Spark-cluster te verzenden.

1. Voor een bestaande Spark scala-toepassing die is gemaakt via IntelliJ idee, opent u het bijbehorende. IML-bestand.

2. Op hoofd niveau is een **module** -element zoals het volgende:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Bewerk het element om `UniqueKey="HDInsightTool"` toe te voegen zodat het **module** -element er als volgt uitziet:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Sla de wijzigingen op. Uw toepassing moet nu compatibel zijn met Azure-toolkit voor IntelliJ. U kunt dit testen door met de rechter muisknop op de project naam in project te klikken. In het pop-upmenu wordt nu de optie **Spark-toepassing verzenden naar HDInsight**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het cluster dat u hebt gemaakt met de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **HDInsight** in het **Zoekvak** bovenaan.

1. Selecteer onder **Services** de optie **HDInsight-clusters**.

1. Selecteer in de lijst met HDInsight-clusters die wordt weer gegeven, de **...** naast het cluster dat u voor deze zelf studie hebt gemaakt.

1. Selecteer **Verwijderen**. Selecteer **Ja**.

![HDInsight-cluster Azure Portal verwijderen](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de Azure-toolkit voor IntelliJ-invoeg toepassing gebruikt om Apache Spark toepassingen te ontwikkelen die in [scala](https://www.scala-lang.org/)zijn geschreven en deze vervolgens rechtstreeks vanuit de IntelliJ Integrated Development Environment (IDE) te verzenden naar een HDInsight Spark-cluster. Ga naar het volgende artikel om te zien hoe de gegevens die u hebt geregistreerd in Apache Spark kunnen worden overgebracht naar een BI-hulpprogramma voor analyse zoals Power BI.

> [!div class="nextstepaction"]
> [Gegevens analyseren met BI-tools](apache-spark-use-bi-tools.md)
