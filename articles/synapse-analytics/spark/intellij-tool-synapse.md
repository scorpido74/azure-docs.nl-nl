---
title: Zelfstudie - Azure Toolkit voor IntelliJ (Spark-toepassing)
description: Zelfstudie - Gebruik de Azure Toolkit voor IntelliJ om Spark-toepassingen te ontwikkelen, die zijn geschreven in Scala, en deze in te dienen bij een Apache Spark-pool (voorbeeld).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422653"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Zelfstudie: Azure Toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor Spark-pools te maken (voorbeeld)

In deze zelfstudie wordt uitgelegd hoe u de Azure Toolkit voor IntelliJ-plug-in gebruiken om Apache Spark-toepassingen te ontwikkelen, die zijn geschreven in [Scala](https://www.scala-lang.org/)en deze vervolgens rechtstreeks vanuit de geïntegreerde ontwikkelomgeving (IDE) van De IntelliJ indienen. U de plug-in op een aantal manieren gebruiken:

- Een Scala Spark-toepassing ontwikkelen en indienen op een Spark-pool.
- Toegang tot uw Spark-poolsbronnen.
- Ontwikkelen en lokaal een Scala Spark-toepassing uitvoeren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - De Azure Toolkit voor IntelliJ-plug-in gebruiken
> - Apache Spark-toepassingen ontwikkelen
> - Toepassing indienen bij Spark-groepen

## <a name="prerequisites"></a>Vereisten

- [IntelliJ IDEA Community Versie](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure toolkit plugin 3.27.0-2019.2 – Installeren vanuit [IntelliJ Plugin repository](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (versie 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin - Installeren van [IntelliJ Plugin repository](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Deze voorwaarde is alleen voor Windows-gebruikers.

  Terwijl u de lokale Spark Scala-toepassing uitvoert op een Windows-computer, krijgt u mogelijk een uitzondering, zoals uitgelegd in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). De uitzondering treedt op omdat WinUtils.exe ontbreekt op Windows.
  Download de [WinUtils die uitvoerbaar zijn](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) naar een locatie zoals **C:\WinUtils\bin**om deze fout op te lossen. Voeg vervolgens de omgevingsvariabele **toe HADOOP_HOME**en stel de waarde van de variabele in op **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Een Spark Scala-toepassing maken voor een Spark-pool

1. Start IntelliJ IDEA en selecteer **Create New Project** om het venster **New Project** te openen.
2. Selecteer **Apache Spark/HDInsight** in het linkerdeelvenster.
3. Selecteer **Spark-project met voorbeelden (Scala)** in het hoofdvenster.
4. Selecteer in de vervolgkeuzelijst **Gereedschap bouwen** een van de volgende typen:

   - **Maven**, voor de ondersteuning van de wizard Scala-project maken.
   - **SBT**, voor het beheren van de afhankelijkheden en het maken van het Scala-project.

    ![Dialoogvenster IntelliJ IDEA New Project](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecteer **Next**.
6. Geef in het venster **New project** de volgende gegevens op:

    | Eigenschap | Beschrijving |
    | ----- | ----- |
    |Projectnaam| Voer een naam in. In deze zelfstudie wordt `myApp` gebruikt.|
    |Project&nbsp;location| Voer de gewenste locatie in om uw project op te slaan.|
    |Project SDK| Het kan leeg zijn op uw eerste gebruik van IDEA. Selecteer **New...** en ga naar uw JDK.|
    |Spark version|De wizard voor het maken van het project integreert de juiste versie voor Spark SDK en Scala SDK. Synapse ondersteunt alleen **Spark 2.4.0**.|

    ![De Apache Spark SDK selecteren](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecteer **Finish**. Het kan enkele minuten duren voordat het project beschikbaar is.
8. Het Spark-project maakt automatisch een artefact voor u. Ga als volgt te werk om het artefact weer te geven:

   a. Navigeer in de menubalk naar > **Bestandsprojectstructuur...**. **File**

   b. Selecteer **Artefacten**in het venster **Projectstructuur** .

   c. Selecteer **Annuleren** na het bekijken van het artefact.

    ![Artefact-gegevens in het dialoogvenster](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Zoek **LogQuery** van **myApp** > **src** > **belangrijkste** > **scala**> **voorbeeld**> **LogQuery**. Deze zelfstudie gebruikt **LogQuery** om uit te voeren.

   ![Opdrachten voor het maken van een Klasse Scala vanuit Project](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Verbinding maken met uw Spark-zwembaden

Meld u aan bij een Azure-abonnement om verbinding te maken met uw Spark-pools.

### <a name="sign-in-to-your-azure-subscription"></a>Aanmelden bij uw Azure-abonnement

1. Navigeer in de menubalk naar**Windows** > **Azure Explorer** **weergeven.** > 

   ![IntelliJ IDEA toont Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Klik in Azure Explorer met de rechtermuisknop op het **Azure-knooppunt** en selecteer **Aanmelden**.

   ![IntelliJ IDEA explorer klik met de rechtermuisknop op azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Kies **in** het dialoogvenster Aanmelden voor Azure de optie **Apparaataanmelding**en selecteer **Aanmelden**.

    ![IntelliJ IDEA azure sign-in](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Klik in het dialoogvenster **Aanmelding van Azure-apparaten** op **Kopiëren&openen**.

   ![IntelliJ IDEA azure device login](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Plak de code in de browserinterface en klik op **Volgende.**

   ![Microsoft voert codedialoogvenster voor HDI in](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Voer uw Azure-referenties in en sluit de browser.

   ![Microsoft voert e-maildialoog in voor HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Nadat u bent aangemeld, bevat het dialoogvenster **Abonnementen selecteren** alle Azure-abonnementen die aan de referenties zijn gekoppeld. Selecteer uw abonnement en klik op **Selecteren**.

    ![Het dialoogvenster Abonnementen selecteren](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Vouw vanuit **Azure Explorer**Apache Spark **op Synapse** uit om de werkruimten in uw abonnementen weer te geven.

    ![IntelliJ IDEA Azure Explorer hoofdweergave](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Als u de Spark-pools wilt bekijken, u een werkruimte verder uitbreiden.

    ![Azure Explorer-opslagaccounts](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Remote Run a Spark Scala-toepassing op een Spark-pool

Nadat u een Scala-toepassing hebt hebt aanmaak, u deze op afstand uitvoeren.

1. Open het venster **Configuratie van uitvoeren/foutopsporing** openen door op het pictogram te klikken.

    ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. Klik in het dialoogvenster **Configuraties uitvoeren/debuggen** op **+** En selecteer **Vervolgens Apache Spark op Synapse**.

    ![De opdracht Spark-toepassing verzenden naar HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Geef in het venster **Configuraties uitvoeren/foutopsporing** de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |Spark-zwembaden|Selecteer de Spark-pools waarop u uw toepassing wilt uitvoeren.|
    |Selecteer een artefact dat u wilt verzenden|Laat de standaardinstelling.|
    |Naam van de hoofdklasse|De standaardwaarde is de hoofdklasse van het geselecteerde bestand. U de klasse wijzigen door de ellips te selecteren**en**een andere klasse te kiezen.|
    |Taakconfiguraties|U de standaardtoets en -waarden wijzigen. Zie [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html)voor meer informatie.|
    |Opdrachtregelargumenten|U argumenten invoeren die worden gescheiden door ruimte voor de hoofdklasse indien nodig.|
    |Verwezen potten en gerefereerde bestanden|U de paden voor de eventuele jars en bestanden waarnaar wordt verwezen, invoeren. U ook door bestanden bladeren in het virtuele bestandssysteem van Azure, dat momenteel alleen het ADLS Gen 2-cluster ondersteunt. Voor meer informatie: [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) en How to upload resources to [cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Opslag voor het uploaden van vacatures|Uitvouwen om extra opties te onthullen.|
    |Opslagtype|Selecteer **Azure Blob gebruiken om te uploaden** vanuit de vervolgkeuzelijst.|
    |Opslagaccount|Voer uw opslagaccount in.|
    |Opslagsleutel|Voer uw opslagsleutel in.|
    |Opslagcontainer|Selecteer uw opslagcontainer in de vervolgkeuzelijst zodra **opslagaccount** en **opslagsleutel** zijn ingevoerd.|

    ![Het dialoogvenster Spark-indiening](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Klik op het pictogram **SparkJobRun** om uw project in te dienen bij de geselecteerde Spark-groep. Op het tabblad **Externe vonktaak op het** tabblad Cluster wordt de voortgang van de uitvoering van taken onderin weergegeven. U de toepassing stoppen door op de rode knop te klikken.

    ![Venster Apache Spark-indiening](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Het dialoogvenster Spark-indiening](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Toepassingen voor lokale run/debuging Apache Spark

U de onderstaande instructies volgen om uw lokale run en lokale foutopsporing voor uw Apache Spark-taak in te stellen.

### <a name="scenario-1-do-local-run"></a>Scenario 1: Lokale run uitvoeren

1. Open het dialoogvenster **Configuraties uitvoeren/foutopsporing** openen**+** en selecteer het plusteken ( ). Selecteer vervolgens de optie **Apache Spark op Synapse.** Voer gegevens in voor **Naam**, **Hoofdklassenaam** om op te slaan.

    ![Intellij Run debug configuraties lokale run](./media/intellij-tool-synapse/local-run-synapse.png)

    - Omgevingsvariabelen en WinUtils.exe Locatie zijn alleen voor windows-gebruikers.
    - Omgevingsvariabelen: de variabele systeemomgeving kan automatisch worden gedetecteerd als u deze eerder hebt ingesteld en u niet handmatig hoeft toe te voegen.
    - [WinUtils.exe-locatie](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): U de locatie WinUtils opgeven door op het mappictogram aan de rechterkant te klikken.

2. Klik vervolgens op de lokale afspeelknop.

    ![Intellij Run debug configuraties lokale run](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Zodra de lokale run is voltooid, u, als script uitvoer bevat, het uitvoerbestand controleren op > **__standaardgegevens.__** **data**

    ![Intellij Project lokaal run resultaat](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: Lokale foutopsporing uitvoeren

1. Open het **Script LogQuery** en stel breekpunten in.
2. Klik op Het pictogram **Lokale foutopsporing** om lokale foutopsporing uit te kunnen.

    ![Intellij Project lokaal run resultaat](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Synapse Workspace openen en beheren

U verschillende bewerkingen uitvoeren in Azure Explorer binnen Azure Toolkit voor IntelliJ. Navigeer in de menubalk naar**Windows** > **Azure Explorer** **weergeven.** > 

### <a name="launch-workspace"></a>Werkruimte starten

1. Navigeer vanuit Azure Explorer naar **Apache Spark op Synapse**en vouw deze vervolgens uit.

    ![IntelliJ IDEA Azure Explorer hoofdweergave](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Klik met de rechtermuisknop op een werkruimte en selecteer **vervolgens Werkruimte starten,** de website wordt geopend.

    ![Gegevens over de functieweergave van spark](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Gegevens over de functieweergave van spark](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-console

U Spark Local Console(Scala) uitvoeren of de interactieve sessieconsole Spark Livy (Scala) uitvoeren.

### <a name="spark-local-console-scala"></a>Lokale console spark (Scala)

Zorg ervoor dat je tevreden bent met de WINUTILS. EXE-voorwaarde.

1. Navigeer in de menubalk naar > **Configuraties** **bewerken...**.
2. Navigeer in het venster **Configuratie van uitvoeren/debuggen** in het linkerdeelvenster naar **Apache Spark op Synapse** > **[Spark on Synapse] myApp**.
3. Selecteer in het hoofdvenster het tabblad **Lokaal uitvoeren.**
4. Geef de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |Omgevingsvariabelen|Zorg ervoor dat de waarde voor HADOOP_HOME juist is.|
    |WINUTILS.exe-locatie|Zorg ervoor dat het pad correct is.|

    ![Configuratie van lokale consoleset](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala** > **myApp**.
6. Navigeer in de menubalk naar **Tools** > **Spark-console** > **Spark Local Console(Scala)** uitvoeren.
7. Vervolgens kunnen twee dialoogvensters worden weergegeven om u te vragen of u afhankelijkheden automatisch wilt oplossen. Selecteer dan **Automatisch oplossen**.

    ![IntelliJ IDEA Spark Auto Fix, dialoogvenster1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix dialoogvenster2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. De console moet er hetzelfde uitzien als de onderstaande afbeelding. Druk in het `sc.appName`type consolevenster en druk op ctrl+Enter. Het resultaat wordt getoond. U de lokale console stoppen door op de rode knop te klikken.

    ![IntelliJ IDEA lokale console resultaat](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy interactieve sessie console (Scala)

Het wordt alleen ondersteund op IntelliJ 2018.2 en 2018.3.

1. Navigeer in de menubalk naar > **Configuraties** **bewerken...**.

2. Navigeer in het venster **Configuratie van uitvoeren/debuggen** in het linkerdeelvenster naar **Apache Spark op synaps** > **[Spark op synapse] myApp**.

3. Selecteer in het hoofdvenster het tabblad **Extern uitvoeren in cluster.**

4. Geef de volgende waarden op en selecteer **OK:**

    |Eigenschap |Waarde |
    |----|----|
    |Spark-zwembaden|Selecteer de Spark-pools waarop u uw toepassing wilt uitvoeren.|
    ||

    ![Configuratie van interactieve consoleset](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Van Project, navigeer naar **myApp** > **src** > **belangrijkste** > **scala** > **myApp**.

6. Navigeer in de menubalk naar **Tools** > **Spark-console** > **Run Spark Livy Interactive Session Console(Scala).**
7. De console moet er hetzelfde uitzien als de onderstaande afbeelding. Druk in het `sc.appName`type consolevenster en druk op ctrl+Enter. Het resultaat wordt getoond. U de lokale console stoppen door op de rode knop te klikken.

    ![IntelliJ IDEA Interactieve Console Resultaat](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Selectie verzenden naar Spark-console

Het is handig voor u om het scriptresultaat te voorzien door een code te verzenden naar de lokale console of Livy Interactive Session Console (Scala). U bepaalde code markeren in het Scala-bestand en vervolgens met de rechtermuisknop selectie **naar spark-console verzenden**. De geselecteerde code wordt naar de console verzonden en wordt uitgevoerd. Het resultaat wordt weergegeven na de code in de console. De console controleert de fouten als deze bestaan.

   ![Selectie naar Spark-console verzenden](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [Een nieuwe Apache Spark-pool maken voor een Azure Synapse Analytics-werkruimte](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
