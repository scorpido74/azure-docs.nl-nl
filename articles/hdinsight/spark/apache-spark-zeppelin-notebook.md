---
title: Zeppelin-notebooks & Apache Spark cluster-Azure HDInsight
description: Stapsgewijze instructies voor het gebruik van Zeppelin-notebooks met Apache Spark clusters in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c5c8a41aef92876ceaa66fb23c01c6ece1609f91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484805"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin-notebooks gebruiken met Apache Spark cluster in azure HDInsight

HDInsight Spark-clusters zijn [Apache Zeppelin](https://zeppelin.apache.org/) -notebooks die u kunt gebruiken om [Apache Spark](https://spark.apache.org/) -taken uit te voeren. In dit artikel leert u hoe u de Zeppelin-notebook kunt gebruiken in een HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* Het URI-schema voor de primaire opslag van uw clusters. Dit wordt `wasb://` voor Azure Blob Storage `abfs://` voor Azure Data Lake Storage Gen2 of `adl://` voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Blob Storage, wordt de URI `wasbs://`.  Zie [veilige overdracht vereisen in azure Storage](../../storage/common/storage-require-secure-transfer.md) voor meer informatie.

## <a name="launch-an-apache-zeppelin-notebook"></a>Een Apache Zeppelin-notebook starten

1. Selecteer in het **overzicht**van het Spark-cluster **Zeppelin notebook** in **cluster dashboards**. Voer de beheerders referenties in voor het cluster.  

   > [!NOTE]  
   > U kunt ook de Zeppelin-notebook voor uw cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Maak een nieuwe notebook. Ga in het deel venster koptekst naar **Notebook** > **nieuwe notitie maken**.

    ![Een nieuwe Zeppelin-notebook maken](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Een nieuwe Zeppelin-notebook maken")

    Voer een naam in voor het notitie blok en selecteer vervolgens **notitie maken**.

3. Zorg ervoor dat de kop van het notitie blok een verbonden status bevat. Deze wordt aangeduid met een groene stip in de rechter bovenhoek.

    ![Status van Zeppelin-notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status van Zeppelin-notebook")

4. Laad voorbeeldgegevens in een tijdelijke tabel. Wanneer u een Spark-cluster in HDInsight maakt, wordt het bestand met voorbeeld gegevens `hvac.csv`gekopieerd naar het gekoppelde opslag account onder `\HdiSamples\SensorSampleData\hvac`.

    Plak het volgende fragment in de lege alinea die standaard wordt gemaakt in het nieuwe notitie blok.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Druk op **SHIFT + ENTER** of selecteer de knop **afspelen** voor de alinea om het fragment uit te voeren. De status in de rechter bovenhoek van de alinea moet worden uitgevoerd vanaf gereed, in behandeling en wordt uitgevoerd op voltooid. De uitvoer wordt weer gegeven aan de onderkant van dezelfde alinea. De scherm afbeelding ziet er als volgt uit:

    ![Een tijdelijke tabel maken op basis van onbewerkte gegevens](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Een tijdelijke tabel maken op basis van onbewerkte gegevens")

    U kunt ook een titel voor elke alinea opgeven. Selecteer in de rechter bovenhoek van de alinea het **instellingen** pictogram (Sprocket) en selecteer vervolgens **titel weer geven**.  

    > [!NOTE]  
    > % spark2-interpreter wordt niet ondersteund in Zeppelin-notebooks voor alle HDInsight-versies en% sh-interpreter wordt niet ondersteund vanaf HDInsight 4,0.

5. U kunt nu Spark SQL-instructies uitvoeren op de `hvac` tabel. Plak de volgende query in een nieuwe alinea. Met de query worden de gebouw-ID en het verschil tussen het doel en de werkelijke Tempe raturen voor elk gebouw op een bepaalde datum opgehaald. Druk op **SHIFT + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    De **% SQL** -instructie aan het begin geeft aan dat het notitie blok de livy scala-interpreter moet gebruiken.

6. Selecteer het pictogram van het **staaf diagram** om de weer gave te wijzigen.  de **instellingen**die worden weer gegeven nadat u een **staaf diagram**hebt geselecteerd, kunt u kiezen uit **sleutels**en **waarden**.  In de volgende scherm afbeelding ziet u de uitvoer.

    ![Een Spark SQL-instructie uitvoeren met behulp van de notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Een Spark SQL-instructie uitvoeren met behulp van de notebook1")

7. U kunt ook Spark SQL-instructies uitvoeren met behulp van variabelen in de query. Het volgende code fragment laat zien hoe u een variabele definieert, `Temp`in de query met de mogelijke waarden waarmee u een query wilt uitvoeren. Wanneer u de query voor het eerst uitvoert, wordt een vervolg keuzelijst automatisch gevuld met de waarden die u hebt opgegeven voor de variabele.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Plak dit fragment in een nieuwe alinea en druk op **SHIFT + ENTER**. Selecteer vervolgens **65** in de vervolg keuzelijst **temp** .

8. Selecteer het pictogram van het **staaf diagram** om de weer gave te wijzigen.  Selecteer vervolgens **instellingen** en breng de volgende wijzigingen aan:

   * **Groepen:**  Voeg **targettemp**toe.  
   * **Waarden:** i. Verwijder de **datum**.  2. Voeg **temp_diff**toe.  3.  Wijzig de aggregator van **Sum** in **AVG**.  

     In de volgende scherm afbeelding ziet u de uitvoer.

     ![Een Spark SQL-instructie uitvoeren met behulp van de notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Een Spark SQL-instructie uitvoeren met behulp van de notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Met het notitie blok Hoe kan ik externe pakketten gebruiken?

U kunt de Zeppelin-notebook in Apache Spark cluster in HDInsight configureren voor het gebruik van externe, door de Community bijgedragen pakketten die niet zijn opgenomen in het cluster. U kunt in de [maven-opslag plaats](https://search.maven.org/) zoeken naar de volledige lijst met pakketten die beschikbaar zijn. U kunt ook een lijst met beschik bare pakketten uit andere bronnen ophalen. Een volledige lijst met door de Community bijgedragen pakketten is bijvoorbeeld beschikbaar in [Spark-pakketten](https://spark-packages.org/).

In dit artikel ziet u hoe u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket gebruikt met het Jupyter-notebook.

1. Open de instellingen voor de interpreter. Selecteer in de rechter bovenhoek de naam van de aangemelde gebruiker en selecteer vervolgens **interpreter**.

    ![Interpreter starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Ga naar **livy2**en selecteer **bewerken**.

    ![Settings1 van interpreter wijzigen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Settings1 van interpreter wijzigen")

3. Navigeer naar Key `livy.spark.jars.packages`en stel de waarde ervan in de notatie `group:id:version`. Als u het [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pakket wilt gebruiken, moet u dus de waarde van de sleutel instellen op `com.databricks:spark-csv_2.10:1.4.0`.

    ![Settings2 van interpreter wijzigen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Settings2 van interpreter wijzigen")

    Selecteer **Opslaan** en klik vervolgens op **OK** om de livy-interpreter opnieuw op te starten.

4. Als u wilt weten hoe u kunt aankomen met de waarde van de hierboven ingevoerde sleutel, kunt u dit als volgt doen.

    a. Zoek het pakket in de Maven-opslag plaats. Voor dit artikel hebben we [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)gebruikt.

    b. Verzamel de waarden voor **GroupId**, **ArtifactId**en **Version**uit de opslag plaats.

    ![Externe pakketten gebruiken met Jupyter notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter notebook")

    c. De drie waarden samen voegen, gescheiden door een dubbele punt ( **:** ).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Waar worden de Zeppelin-notebooks opgeslagen?

De Zeppelin-notebooks worden opgeslagen in het cluster hoofd knooppunten. Als u het cluster verwijdert, worden ook de notitie blokken verwijderd. Als u uw notitie blokken voor later gebruik op andere clusters wilt behouden, moet u deze exporteren nadat u de taken hebt uitgevoerd. Als u een notitie blok wilt exporteren, selecteert u het pictogram **exporteren** , zoals wordt weer gegeven in de onderstaande afbeelding.

![Notitie blok downloaden](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Het notitie blok downloaden")

Hiermee wordt het notitie blok opgeslagen als een JSON-bestand in de download locatie.

## <a name="livy-session-management"></a>Livy-sessie beheer

Wanneer u de eerste code alinea in uw Zeppelin-notebook uitvoert, wordt er een nieuwe livy-sessie in uw HDInsight Spark-cluster gemaakt. Deze sessie wordt gedeeld in alle Zeppelin-notitie blokken die u vervolgens maakt. Als de livy-sessie om de een of andere reden wordt afgebroken (het opnieuw opstarten van het cluster, enzovoort), kunt u geen taken uitvoeren vanuit de Zeppelin-notebook.

In dat geval moet u de volgende stappen uitvoeren voordat u taken kunt uitvoeren vanuit een Zeppelin-notebook.  

1. Start de livy-interpreter vanuit de Zeppelin-notebook. Hiertoe opent u de instellingen voor de interpreter door de naam van de aangemelde gebruiker te selecteren in de rechter bovenhoek en vervolgens **interpreter**te selecteren.

    ![Interpreter starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Ga naar **livy2**en selecteer **opnieuw starten**.

    ![De livy-interpreter opnieuw opstarten](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "De Zeppelin-interpreter opnieuw opstarten")

3. Een code-cel uitvoeren vanuit een bestaand Zeppelin-notebook. Hiermee maakt u een nieuwe livy-sessie in het HDInsight-cluster.

## <a name="general-information"></a>Algemene informatie

### <a name="validate-service"></a>Service valideren

Als u de service van Ambari wilt valideren, gaat u naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` waarbij CLUSTERNAME de naam van uw cluster is.

Als u de service vanaf een opdracht regel wilt valideren, moet u SSH naar het hoofd knooppunt. Schakel de gebruiker over naar Zeppelin met behulp van de opdracht `sudo su zeppelin`. Status opdrachten:

|Opdracht |Beschrijving |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Status van de service.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Service versie.|
|`ps -aux | grep zeppelin`|PID identificeren.|

### <a name="log-locations"></a>Logboek locaties

|Service |Pad |
|---|---|
|Zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Serverlogboeken|/var/log/zeppelin|
|Configuratie-Interpreter, Shiro, site. XML, Log4J|/usr/HDP/Current/Zeppelin-server/conf of/etc/Zeppelin/conf|
|PID-map|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Inschakelen van logboekregistratie voor foutopsporing

1. Navigeer naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` waarbij CLUSTERNAME de naam van uw cluster is.

1. Ga naar **configuratie** > **Geavanceerde Zeppelin-log4j-Properties** > **log4j_properties_content**.

1. Wijzig `log4j.appender.dailyfile.Threshold = INFO` in `log4j.appender.dailyfile.Threshold = DEBUG`.

1. Voeg `log4j.logger.org.apache.zeppelin.realm=DEBUG`toe.

1. Sla de wijzigingen op en start de service opnieuw.

## <a name="next-steps"></a>Volgende stappen

[Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario 's

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [De invoeg toepassing HDInsight tools for IntelliJ idee gebruiken om Apache Spark scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
