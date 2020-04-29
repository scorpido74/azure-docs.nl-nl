---
title: Zelf studie-Azure-toolkit voor IntelliJ (Spark-toepassing)
description: 'Zelf studie: gebruik de Azure-toolkit voor IntelliJ voor het ontwikkelen van Spark-toepassingen die zijn geschreven in scala en verzend deze naar een Apache Spark groep (preview).'
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422653"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Zelf studie: Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor Spark-Pools te maken (preview)

In deze zelf studie wordt gedemonstreerd hoe u de Azure-toolkit voor IntelliJ-invoeg toepassing gebruikt voor het ontwikkelen van Apache Spark toepassingen, die zijn geschreven in [scala](https://www.scala-lang.org/), en ze vervolgens rechtstreeks vanuit de IntelliJ Integrated Development Environment (IDE) naar een Spark-groep (preview) verzenden. U kunt de invoeg toepassing op een paar manieren gebruiken:

- Ontwikkel en verzend een scala Spark-toepassing in een Spark-pool.
- Toegang tot uw Spark-Pools resources.
- Ontwikkel en voer lokaal een scala Spark-toepassing uit.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - De Azure-toolkit voor IntelliJ-invoeg toepassing gebruiken
> - Apache Spark-toepassingen ontwikkelen
> - Toepassing naar Spark-Pools verzenden

## <a name="prerequisites"></a>Vereisten

- [IntelliJ-idee Community-versie](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure Toolkit-invoeg toepassing 3.27.0-2019.2-installeren vanuit [IntelliJ-invoeg toepassing opslag plaats](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Jdk (versie 1,8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala-invoeg toepassing: installeren vanuit [IntelliJ-invoeg toepassing](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Deze vereiste is alleen voor Windows-gebruikers.

  Terwijl u de lokale Spark scala-toepassing uitvoert op een Windows-computer, kunt u een uitzonde ring krijgen, zoals wordt uitgelegd in [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzonde ring treedt op omdat WinUtils. exe ontbreekt in Windows.
  U kunt deze fout oplossen door het [uitvoer bare bestand WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) te downloaden naar een locatie zoals **C:\WinUtils\bin**. Voeg vervolgens de omgevings variabele **HADOOP_HOME**toe en stel de waarde van de variabele in op **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Een Spark scala-toepassing maken voor een Spark-groep

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.
2. Selecteer **Apache Spark/HDInsight** in het linkerdeel venster.
3. Selecteer **Spark-project met voor beelden (scala)** in het hoofd venster.
4. Selecteer een van de volgende typen in de vervolg keuzelijst **Build Tool** :

   - **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   - **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

    ![Het dialoog venster IntelliJ-idee nieuw project](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecteer **Next**.
6. Geef in het venster **New project** de volgende gegevens op:

    | Eigenschap | Beschrijving |
    | ----- | ----- |
    |Projectnaam| Voer een naam in. In deze zelfstudie wordt `myApp` gebruikt.|
    |Project&nbsp;location| Voer de gewenste locatie in om uw project op te slaan.|
    |Project SDK| Dit kan leeg zijn in uw eerste gebruik van het idee. Selecteer **New...** en ga naar uw JDK.|
    |Spark version|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Synapse ondersteunt alleen **Spark 2.4.0**.|

    ![De Apache Spark SDK selecteren](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecteer **Finish**. Het kan enkele minuten duren voordat het project beschikbaar wordt.
8. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

   a. Navigeer in de menu balk naar **bestand** > **project structuur...**.

   b. Selecteer **artefacten**in het venster **project structuur** .

   c. Selecteer **Annuleren** nadat u het artefact hebt weer gegeven.

    ![Artefact gegevens in het dialoog venster](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Zoeken **naar LogQuery** van de**main** > **scala** **myApp** > **src** > > **sample**-voorbeeld> **LogQuery**van Mijntoep src. Deze zelf studie maakt gebruik van **LogQuery** om uit te voeren.

   ![Opdrachten voor het maken van een scala-klasse van project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Verbinding maken met uw Spark-Pools

Meld u aan bij het Azure-abonnement om verbinding te maken met uw Spark-Pools.

### <a name="sign-in-to-your-azure-subscription"></a>Meld u aan bij uw Azure-abonnement

1. Ga in de menu balk naar het **View** > **hulp programma Windows** > **Azure Verkenner**bekijken.

   ![IntelliJ-idee toont Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Klik in azure Verkenner met de rechter muisknop op het **Azure** -knoop punt en selecteer **Aanmelden**.

   ![IntelliJ-ideeën Verkenner met de rechter muisknop op Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Kies **apparaat aanmelden**in het dialoog venster **Azure-aanmelding** en selecteer **Aanmelden**.

    ![IntelliJ-idee Azure-aanmelding](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Klik in het dialoog venster **Aanmelden bij Azure-apparaat** op **kopiëren&openen**.

   ![IntelliJ-idee Azure-apparaat aanmelden](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Plak de code in de browser interface en klik vervolgens op **volgende**.

   ![Micro soft voert het dialoog venster code in voor HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Voer uw Azure-referenties in en sluit de browser.

   ![Micro soft voert het dialoog venster e-mail voor HDI in](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Nadat u bent aangemeld, worden in het dialoog venster **abonnementen selecteren** een lijst weer gegeven met alle Azure-abonnementen die zijn gekoppeld aan de referenties. Selecteer uw abonnement en klik vervolgens op **selecteren**.

    ![Het dialoogvenster Abonnementen selecteren](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Vouw in **Azure Explorer** **Apache Spark uit op Synapse** om de werk ruimten in uw abonnementen weer te geven.

    ![Hoofd weergave van IntelliJ-idee Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Als u de Spark-groepen wilt weer geven, kunt u een werk ruimte verder uitbreiden.

    ![Azure Explorer-opslag accounts](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Extern een Spark scala-toepassing uitvoeren op een Spark-groep

Nadat u een scala-toepassing hebt gemaakt, kunt u deze op afstand uitvoeren.

1. Klik op het pictogram **uitvoeren/configuratie van fouten opsporen** .

    ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. Klik in het dialoog venster **configuraties voor uitvoeren/fout opsporing** op **+** en selecteer **Apache Spark op Synapse**.

    ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Geef in het venster **configuraties voor uitvoeren/fout opsporing** de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-Pools|Selecteer de Spark-Pools waarop u de toepassing wilt uitvoeren.|
    |Een artefact selecteren om in te dienen|De standaard instelling ongewijzigd.|
    |Naam van hoofd klasse|De standaard waarde is de hoofd klasse van het geselecteerde bestand. U kunt de klasse wijzigen door het beletsel teken (**...**) te selecteren en een andere klasse te kiezen.|
    |Taak configuraties|U kunt de standaard sleutel en-waarden wijzigen. Zie [Apache Livy rest API](https://livy.incubator.apache.org./docs/latest/rest-api.html)voor meer informatie.|
    |Opdrachtregelargumenten|Als dat nodig is, kunt u argumenten door geven die worden gescheiden door de ruimte voor de hoofd klasse.|
    |Potten waarnaar wordt verwezen en waarnaar wordt verwezen|U kunt de paden voor de potten en bestanden waarnaar wordt verwezen, opgeven. U kunt ook bladeren door bestanden in het virtuele bestands systeem van Azure, dat momenteel alleen ondersteuning biedt voor ADLS gen 2-cluster. Voor meer informatie: [Apache Spark configuratie](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) en [het uploaden van resources naar het cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Upload opslag van taken|Uitvouwen om extra opties weer te geven.|
    |Opslagtype|Selecteer **Azure-Blob gebruiken om te uploaden** uit de vervolg keuzelijst.|
    |Opslagaccount|Voer uw opslag account in.|
    |Opslag sleutel|Voer uw opslag sleutel in.|
    |Opslag container|Selecteer uw opslag container in de vervolg keuzelijst zodra het **opslag account** en de **opslag sleutel** zijn ingevoerd.|

    ![Het dialoog venster voor de verzen ding van Spark](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Klik op **SparkJobRun** pictogram om het project naar de geselecteerde Spark-groep te verzenden. Op de **externe Spark-taak in** het tabblad cluster wordt de voortgang van de taak uitvoering onderaan weer gegeven. U kunt de toepassing stoppen door te klikken op de knop rood.

    ![Inzendings venster Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Het dialoog venster voor de verzen ding van Spark](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokale toepassingen voor uitvoeren/fouten opsporen Apache Spark

U kunt de onderstaande instructies volgen om uw lokale uitvoerings-en lokale fout opsporing in te stellen voor uw Apache Spark-taak.

### <a name="scenario-1-do-local-run"></a>Scenario 1: lokale uitvoering uitvoeren

1. Open het dialoog venster **configuraties voor uitvoeren/fout opsporing** en selecteer het**+** plus teken (). Selecteer vervolgens de optie **Apache Spark op Synapse** . Voer informatie in voor **naam**, **naam van hoofd klasse** om op te slaan.

    ![IntelliJ voor fout opsporing uitvoeren in lokale uitvoering](./media/intellij-tool-synapse/local-run-synapse.png)

    - Omgevings variabelen en WinUtils. exe-locatie zijn alleen voor Windows-gebruikers.
    - Omgevings variabelen: de systeem omgevingsvariabele kan automatisch worden gedetecteerd als u deze eerder hebt ingesteld en niet hand matig moet worden toegevoegd.
    - [Locatie van WinUtils. exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): u kunt de locatie van de WinUtils opgeven door aan de rechter kant op het mappictogram te klikken.

2. Klik vervolgens op de knop lokaal afspelen.

    ![IntelliJ voor fout opsporing uitvoeren in lokale uitvoering](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Zodra de lokale uitvoering is voltooid, kunt u het uitvoer bestand **controleren op basis** > van de**__standaard__** waarden als het script uitvoer bevat.

    ![Resultaat van lokale uitvoering van IntelliJ-project](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: lokale fout opsporing uitvoeren

1. Open het script **LogQuery** en stel onderbrekings punten in.
2. Klik op **lokaal debug** -pictogram om lokale fout opsporing uit te voeren.

    ![Resultaat van lokale uitvoering van IntelliJ-project](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Toegang tot en beheer van de Synapse-werk ruimte

U kunt binnen Azure-toolkit voor IntelliJ verschillende bewerkingen uitvoeren in azure Verkenner. Ga in de menu balk naar het **View** > **hulp programma Windows** > **Azure Verkenner**bekijken.

### <a name="launch-workspace"></a>Werk ruimte starten

1. Ga in azure Verkenner naar **Apache Spark op Synapse**en vouw dit uit.

    ![Hoofd weergave van IntelliJ-idee Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klik met de rechter muisknop op een werk ruimte en selecteer vervolgens **werk ruimte starten**, website wordt geopend.

    ![Details van de toepassing Spark-taak weergave](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Details van de toepassing Spark-taak weergave](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-console

U kunt een lokale Spark-console (scala) uitvoeren of een Spark livy-console (scala) uitvoeren.

### <a name="spark-local-console-scala"></a>Spark lokale console (scala)

Zorg ervoor dat u tevreden bent over de WINUTILS. EXE-vereiste.

1. Navigeer in de menu balk naar > **bewerkings configuraties uitvoeren...**. **Run**
2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op Synapse** > **[Spark op Synapse] MyApp**.
3. Selecteer in het hoofd venster het tabblad **lokaal uitvoeren** .
4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Omgevingsvariabelen|Zorg ervoor dat de waarde voor HADOOP_HOME juist is.|
    |Locatie van WINUTILS. exe|Zorg ervoor dat het pad juist is.|

    ![Configuratie van lokale console instellen](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Ga vanuit Project naar **Mijntoep** > **src** > **Main** > **scala** > **Mijntoep**.
6. Ga in de menu balk naar **extra** > **Spark console** > **uitvoeren Spark Local Console (scala)**.
7. Vervolgens worden er twee dialoog vensters weer gegeven waarin u wordt gevraagd of u afhankelijkheden automatisch wilt oplossen. Als dit het geval is, selecteert u **automatisch oplossen**.

    ![IntelliJ-idee Spark automatisch oplossen Dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ-idee Spark automatisch oplossen dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. De-console moet er ongeveer als volgt uitzien. In het console venster typt `sc.appName`en druk vervolgens op CTRL + ENTER. Het resultaat wordt weer gegeven. U kunt de lokale console stoppen door te klikken op de knop rood.

    ![Resultaat van lokale console voor IntelliJ-ideeën](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Livy (scala) voor de interactieve sessie van Spark

Het wordt alleen ondersteund op IntelliJ 2018,2 en 2018,3.

1. Navigeer in de menu balk naar > **bewerkings configuraties uitvoeren...**. **Run**

2. Ga in het linkerdeel venster van het venster **configuraties voor uitvoeren/fout opsporing** naar **Apache Spark op Synapse** > **[Spark op Synapse] MyApp**.

3. Selecteer in het hoofd venster het tabblad op **afstand uitvoeren op het cluster** .

4. Geef de volgende waarden op en selecteer **OK**:

    |Eigenschap |Waarde |
    |----|----|
    |Spark-Pools|Selecteer de Spark-Pools waarop u de toepassing wilt uitvoeren.|
    ||

    ![Configuratie van een interactieve console instellen](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Ga vanuit Project naar **Mijntoep** > **src** > **Main** > **scala** > **Mijntoep**.

6. Ga in de menu balk naar **extra** > **Spark-console** > en**Voer Spark livy Interactive Session console (scala)** uit.
7. De-console moet er ongeveer als volgt uitzien. In het console venster typt `sc.appName`en druk vervolgens op CTRL + ENTER. Het resultaat wordt weer gegeven. U kunt de lokale console stoppen door te klikken op de knop rood.

    ![Resultaat van interactieve console van IntelliJ-ideeën](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Selectie naar Spark-console verzenden

Het is handig om te voorzien in het script resultaat door het verzenden van code naar de lokale console of livy Interactive Session console (scala). U kunt bepaalde code markeren in het scala-bestand en vervolgens met de rechter muisknop op **selectie verzenden klikken naar Spark-console**. De geselecteerde code wordt naar de console verzonden en wordt uitgevoerd. Het resultaat wordt weer gegeven na de code in de-console. De console controleert de fouten als deze zich voordoen.

   ![Selectie naar Spark-console verzenden](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [Een nieuwe Apache Spark groep maken voor een Azure Synapse Analytics-werk ruimte](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
