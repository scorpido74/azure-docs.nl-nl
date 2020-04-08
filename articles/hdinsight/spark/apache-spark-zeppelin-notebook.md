---
title: Zeppelin-laptops & Apache Spark-cluster - Azure HDInsight
description: Stapsgewijze instructies voor het gebruik van Zeppelin-notitieblokken met Apache Spark-clusters op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 3c1369e813ba6518f6cd4b27082020ae36a24c82
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811201"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin-notebooks gebruiken met Apache Spark-cluster in Azure HDInsight

HDInsight Spark-clusters bevatten [Apache Zeppelin-laptops.](https://zeppelin.apache.org/) Gebruik de notitieblokken om [Apache Spark-taken](https://spark.apache.org/) uit te voeren. In dit artikel leert u hoe u de Zeppelin-notebook gebruiken op een HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.
* Het URI-schema voor de primaire opslag van uw clusters. Het schema `wasb://` zou zijn voor `abfs://` Azure Blob Storage, `adl://` voor Azure Data Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Blob `wasbs://`Storage, is de URI .  Zie [Veilige overdracht in Azure Storage vereisen](../../storage/common/storage-require-secure-transfer.md) voor meer informatie.

## <a name="launch-an-apache-zeppelin-notebook"></a>Een Apache Zeppelin-laptop starten

1. Selecteer in het **Overzicht**van het Spark-cluster **het Zeppelin-notitieblok** uit **clusterdashboards**. Voer de beheerdersreferenties voor het cluster in.  

   > [!NOTE]  
   > U ook het Zeppelin-notitieblok voor uw cluster bereiken door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Maak een nieuwe notebook. Navigeer in het koptekstvenster naar **Notitieblok** > **Nieuwe notitie maken**.

    ![Een nieuw Zeppelin-notitieblok maken](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Een nieuw Zeppelin-notitieblok maken")

    Voer een naam in voor het notitieblok en selecteer **Notitie maken**.

3. Zorg ervoor dat de notitieblokkop een verbonden status weergeeft. Het wordt aangeduid door een groene stip in de rechterbovenhoek.

    ![Zeppelin-notitieblokstatus](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin-notitieblokstatus")

4. Laad voorbeeldgegevens in een tijdelijke tabel. Wanneer u een Spark-cluster maakt in HDInsight, wordt het `hvac.csv`voorbeeldgegevensbestand `\HdiSamples\SensorSampleData\hvac`gekopieerd naar het bijbehorende opslagaccount onder .

    Plak in de lege alinea die standaard in het nieuwe notitieblok is gemaakt het volgende fragment.

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

    Druk op **Shift + ENTER** of selecteer de knop **Afspelen** voor de alinea om het fragment uit te voeren. De status in de rechterhoek van de alinea moet worden uitgevoerd van READY, IN BEHANDELING, LOPEND naar VOLTOOID. De uitvoer wordt onder aan dezelfde alinea weergegeven. De screenshot ziet eruit als de volgende afbeelding:

    ![Een tijdelijke tabel maken op basis van ruwe gegevens](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Een tijdelijke tabel maken op basis van ruwe gegevens")

    U ook een titel aan elke alinea geven. Selecteer in de rechterhoek van de alinea het pictogram **Instellingen** (sprocket) en selecteer **titel weergeven**.  

    > [!NOTE]  
    > %spark2-interpreter wordt niet ondersteund in Zeppelin-laptops in alle HDInsight-versies en %sh-interpreter wordt niet meer ondersteund vanaf HDInsight 4.0.

5. U nu Spark SQL-instructies op de `hvac` tabel uitvoeren. Plak de volgende query in een nieuwe alinea. De query haalt de bouw-ID op. Ook het verschil tussen het doel en de werkelijke temperaturen voor elk gebouw op een bepaalde datum. Druk op **Shift + ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    De **%sql-instructie** aan het begin vertelt het notitieblok om de Livy Scala-tolk te gebruiken.

6. Selecteer het pictogram **Staafdiagram** om de weergave te wijzigen.  **instellingen**, worden weergegeven nadat u **Staafdiagram**hebt geselecteerd, u **Toetsen**en **Waarden**kiezen.  De volgende schermafbeelding toont de uitvoer.

    ![Een Spark SQL-instructie uitvoeren met het notitieblok1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Een Spark SQL-instructie uitvoeren met het notitieblok1")

7. U spark SQL-instructies ook uitvoeren met variabelen in de query. In het volgende fragment ziet `Temp`u hoe u een variabele definieert in de query met de mogelijke waarden waarmee u wilt query's. Wanneer u de query voor het eerst uitvoert, wordt een vervolgkeuzelijst automatisch gevuld met de waarden die u voor de variabele hebt opgegeven.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Plak dit fragment in een nieuwe alinea en druk op **SHIFT + ENTER**. Selecteer vervolgens **65** in de vervolgkeuzelijst **Temp.**

8. Selecteer het pictogram **Staafdiagram** om de weergave te wijzigen.  Selecteer vervolgens **instellingen** en breng de volgende wijzigingen aan:

   * **Groepen:**  **Voeg targettemp**toe .  
   * **Waarden:** 1. Datum **verwijderen**.  2. Voeg **temp_diff**toe.  3.  Wijzig de aggregator van **SOM** naar **AVG**.  

     De volgende schermafbeelding toont de uitvoer.

     ![Een Spark SQL-instructie uitvoeren met het notitieblok2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Een Spark SQL-instructie uitvoeren met het notitieblok2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hoe gebruik ik externe pakketten met het notitieblok?

Zeppelin-laptop in apache spark-cluster op HDInsight kan externe pakketten die door de community zijn bijgedragen en die niet in het cluster zijn opgenomen, gebruiken. Zoek in de [Maven-opslagplaats](https://search.maven.org/) naar de volledige lijst met pakketten die beschikbaar zijn. U ook een lijst met beschikbare pakketten uit andere bronnen krijgen. Een volledige lijst met door de community bijgedragen pakketten is bijvoorbeeld beschikbaar bij [Spark-pakketten.](https://spark-packages.org/)

In dit artikel ziet u hoe u het [spark-csv-pakket](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) gebruiken met het Jupyter-notitieblok.

1. Instellingen voor tolken openen. Selecteer in de rechterbovenhoek de aangemelde gebruikersnaam en selecteer **Vervolgens Tolk**.

    ![Tolk starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Schuif naar **livy2**en selecteer **bewerken**.

    ![Tolkinstellingen wijzigen1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Tolkinstellingen wijzigen1")

3. Navigeer naar `livy.spark.jars.packages`de toets en stel `group:id:version`de waarde ervan in de indeling in . Dus als u het [spark-csv-pakket](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) wilt gebruiken, moet u `com.databricks:spark-csv_2.10:1.4.0`de waarde van de sleutel instellen op .

    ![Tolkinstellingen wijzigen2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Tolkinstellingen wijzigen2")

    Selecteer **Opslaan** en vervolgens **OK** om de Livy-tolk opnieuw te starten.

4. Als u wilt weten hoe u komen tot de waarde van de bovenstaande sleutel, hier is hoe.

    a. Zoek het pakket in de Maven Repository. Voor dit artikel gebruikten we [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Verzamel in de opslagplaats de waarden voor **GroupId,** **ArtifactId**en **Version**.

    ![Externe pakketten gebruiken met Jupyter-laptop](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Externe pakketten gebruiken met Jupyter-laptop")

    c. Concatenate de drie waarden, gescheiden door een dikke darm (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Waar worden de Zeppelin-notebooks opgeslagen?

De Zeppelin-notitieblokken worden opgeslagen in de clusterhoofdknooppunten. Dus als u het cluster verwijdert, worden de notitieblokken ook verwijderd. Als u uw notitieblokken wilt behouden voor later gebruik op andere clusters, moet u ze exporteren nadat u klaar bent met het uitvoeren van de taken. Als u een notitieblok wilt exporteren, selecteert u het pictogram **Exporteren** zoals weergegeven in de afbeelding hieronder.

![Notitieblok downloaden](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Het notitieblok downloaden")

Met deze actie slaat u het notitieblok op als JSON-bestand op uw downloadlocatie.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Toegang `Shiro` tot Zeppelin-tolken configureren in ESP-clusters (Enterprise Security Package)

Zoals hierboven vermeld, wordt de `%sh` tolk niet ondersteund vanaf HDInsight 4.0. Aangezien `%sh` interpreter potentiële beveiligingsproblemen introduceert, zoals toegangskeytabs met shell-opdrachten, is deze ook uit HDInsight 3.6 ESP-clusters verwijderd. Dit `%sh` betekent dat de tolk niet beschikbaar is wanneer u standaard op **Nieuwe notitie maken** of in de gebruikersinterface voor tolken klikt.

Gebruikers van bevoorrechte `Shiro.ini` domeinen kunnen het bestand gebruiken om de toegang tot de gebruikersinterface van de tolk te beheren. Alleen deze gebruikers `%sh` kunnen nieuwe tolken maken en `%sh` machtigingen instellen voor elke nieuwe tolk. Als u de `shiro.ini` toegang met het bestand wilt beheren, gebruikt u de volgende stappen:

1. Een nieuwe rol definiëren met een bestaande domeinnaam. In het volgende `adminGroupName` voorbeeld is een groep bevoorrechte gebruikers in AAD. Gebruik geen speciale tekens of witte spaties in de groepsnaam. De tekens `=` na geven de machtigingen voor deze rol. `*`betekent dat de groep volledige machtigingen heeft.

    ```
    [roles]
    adminGroupName = *
    ```

2. Voeg de nieuwe rol voor toegang tot Zeppelin-tolken toe. In het volgende voorbeeld `adminGroupName` krijgen alle gebruikers toegang tot Zeppelin-tolken en kunnen ze nieuwe tolken maken. U meerdere rollen tussen `roles[]`de haakjes in, gescheiden door komma's. Vervolgens hebben gebruikers die over de benodigde machtigingen beschikken, toegang tot Zeppelin-tolken.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy sessiemanagement

De eerste codealinea in uw Zeppelin-notitieblok maakt een nieuwe Livy-sessie in uw cluster. Deze sessie wordt gedeeld in alle Zeppelin-notitieblokken die u later maakt. Als de Livy sessie om welke reden dan ook wordt gedood, zullen er geen banen worden uitgevoerd van de Zeppelin notebook.

In een dergelijk geval moet u de volgende stappen uitvoeren voordat u taken uitvoeren vanuit een Zeppelin-notitieblok.  

1. Start de Livy-tolk opnieuw uit het Zeppelin-notitieblok. Open hiervoor tolkinstellingen door de aangemelde gebruikersnaam in de rechterbovenhoek te selecteren en vervolgens **Tolk**te selecteren .

    ![Tolk starten](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-uitvoer")

2. Schuif naar **livy2**en selecteer **opnieuw starten**.

    ![Start de Livy-tolk opnieuw](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "De Zeppelin-tolk opnieuw starten")

3. Een codecel uitvoeren vanuit een bestaand Zeppelin-notitieblok. Deze code maakt een nieuwe Livy-sessie in het HDInsight-cluster.

## <a name="general-information"></a>Algemene informatie

### <a name="validate-service"></a>Service valideren

Als u de service vanuit Ambari wilt valideren, navigeert u naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` de plaats waar CLUSTERNAME de naam van uw cluster is.

Als u de service wilt valideren vanaf een opdrachtregel, gaat SSH naar het hoofdknooppunt. Overschakelen naar zeppelin `sudo su zeppelin`met behulp van opdracht . Statusopdrachten:

|Opdracht |Beschrijving |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Servicestatus.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Serviceversie.|
|`ps -aux | grep zeppelin`|Identificeer PID.|

### <a name="log-locations"></a>Loglocaties

|Service |Pad |
|---|---|
|zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Serverlogboeken|/var/log/zeppelin|
|Configuratietolk, `Shiro`, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf or /etc/zeppelin/conf|
|PID-map|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Foutopsporingslogboekregistratie inschakelen

1. Navigeer `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` naar de plaats waar CLUSTERNAME de naam van uw cluster is.

1. Navigeer naar **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content.**

1. Wijzigen `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`in .

1. Toevoegen `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Sla wijzigingen op en start de service opnieuw op.

## <a name="next-steps"></a>Volgende stappen

[Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve data-analyse met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [Gebruik HDInsight Tools Plugin voor IntelliJ IDEA om Apache Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [HdInsight Tools Plugin gebruiken voor IntelliJ IDEA om Apache Spark-toepassingen op afstand te debuggen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels beschikbaar voor Jupyter-laptop in Apache Spark-cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
