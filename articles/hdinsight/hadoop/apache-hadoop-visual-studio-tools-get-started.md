---
title: Apache Hadoop-en Data Lake-Hulpprogram Ma's voor Visual Studio-Azure HDInsight
description: Meer informatie over het installeren en gebruiken van Data Lake-Hulpprogram Ma's voor Visual Studio om verbinding te maken met Apache Hadoop clusters in azure HDInsight en vervolgens Hive-query's uit te voeren.
keywords: hadoop-hulpprogramma's, hive-query, visual studio, visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 44a076ee6979e207ac3992f76d3b89cc188d53b8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076301"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om verbinding te maken met Azure HDInsight en Apache Hive query's uit te voeren

Meer informatie over het gebruik [van Microsoft Azure data Lake en stream Analytics Hulpprogram ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (ook wel Data Lake-hulpprogram ma's genoemd) om verbinding te maken met Apache Hadoop clusters in [Azure HDInsight](../hdinsight-hadoop-introduction.md) en Hive-query's te verzenden.  

Zie [Inleiding tot HDInsight](../hdinsight-hadoop-introduction.md) en [Aan de slag met HDInsight](apache-hadoop-linux-tutorial-get-started.md) voor meer informatie over het gebruik van HDInsight.  

Zie voor meer informatie over het maken van verbinding met een Apache Storm-cluster [topologieën ontwikkelen C# voor Apache Storm op HDInsight met behulp van Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

U kunt Data Lake Tools voor Visual Studio gebruiken voor toegang tot Azure Data Lake Analytics en HDInsight. Zie voor informatie over Data Lake Tools [Zelfstudie: U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt volt ooien en Data Lake-Hulpprogram Ma's voor Visual Studio wilt gebruiken, hebt u de volgende items nodig:

* Een Azure HDInsight-cluster. Zie aan de [slag met Apache Hadoop in azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)om een HDInsight-cluster te maken. Als u interactieve Apache Hive query's wilt uitvoeren, hebt u een [HDInsight Interactive query](../interactive-query/apache-interactive-query-get-started.md) -cluster nodig.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 of hoger).  De [Visual Studio Community-editie](https://visualstudio.microsoft.com/vs/community/) is gratis.  Zie ook [Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) en [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)installeren. Er zijn lichte interface variaties met Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake-Hulpprogram Ma's worden niet meer ondersteund voor Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools voor Visual Studio installeren  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 of Visual Studio 2019  
  Zorg er tijdens de installatie voor dat u ten minste werk belastingen hebt die **Azure-ontwikkeling** of- **gegevens opslag en-verwerking**bevatten.  

  Voor bestaande installaties navigeert u in de menu balk naar **hulpprogram** > ma's**en functies ophalen...** om Visual Studio Installer te openen.  Selecteer vervolgens ten minste de werk belastingen **Azure-ontwikkeling** of **gegevens opslag en-verwerking**.

  ![Scherm opname van Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 en 2015  
  [Down load data Lake-Hulpprogram ma's](https://www.microsoft.com/download/details.aspx?id=49504). Kies de versie van Data Lake Tools die overeenkomt met uw versie van Visual Studio.  

> [!NOTE]  
> Momenteel is alleen de Engelse versie van Data Lake Tools voor Visual Studio beschikbaar.

## <a name="update-data-lake-tools-for-visual-studio"></a>Data Lake-Hulpprogram Ma's voor Visual Studio bijwerken  

1. Open Visual Studio.

2. Ga in de menu balk naar **extra** > **extensies en updates...** .

3. Vouw in het venster **extensies en updates** links **updates** uit.

4. Als er een update beschikbaar is, worden **Azure data Lake en stream analytic-Hulpprogram ma's** weer gegeven in het hoofd venster.  Selecteer **Update**.

> [!NOTE]  
> U kunt alleen Data Lake Tools versie 2.3.0.0 of hoger gebruiken om verbinding te maken met Interactive Query-clusters en interactieve Hive-query's uit te voeren.

## <a name="connect-to-azure-subscriptions"></a>Verbinding maken met Azure-abonnementen
U kunt Data Lake Tools voor Visual Studio gebruiken om verbinding te maken met uw HDInsight-clusters, enkele eenvoudige beheerbewerkingen uit te voeren en Hive-query's uit te voeren.

> [!NOTE]  
> Zie [Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Hive-query's schrijven en verzenden met behulp van Visual Studio) voor informatie over het maken van verbinding met een algemeen Hadoop-cluster.

Verbinding maken met uw Azure-abonnement:

1. Open Visual Studio.

2. Ga in de menu balk naar**Server Explorer** **weer geven** > .

3. Klik vanuit Server Explorer met de rechter muisknop op **Azure**, selecteer **verbinding maken met Microsoft Azure abonnement...** en voltooi het aanmeldings proces.

4. Vanuit Server Explorer wordt een lijst met bestaande HDInsight-clusters weer gegeven. Als u geen clusters hebt, kunt u een cluster maken door gebruik te maken van Azure Portal, Azure PowerShell of de HDInsight SDK. Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie.

   ![Data Lake-Hulpprogram ma's voor Visual Studio-cluster lijst in Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Data Lake-Hulpprogram ma's voor Visual Studio-cluster lijst in Server Explorer")

5. Een HDInsight-cluster uitbreiden. **Hive-data bases**, een standaard opslag account, gekoppelde opslag accounts en **Hadoop-service logboek** worden weer gegeven. U kunt de entiteiten verder uitbreiden.

Nadat u verbinding hebt gemaakt met uw Azure-abonnement, kunt u de volgende taken uitvoeren.

Verbinding maken met Azure Portal vanuit Visual Studio:

1. In Server Explorer gaat u naar **Azure** > **HDInsight** en selecteert u uw cluster.

2. Klik met de rechter muisknop op een HDInsight-cluster en selecteer **cluster beheren in azure Portal [sic]** .

Vragen stellen en/of feedback geven vanuit Visual Studio:

1. Ga vanuit Server Explorer naar **Azure** > **HDInsight**.

2. Klik met de rechter muisknop op **HDInsight** en selecteer **MSDN-forum** om vragen te stellen of **Geef feedback** om feedback te geven.

## <a name="link-a-cluster"></a>Een cluster koppelen
U kunt een cluster koppelen door met de rechter muisknop op **HDInsight** te klikken en vervolgens **een HDInsight-cluster koppelen**te selecteren. Voer de **verbindings-URL**, de **gebruikers naam** en het **wacht woord**in, klik op **volgende** en vervolgens op **volt ooien**. het cluster moet worden vermeld onder HDInsight-knoop punt.

![Scherm afbeelding van het dialoog venster Data Lake-Hulpprogram Ma's voor Visual Studio-koppelings cluster](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Klik met de rechter muisknop op het gekoppelde cluster, selecteer **bewerken**, de gebruiker kan de cluster gegevens bijwerken. Het toevoegen van een HDInsight-cluster ondersteunt alleen component voor nu.

![Scherm opname van Data Lake-Hulpprogram Ma's voor Visual Studio koppeling cluster update](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Gekoppelde resources verkennen
In Server Explorer worden het standaardaccount voor opslag en alle gekoppelde opslagaccounts weergegeven. Als u het standaardopslagaccount uitvouwt, kunt u de containers op het opslagaccount zien. Het standaardopslagaccount en de standaardcontainer worden gemarkeerd. Klik met de rechtermuisknop op een van de containers om de inhoud ervan weer te geven.

![Data Lake-Hulpprogram ma's voor Visual Studio gekoppelde resources in Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Gekoppelde resources weer geven")

Nadat u een container hebt geopend, kunt u met de volgende knoppen blobs uploaden, verwijderen en downloaden:

![Data Lake-Hulpprogram ma's voor Visual Studio BLOB-bewerkingen in Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Blobs in Server Explorer uploaden, verwijderen en downloaden")

## <a name="run-interactive-apache-hive-queries"></a>Interactieve Apache Hive query's uitvoeren
[Apache Hive](https://hive.apache.org) is een datawarehouse-infrastructuur die is gebouwd op Hadoop. Hive wordt gebruikt voor gegevenssamenvatting, query's en analyse. U kunt Data Lake Tools voor Visual Studio gebruiken om Hive-query's uit te voeren vanuit Visual Studio. Zie [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)voor meer informatie over Hive.

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) gebruikt [Hive op LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query brengt interactiviteit naar complexe datawarehouse-achtige query's op grote opgeslagen gegevenssets. Het uitvoeren van Hive-query's in Interactive Query is veel sneller dan de traditionele Hive-batchtaken. 

> [!NOTE]  
> U kunt alleen interactieve Hive-query's uitvoeren wanneer u verbinding maakt met een [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-cluster.

U kunt Data Lake Tools voor Visual Studio ook gebruiken om te zien wat er in een Hive-taak zit. Data Lake Tools voor Visual Studio verzamelt de Yarn-logboeken van bepaalde Hive-taken en maakt ze zichtbaar.

In Server Explorer gaat u naar **Azure** > **HDInsight** en selecteert u uw cluster.  Dit is het begin punt in Server Explorer voor de volgende secties.

### <a name="view-hivesampletable"></a>Hivesampletable weer geven
Alle HDInsight-clusters hebben een standaard voor beeld- `hivesampletable`Hive-tabel met de naam.  

Navigeer vanuit uw cluster naar **Hive-data bases** > **standaard** > **hivesampletable**.

* Schema weer `hivesampletable` geven:  
Vouw **hivesampletable**uit.

* Gegevens weer `hivesampletable` geven:  
Klik met de rechter muisknop op **hivesampletable**en selecteer **Top 100-rijen weer geven**.  Dit is equivalent aan het uitvoeren van de volgende Hive-query met behulp van het Hive ODBC-stuurprogramma:

   `SELECT * FROM hivesampletable LIMIT 100`

  U kunt het aantal rijen aanpassen.

  ![Schermafbeelding van een HDInsight Hive Visual Studio-schemaquery](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Resultaten van Hive-query")

### <a name="create-hive-tables"></a>Hive-tabellen maken
U een Hive-tabel maken door de GUI te gebruiken of door Hive-query's te gebruiken. Zie [Apache Hive Query's uitvoeren](#run.queries)voor meer informatie over het gebruik van Hive-query's.

1. Navigeer vanuit uw cluster naar **Hive-data bases** > **standaard**.

2. Klik met de rechter muisknop op **standaard**en selecteer **tabel maken**.

3. Configureer de tabel naar wens.  

4. Selecteer **Tabel maken** om de taak voor het maken van de nieuwe Hive-tabel te verzenden.

    ![Schermafbeelding van het venster Tabel maken van HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Hive-tabel maken")

### <a name="run.queries"></a>Hive-query's maken en uitvoeren
U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hocquery's maken
* Een Hive-toepassing maken

Ad-hoc query's maken en uitvoeren:

1. Klik met de rechter muisknop op het cluster waar u de query wilt uitvoeren en selecteer **een Hive-query schrijven**.  

2. Voer de volgende Hive-query in:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld typt `SELECT * FROM`, geeft IntelliSense alle voorgestelde tabel namen weer. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive.

    ![Schermafbeelding van een HDInsight Visual Studio Tools IntelliSense-voorbeeld 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Schermafbeelding van een HDInsight Visual Studio Tools IntelliSense-voorbeeld 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

3. Kies de uitvoerings modus:

    * **Interactief**  

      Zorg ervoor dat **interactief** is geselecteerd en selecteer vervolgens **uitvoeren**.

      ![Scherm afbeelding van query en uitvoeren](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Zorg ervoor dat **batch** is geselecteerd en selecteer vervolgens **verzenden**.  Als u de optie Geavanceerde verzen ding selecteert, configureert u de **taak naam**, **argumenten**, **aanvullende configuraties**en de **status Directory** voor het script.

      ![Opties voor Visual Studio-query's en-batch](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Schermafbeelding van een HDInsight Hadoop Hive-query](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Query's verzenden")

      > [!NOTE]  
      > U kunt geen batches indienen bij interactieve query clusters.  U moet de interactieve modus gebruiken.

Een Hive-oplossing maken en uitvoeren:

1. Ga in de menu balk naar **bestand** > **Nieuw** > **project...** .

2. Navigeer in het linkerdeel venster naar **geïnstalleerde** > **Azure data Lake** > **Hive (HDInsight)** .  

3. Selecteer **Hive-toepassing** in het middelste deelvenster. Voer de eigenschappen in en selecteer **OK**.

    ![Schermafbeelding van een nieuw Hive-project in HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Hive-toepassingen maken vanuit Visual Studio")

4. Dubbelklik in **Solution Explorer** op **Script.hql** om het script te openen.

### <a name="view-job-summary-and-output"></a>Taak samenvatting en-uitvoer weer geven

De samen vatting van de taak varieert enigszins tussen **batch** en **interactieve** modus.

![Tabblad Apache Hive taak overzicht weer geven](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Samen vatting van Hive-taak")

Gebruik de knop **vernieuwen** om de status bij te werken totdat de taak status **is**gewijzigd in voltooid.  

* Voor de taak Details van de **batch** modus selecteert u de koppelingen onderaan om de **taak query**, **taak uitvoer**, **taak logboek**of **garen logboek**te zien.

* Zie tabbladen **uitvoer** en **HiveServer2 uitvoer**voor de taak Details van de **interactieve** modus.

  ![Taak Details van Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Details van Hive-taak")

### <a name="view-job-graph"></a>Taak grafiek weer geven

Taak grafieken worden momenteel alleen weer gegeven voor Hive-taken die gebruikmaken van TEZ als uitvoerings engine.  Zie [Apache Hive gebruiken in HDInsight](hdinsight-use-hive.md)voor meer informatie over het inschakelen van TEZ.  Zie ook [Apache TEZ gebruiken in plaats van toewijzings reductie](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Als u alle operators binnen het hoekpunt wilt bekijken, dubbelklikt u op de hoekpunten van de taakgrafiek. U kunt ook een bepaalde operator aanwijzen voor meer informatie over de operator.

De taak grafiek wordt mogelijk niet weer gegeven, zelfs als TEZ is opgegeven als de uitvoerings engine als er geen TEZ-toepassing wordt gestart.  Dit kan gebeuren omdat de taak geen DML-instructies bevat of omdat de DML-instructies kunnen retour neren zonder een TEZ-toepassing te starten. Bijvoorbeeld, wordt `SELECT * FROM table1` de TEZ-toepassing niet gestart.

![Taak diagram van Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Samen vatting van Hive-taak")

### <a name="task-execution-detail"></a>Details van taak uitvoering

Vanuit de taak grafiek kunt u details van **taak uitvoering** selecteren om gestructureerde en gevisualiseerde informatie voor Hive-taken op te halen. U kunt ook meer taakgegevens ophalen. Als er prestatieproblemen optreden, kunt u de weergave gebruiken om meer informatie over het probleem op te vragen. U kunt bijvoorbeeld informatie krijgen over hoe elke taak werkt, en gedetailleerde informatie over elke taak (gegevens lezen/schrijven, geplande/start-/eindtijd, enzovoort). Gebruik de informatie om taakconfiguraties of systeemarchitectuur bij te stellen op basis van de gevisualiseerde informatie.

![Weergave venster voor taak uitvoering van Visual Studio Tools data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Taak uitvoering weer geven")


### <a name="view-hive-jobs"></a>Hive-taken weergeven
U kunt taakquery's, taakuitvoer, logboeken van taken en Yarn-logboeken voor Hive-taken weergeven.

In de meest recente versie van de hulpprogramma's kunt u in uw Hive-taken kijken door Yarn-logboeken te verzamelen en zichtbaar te maken. Een Yarn-logboek kan u helpen bij het onderzoeken van prestatieproblemen. Zie [Programmacode gebruiken om toegang te krijgen tot HDInsight-toepassingslogboeken](../hdinsight-hadoop-access-yarn-app-logs.md) voor informatie over hoe HDInsight Yarn-logboeken verzamelt.

Hive-taken weergeven:

1. Klik met de rechter muisknop op een HDInsight-cluster en selecteer **taken weer geven**. Er wordt een lijst weergegeven met de Hive-taken die op het cluster zijn uitgevoerd.  

2. Selecteer een taak. Selecteer in het venster **Samenvatting van Hive-taak** een van de volgende opties:
    - **Taakquery**
    - **Taakuitvoer**
    - **Takenlogboek**  
    - **Yarn-logboek**

    ![Schermafbeelding van het venster Hive-taken in HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Hive-taken weergeven")


## <a name="run-apache-pig-scripts"></a>Apache Pig-scripts uitvoeren

1. Ga in de menu balk naar **bestand** > **Nieuw** > **project...** .

2. Ga in het linkerdeel venster naar **geïnstalleerde** > **Azure data Lake** > **varken (HDInsight)** .  

3. Selecteer **varkens toepassing**in het middelste deel venster. Voer de eigenschappen in en selecteer **OK**.

4. Dubbel klik in **Solution Explorer**op **script. Pig** om het script te openen.

## <a name="feedback-and-known-issues"></a>Feedback en bekende problemen
* Een probleem waardoor resultaten beginnend met null-waarden niet worden weergegeven, is opgelost. Neem contact op met het ondersteuningsteam als u vastloopt op dit probleem.
* Het HQL-script dat door Visual Studio wordt gemaakt, word gecodeerd op basis van de lokale regio-instellingen van de gebruiker. Het script wordt niet correct uitgevoerd als u het script als binair bestand naar een cluster uploadt.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u hoe geleerd u het Data Lake Tools-pakket voor Visual Studio kunt gebruiken om verbinding te maken met HDInsight-clusters vanuit Visual Studio. U hebt ook geleerd hoe u een Hive-query uitvoert. Raadpleeg voor meer informatie de volgende artikelen:

* [Apache Hive-query's uitvoeren met de Data Lake-hulpprogramma's voor Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Hadoop Hive in HDInsight gebruiken](hdinsight-use-hive.md)
* [Aan de slag met Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken in HDInsight verzenden](submit-apache-hadoop-jobs-programmatically.md)
* [Twitter-gegevens analyseren met Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data.md)

