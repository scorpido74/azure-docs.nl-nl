---
title: Apache Hadoop & Visual Studio Data Lake Tools - Azure HDInsight
description: Meer informatie over het installeren en gebruiken van Data Lake Tools voor Visual Studio om verbinding te maken met Apache Hadoop-clusters in Azure HDInsight en vervolgens Hive-query's uit te voeren.
keywords: hadoop-hulpprogramma's, hive-query, visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272784"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake-hulpprogramma's voor Visual Studio gebruiken om verbinding te maken met Azure HDInsight en Apache Hive-query's uit te voeren

Meer informatie over het gebruik van Microsoft Azure Data Lake en Stream Analytics Tools voor Visual Studio (ook wel Data Lake Tools genoemd) om verbinding te maken met [Apache Hadoop-clusters in Azure HDInsight](apache-hadoop-introduction.md) en Hive-query's in te dienen.  

Zie [Aan de slag met HDInsight](apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het gebruik van HDInsight.  

Zie C# topologieën voor Apache Storm ontwikkelen voor meer informatie over verbinding maken met een Cluster Apache Storm [met behulp van de Data Lake-tools voor Visual Studio.](../storm/apache-storm-develop-csharp-visual-studio-topology.md)

U kunt Data Lake Tools voor Visual Studio gebruiken voor toegang tot Azure Data Lake Analytics en HDInsight. Zie voor informatie over Data Lake Tools [Zelfstudie: U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Vereisten

Om dit artikel te voltooien en Data Lake Tools voor Visual Studio te gebruiken, heb je de volgende items nodig:

* Een Azure HDInsight-cluster. Zie Aan de slag [met Apache Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om een HDInsight-cluster te maken. Als u interactieve Apache Hive-query's wilt uitvoeren, hebt u een [HDInsight Interactive Query-cluster](../interactive-query/apache-interactive-query-get-started.md) nodig.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). De [Visual Studio Community editie](https://visualstudio.microsoft.com/vs/community/) is gratis. De instructies hier zijn voor [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools voor Visual Studio installeren  

Volg de juiste instructies om Data Lake Tools te installeren voor uw versie van Visual Studio:

- Voor Visual Studio 2017 of Visual Studio 2019:

    Zorg er tijdens de installatie van Visual Studio voor dat u de **azure-ontwikkelworkload** of de werkbelasting **voor gegevensopslag en -verwerking** opneemt.  

    Ga voor bestaande Visual Studio-installaties naar de IDE-menubalk en selecteer **Hulpmiddelen** > **voor extra's en functies om** Visual Studio Installer te openen. Selecteer op het tabblad **Workloads** ten minste de **Azure-ontwikkelingsworkload** (onder **Web & Cloud)** of de werkbelasting **voor gegevensopslag en -verwerking** (onder **Andere hulpprogramma's).**

  ![Workloadselectie, Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Voor Visual Studio 2015:

    [Data Lake-hulpprogramma's downloaden.](https://www.microsoft.com/download/details.aspx?id=49504) Kies de versie van Data Lake Tools die overeenkomt met uw versie van Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Gegevenslaketools bijwerken voor Visual Studio  

Zorg er vervolgens voor dat u Data Lake Tools bijwerkt naar de meest recente versie.

1. Open Visual Studio.

2. Selecteer Doorgaan zonder **code**in het venster **Start** .

3. Kies **extensies** > **beheren**in de menubalk van Visual Studio IDE .

4. Vouw in het dialoogvenster **Extensies beheren** het knooppunt **Updates** uit.

5. Als de lijst met beschikbare updates **Azure Data Lake en Stream Analytic Tools**bevat, selecteert u deze. Selecteer vervolgens de knop **Bijwerken.** Nadat het dialoogvenster **Downloaden en installeren** is weergegeven en verdwijnt, voegt Visual Studio de extensie Azure Data Lake en Stream Analytic **Tools** toe aan het updateschema.

6. Sluit alle Visual Studio-vensters. Het dialoogvenster **VSIX Installer** wordt weergegeven.

7. Selecteer **Licentie** om de licentievoorwaarden te lezen en selecteer **Sluiten** om terug te keren naar het dialoogvenster **VSIX Installer.**

8. Selecteer **Wijzigen**. De installatie van de uitbreidingsupdate begint. Na een tijdje wordt het dialoogvenster gewijzigd om aan te geven dat het klaar is met het aanbrengen van wijzigingen. Selecteer **Sluiten**en start Visual Studio opnieuw om de installatie te voltooien.

> [!NOTE]  
> U kunt alleen Data Lake Tools versie 2.3.0.0 of hoger gebruiken om verbinding te maken met Interactive Query-clusters en interactieve Hive-query's uit te voeren.

## <a name="connect-to-azure-subscriptions"></a>Verbinding maken met Azure-abonnementen

U Data Lake Tools voor Visual Studio gebruiken om verbinding te maken met uw HDInsight-clusters, een aantal basisbeheerbewerkingen uit te voeren en Hive-query's uit te voeren.

> [!NOTE]  
> Zie [Hive-query's schrijven en verzenden met Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)voor informatie over het maken van verbinding met een algemeen Hadoop-cluster.

### <a name="connect-to-an-azure-subscription"></a>Verbinding maken met een Azure-abonnement

Verbinding maken met uw Azure-abonnement:

1. Open Visual Studio.

2. Selecteer Doorgaan zonder **code**in het venster **Start** .

3. Kies **serververkenner weergeven** > in de menubalk van**IDE**.

4. Klik in **Server Explorer**met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement**en voltooi het verificatieproces. Vouw azure**HDInsight** **uit** > vanuit **Server Explorer**om een lijst met bestaande HDInsight-clusters weer te geven.

5. Als u geen clusters hebt, maakt u er een met de Azure-portal, Azure PowerShell of de HDInsight SDK. Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie.

   ![HDInsight-clusterlijst, Server Explorer, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Een HDInsight-cluster uitbreiden. Het cluster bevat knooppunten voor **Hive Databases,** een standaardopslagaccount, extra gekoppelde opslagaccounts en **Hadoop Service Log**. U kunt de entiteiten verder uitbreiden.

Nadat u verbinding hebt gemaakt met het Azure-abonnement, kunt u de volgende taken uitvoeren.

### <a name="connect-to-azure-from-visual-studio"></a>Verbinding maken met Azure vanuit Visual Studio

Verbinding maken met Azure Portal vanuit Visual Studio:

1. Vouw **in Server Explorer** **Azure** > **HDInsight** uit en selecteer uw cluster.

2. Klik met de rechtermuisknop op een HDInsight-cluster en selecteer **Cluster beheren in Azure-portal.**

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Stel vragen en feedback van Visual Studio

Vragen stellen en/of feedback geven vanuit Visual Studio:

1. **Kies** > azure**HDInsight**in Server Explorer.

2. Klik met de rechtermuisknop op **HDInsight** en selecteer **een MSDN-forum** om vragen te stellen of feedback te **geven** om feedback te geven.

## <a name="link-to-or-edit-a-cluster"></a>Een cluster koppelen aan of bewerken

> [!NOTE]
> Momenteel is het enige type HDInsight-cluster waarnaar u linken een Hive-type.

Een HDInsight-cluster koppelen:

1. Klik met de rechtermuisknop op **HDInsight**en selecteer **vervolgens Een HDInsight-cluster koppelen** om het dialoogvenster Een **HDInsight-cluster** koppelen weer te geven.

2. Voer een **verbindingsurl** in het formulier *\://\<&nbsp;https-clusternaam>.azurehdinsight.net*. De **clusternaam** vult automatisch het gedeelte van de clusternaam van uw URL in wanneer u naar een ander veld gaat. Voer vervolgens een **gebruikersnaam** en **wachtwoord**in en selecteer **Volgende**.

    ![Een cluster koppelen, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selecteer **Finish**. Als de clusterkoppeling is geslaagd, wordt het cluster vervolgens weergegeven onder het knooppunt **HDInsight.**

Als u een gekoppeld cluster wilt bijwerken, klikt u met de rechtermuisknop op het cluster en selecteert u **Bewerken**. U vervolgens de clusterinformatie bijwerken.

![Een gekoppeld cluster bewerken, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Gekoppelde resources verkennen
In Server Explorer worden het standaardaccount voor opslag en alle gekoppelde opslagaccounts weergegeven. Als u het standaardopslagaccount uitvouwt, kunt u de containers op het opslagaccount zien. Het standaardopslagaccount en de standaardcontainer worden gemarkeerd.

![Data Lake-hulpprogramma's voor gekoppelde bronnen in Visual Studio in Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Klik met de rechtermuisknop op een container en selecteer **Container weergeven** om de inhoud van de container weer te geven. Nadat u een container hebt geopend, u de werkbalkknoppen gebruiken om de inhoudslijst, **Blob uploaden,** **Geselecteerde blobs verwijderen,** **Blob openen**en geselecteerde blobs downloaden (**Opslaan als).** **Refresh**

![Containerlijst- en blobbewerkingen, HDInsight-cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Interactieve Apache Hive-query's uitvoeren
[Apache Hive](https://hive.apache.org) is een datawarehouse-infrastructuur die is gebouwd op Hadoop. Hive wordt gebruikt voor gegevenssamenvatting, query's en analyse. U kunt Data Lake Tools voor Visual Studio gebruiken om Hive-query's uit te voeren vanuit Visual Studio. Zie [Wat is Apache Hive en HiveQL op Azure HDInsight voor](hdinsight-use-hive.md)meer informatie over Hive.

[Interactieve query in Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) maakt gebruik [van Hive op LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query brengt interactiviteit naar complexe query's in datawarehousestijl op grote, opgeslagen gegevenssets. Het uitvoeren van Hive-query's op Interactive Query is veel sneller dan traditionele Hive-batchtaken. 

> [!NOTE]  
> U kunt alleen interactieve Hive-query's uitvoeren wanneer u verbinding maakt met een [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-cluster.

U kunt Data Lake Tools voor Visual Studio ook gebruiken om te zien wat er in een Hive-taak zit. Data Lake Tools voor Visual Studio verzamelt de Yarn-logboeken van bepaalde Hive-taken en maakt ze zichtbaar.

Kies **vanuit Server Explorer** **Azure** > **HDInsight** en selecteer uw cluster.  Dit knooppunt is het startpunt in **Server Explorer** voor de volgende secties.

### <a name="view-hivesampletable"></a>hivesampletable bekijken

Alle HDInsight-clusters hebben een `hivesampletable`standaardvoorbeeldhive-tabel met de naam .  

Kies in uw cluster **de** > **standaardhivesampletabel****default** > van Hive Databases .

- Ga als `hivesampletable` het gaat om het schema:

    **Hivesampletable uitvouwen**. De namen en gegevenstypen van de `hivesampletable` kolommen worden weergegeven.

- Ga als `hivesampletable` het gaat om het bekijken van de gegevens:

    Klik met de rechtermuisknop op **de tabel Hivesampleen**en selecteer **Top 100-rijen weergeven**. De lijst met 100 resultaten wordt weergegeven in het venster **HiveSampletable.** Deze actie is gelijk aan het uitvoeren van de volgende Hive-query met behulp van het Hive ODBC-stuurprogramma:

    `SELECT * FROM hivesampletable LIMIT 100`

    U het aantal rijen aanpassen door **het aantal rijen te**wijzigen; u 50, 100, 200 of 1000 rijen kiezen uit de vervolgkeuzelijst.

### <a name="create-hive-tables"></a>Hive-tabellen maken
U een Hive-tabel maken door de GUI te gebruiken of door Hive-query's te gebruiken. Zie [Hive-query's maken en uitvoeren](#create-and-run-hive-queries)voor informatie over het gebruik van Hive-query's.

1. Kies standaard **hive-databases in** > **default**uw cluster .

2. Klik met **de rechtermuisknop op standaard**en selecteer Tabel **maken**.

3. Configureer de tabel.

4. Selecteer de knop **Tabel maken** om de taak in te dienen, waardoor de nieuwe tabel Hive wordt gemaakt.

    ![Tabelvenster, Hive, HDInsight-cluster, Visual Studio maken](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Hive-query's maken en uitvoeren
U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hocquery's maken
* Een Hive-toepassing maken

#### <a name="create-an-ad-hoc-query"></a>Een ad-hocquery maken

Een ad-hocquery maken en uitvoeren:

1. Klik met de rechtermuisknop op het cluster waar u de query wilt uitvoeren en selecteer **Een bijenkorfquery schrijven**.  

2. Voer een Hive-query in.

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld `SELECT * FROM`typt, worden intelliSense alle voorgestelde tabelnamen weergegeven. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive.

    ![IntelliSense voorbeeld 1, Hive ad-hocquery, HDInsight-cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense voorbeeld 2, Hive ad-hocquery, HDInsight-cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

    Hier is een voorbeeldquery die u gebruiken:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Kies de uitvoeringsmodus:

    * **Interactieve**  

        Kies in de eerste vervolgkeuzelijst **Interactief**en selecteer **Vervolgens Uitvoeren**.

        ![Interactieve modus, Hive ad-hoc query, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Kies in de eerste vervolgkeuzelijst **Batch**en selecteer **Verzenden** (of selecteer het vervolgkeuzepictogram naast **Verzenden** en kies **Geavanceerd**).

        ![Batch-modus, Hive ad-hoc query, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Als u de optie Geavanceerd verzenden selecteert, wordt het dialoogvenster **Script verzenden** weergegeven. Configureer de **taaknaam**, **argumenten,** **aanvullende configuraties**en **statusmap** voor het script.

        ![Dialoogvenster Script verzenden, Ad-hocquery Hive, HDInsight-cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > U geen batches indienen bij clusters voor interactieve query's.  U moet de interactieve modus gebruiken.

#### <a name="create-a-hive-application"></a>Een Hive-toepassing maken

Een Hive-oplossing maken en uitvoeren:

1. Kies **Bestand** > **Nieuw** > **project**in de menubalk .

2. Selecteer in **het venster Een nieuw project maken** het zoekvak en typ **Hive**. Kies vervolgens **Hive-toepassing** en selecteer **Volgende**.

3. Voer in het **venster Uw nieuwe project configureren** een **projectnaam**in, selecteer of maak de **projectlocatie**en selecteer **Vervolgens Maken**.

    ![Nieuwe Hive-toepassing, Configureer uw nieuwe projectvenster, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. Dubbelklik in **Solution Explorer** op **Script.hql** om het script te openen.

### <a name="view-job-summary-and-output"></a>Overzicht en uitvoer van taken bekijken

Het overzicht van de taak varieert enigszins tussen **de batch-** en **de interactieve** modus.

![Hive-overzichtsvensters, batch- en interactieve modus, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Gebruik het pictogram **Vernieuwen** om de status bij te werken totdat de taakstatus is gewijzigd in **Voltooid**.  

* Selecteer de koppelingen onderaan om de **taakquery,** **taakuitvoer**of taaklogboek of **taaklogboek**te bekijken of **garenlogboeken**weer te geven voor de taakgegevens in **de batchmodus.**

* Zie de deelvensters **Uitvoer** en **HiveServer2 uitvoer** voor de taakgegevens van **de modus Interactieve.**

    ![Hive interactieve taakuitvoer, HDInsight-cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Taakgrafiek weergeven

Momenteel worden taakgrafieken alleen weergegeven voor Hive-taken die Tez als uitvoeringsengine gebruiken.  Zie Wat is Apache [Hive en HiveQL op Azure HDInsight voor](hdinsight-use-hive.md)informatie over het inschakelen van Tez.  Zie ook, [Gebruik Apache Tez in plaats van Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Als u alle operatoren in het hoekpunt wilt weergeven, dubbelklikt u op de hoekpunten van de taakgrafiek. U kunt ook een bepaalde operator aanwijzen voor meer informatie over de operator.

Zelfs als Tez is opgegeven als de uitvoeringsengine, wordt de taakgrafiek mogelijk niet weergegeven als er geen Tez-toepassing wordt gestart.  Deze situatie kan optreden omdat de taak geen DML-instructies bevat of omdat de DML-instructies kunnen worden retournerd zonder een Tez-toepassing te starten. `SELECT * FROM table1` Start bijvoorbeeld de Tez-toepassing niet.

![Apache Hive job graph, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Taakuitvoeringsdetail weergeven

In de taakgrafiek u **Taakuitvoeringsdetail** selecteren om gestructureerde en gevisualiseerde informatie voor Hive-taken te krijgen. U ook meer functiedetails krijgen. Als er prestatieproblemen optreden, kunt u de weergave gebruiken om meer informatie over het probleem op te vragen. U bijvoorbeeld informatie ophalen over hoe elke taak werkt en gedetailleerde informatie over elke taak (gegevens lezen/schrijven, planning/begin-/eindtijd en meer). Gebruik de informatie om taakconfiguraties of systeemarchitectuur bij te stellen op basis van de gevisualiseerde informatie.

![Venster Weergave taakuitvoering, Gegevensmeer Visual Studio-hulpprogramma's](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Hive-taken weergeven

U kunt taakquery's, taakuitvoer, logboeken van taken en Yarn-logboeken voor Hive-taken weergeven.

In de meest recente versie van de hulpprogramma's kunt u in uw Hive-taken kijken door Yarn-logboeken te verzamelen en zichtbaar te maken. Een Yarn-logboek kan u helpen bij het onderzoeken van prestatieproblemen. Zie [Access Apache Hadoop YARN-toepassingslogboeken](../hdinsight-hadoop-access-yarn-app-logs-linux.md)voor meer informatie over hoe HDInsight garenlogboeken verzamelt.

Hive-taken weergeven:

1. Klik met de rechtermuisknop op een HDInsight-cluster en selecteer **Taken weergeven**.

    ![Vacatures, Apache Hive, HDInsight-cluster, Visual Studio bekijken](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Er wordt een lijst weergegeven met de Hive-taken die op het cluster zijn uitgevoerd.  

2. Selecteer een taak. Selecteer in het venster Overzicht van **de hive-taak** een van de volgende koppelingen:
    - **Taakquery**
    - **Taakuitvoer**
    - **Takenlogboek**  
    - **Garenlogboek**

## <a name="run-apache-pig-scripts"></a>Apache Pig-scripts uitvoeren

1. Kies **Bestand** > **Nieuw** > **project**in de menubalk .

2. Selecteer **in** het venster Start het zoekvak en voer **Varken**in. Selecteer vervolgens **Varkenstoepassing** en selecteer **Volgende**.

3. Voer in het **venster Uw nieuwe project configureren** een **projectnaam**in en selecteer of maak een **locatie** voor het project. Selecteer vervolgens **Maken**.

4. Dubbelklik in het deelvenster IDE **Solution Explorer** op **Script.pig** om het script te openen.

## <a name="feedback-and-known-issues"></a>Feedback en bekende problemen

* Een probleem waardoor resultaten beginnend met null-waarden niet worden weergegeven, is opgelost. Neem contact op met het ondersteuningsteam als u vastloopt op dit probleem.

* Het HQL-script dat door Visual Studio wordt gemaakt, word gecodeerd op basis van de lokale regio-instellingen van de gebruiker. Het script wordt niet correct uitgevoerd als u het script als binair bestand naar een cluster uploadt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u hoe geleerd u het Data Lake Tools-pakket voor Visual Studio kunt gebruiken om verbinding te maken met HDInsight-clusters vanuit Visual Studio. U hebt ook geleerd hoe u een Hive-query uitvoert. Raadpleeg voor meer informatie de volgende artikelen:

* [Apache Hive-query's uitvoeren met de Data Lake-hulpprogramma's voor Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Wat is Apache Hive en HiveQL op Azure HDInsight?](hdinsight-use-hive.md)
* [Een Apache Hadoop-cluster maken - sjabloon](apache-hadoop-linux-tutorial-get-started.md)
* [Stuur Apache Hadoop vacatures in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analyseer Twitter-gegevens met Apache Hive en Apache Hadoop op HDInsight](../hdinsight-analyze-twitter-data-linux.md)
