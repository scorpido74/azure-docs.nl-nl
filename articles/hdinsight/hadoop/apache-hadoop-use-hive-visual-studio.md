---
title: Apache Hive & Data Lake-tools voor Visual Studio - Azure HDInsight
description: Meer informatie over het gebruik van de Data Lake-hulpprogramma's voor Visual Studio om Apache Hive-query's uit te voeren met Apache Hadoop op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687807"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive-query's uitvoeren met de Data Lake-hulpprogramma's voor Visual Studio

Meer informatie over het gebruik van de Data Lake-tools voor Visual Studio om Apache Hive op te vragen. Met de Data Lake-hulpprogramma's u eenvoudig Hive-query's maken, verzenden en controleren naar Apache Hadoop op Azure HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Apache Hadoop-cluster maken in Azure HDInsight met behulp van resourcebeheersjabloon](./apache-hadoop-linux-tutorial-get-started.md)voor informatie over het maken van dit item.

* [Visual Studio](https://visualstudio.microsoft.com/vs/). De stappen in dit artikel maken gebruik van Visual Studio 2019.

* HDInsight-hulpprogramma's voor Visual Studio- of Azure Data Lake-hulpprogramma's voor Visual Studio. Zie Data Lake Tools voor Visual [Studio installeren voor](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)informatie over het installeren en configureren van de hulpprogramma's.

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Apache Hive-query's uitvoeren met de Visual Studio

U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hocquery's maken.
* Maak een Hive-toepassing.

### <a name="create-an-ad-hoc-hive-query"></a>Een ad-hoc Hive-query maken

Ad hoc query's kunnen worden uitgevoerd in **de batch-** of **interactieve** modus.

1. Start **Visual Studio** en selecteer Doorgaan zonder **code**.

2. Klik vanuit **Server Explorer**met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement...** en voltooi het aanmeldingsproces.

3. HdInsight uitvouwen, met de rechtermuisknop op het cluster klikken waar u de query wilt uitvoeren en vervolgens **Een bijenkorfquery schrijven selecteren**. **HDInsight**

4. Voer de volgende bijenkorfquery in:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecteer **Uitvoeren**. De uitvoeringsmodus wordt standaard ingesteld op **Interactief**.

    ![Interactieve Hive-query uitvoeren, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Als u dezelfde query wilt uitvoeren in **de batchmodus,** schakelt u de vervolgkeuzelijst in van **Interactief** naar **Batch**. De uitvoeringsknop wordt gewijzigd **van Uitvoeren** naar **Verzenden**.

    ![Batch Hive-query verzenden, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld `SELECT * FROM`typt, worden intelliSense alle voorgestelde tabelnamen weergegeven. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive. IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

7. Selecteer op de werkbalk query (het gebied onder het querytabblad en boven de **querytekst)** Verzenden of selecteer de uithaalpijl naast **Verzenden** en kies **Geavanceerd** in de lijst met uithaalgegevens. Als u de laatste optie selecteert,

8. Als u de optie Geavanceerd verzenden hebt geselecteerd, configureert u **Taaknaam**, **Argumenten**, **Aanvullende configuraties**en **Statusmap** in het dialoogvenster **Script verzenden.** Selecteer vervolgens **Verzenden**.

    ![Dialoogvenster Script verzenden, HDInsight Hadoop Hive-query](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Een Hive-toepassing maken

Voer de volgende stappen uit om een Hive-query uit te voeren door een Hive-toepassing te maken:

1. Visual **Studio openen**.

2. Selecteer **in** het venster Start de optie **Een nieuw project maken**.

3. Voer in **het venster Een nieuw project maken** in het vak Zoeken naar **sjablonen** *in Hive*in . Kies vervolgens **Hive-toepassing** en selecteer **Volgende**.

4. Voer in het **venster Uw nieuwe project configureren** een **projectnaam**in, selecteer of maak een **locatie** voor het nieuwe project en selecteer **Vervolgens Maken**.

5. Open het **script.hql-bestand** dat met dit project is gemaakt en plak de volgende HiveQL-instructies:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Deze verklaringen doen de volgende acties:

    * `DROP TABLE`: Hiermee verwijdert u de tabel als deze bestaat.

    * `CREATE EXTERNAL TABLE`: Hiermee maakt u een nieuwe 'externe' tabel in Hive. Externe tabellen slaan alleen de tabeldefinitie op in Hive. (De gegevens worden op de oorspronkelijke locatie achtergelaten.)

        > [!NOTE]  
        > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron, zoals een taak MapReduce of een Azure-service.
        >
        > Als u een externe tabel laat vallen, worden de gegevens **niet** verwijderd, alleen de tabeldefinitie.

    * `ROW FORMAT`: Hiermee vertelt Hive hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.

    * `STORED AS TEXTFILE LOCATION`: Hiermee vertelt Hive dat de gegevens zijn opgeslagen in de *voorbeeld/gegevensmap* en dat deze als tekst zijn opgeslagen.

    * `SELECT`: Hiermee selecteert u een `t4` telling van `[ERROR]`alle rijen waar kolom de waarde bevat. Met deze instructie `3`wordt een waarde geretourneerd van , omdat drie rijen deze waarde bevatten.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hiermee wordt Bijhive opdedag gekregen dat gegevens van bestanden die eindigen op .log alleen gegevens moeten retourneren. Deze clausule beperkt de zoekopdracht tot het *voorbeeld.log-bestand* dat de gegevens bevat.

6. Selecteer op de werkbalk van het querybestand (die een vergelijkbare weergave heeft als de ad-hocquerywerkbalk), het HDInsight-cluster dat u voor deze query wilt gebruiken. Wijzig **vervolgens Interactief** in **Batch** (indien nodig) en selecteer **Verzenden** om de instructies uit te voeren als een Hive-taak.

   Het **overzicht van de hive-taak** wordt weergegeven en geeft informatie weer over de lopende taak. Gebruik de koppeling **Vernieuwen** om de taakgegevens te vernieuwen totdat de **taakstatus** wordt gewijzigd in **Voltooid**.

   ![Voltooid hive-overzicht, Hive-toepassing, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecteer **Taakuitvoer** om de uitvoer van deze taak weer te geven. Het `[ERROR] 3`wordt weergegeven , wat de waarde is die door deze query wordt geretourneerd.

### <a name="additional-example"></a>Extra voorbeeld

Het volgende voorbeeld is `log4jLogs` gebaseerd op de tabel die in de vorige procedure is gemaakt, [Een Hive-toepassing maken.](#create-a-hive-application)

1. Klik in **Server Explorer**met de rechtermuisknop op het cluster en selecteer **Een bijenkorfquery schrijven**.

2. Voer de volgende bijenkorfquery in:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze verklaringen doen de volgende acties:

    * `CREATE TABLE IF NOT EXISTS`: Hiermee maakt u een tabel als deze nog niet bestaat. Omdat `EXTERNAL` het trefwoord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-gegevensmagazijn en worden beheerd door Hive.

        > [!NOTE]  
        > In `EXTERNAL` tegenstelling tot tabellen verwijdert het laten vallen van een interne tabel ook de onderliggende gegevens.

    * `STORED AS ORC`: Slaat de gegevens op *in de orc-indeling (optimized row columnar).* ORC is een zeer geoptimaliseerd en efficiënt formaat voor het opslaan van Hive-gegevens.

    * `INSERT OVERWRITE ... SELECT`: Hiermee selecteert `log4jLogs` u rijen `[ERROR]`uit de tabel die `errorLogs` deze bevatten en voegt u de gegevens in de tabel in.

3. Wijzig indien nodig **Interactief** in **Batch** en selecteer **Verzenden**.

4. Ga naar **Server Explorer** en vouw **Azure** > **HDInsight**uit om te controleren of de taak de tabel heeft gemaakt. Vouw uw HDInsight-cluster uit en vouw**vervolgens standaard** **Hive-databases** > uit. De **tabel errorLogs** en de **log4jLogs-tabel** worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

Zoals u zien, bieden de HDInsight-tools voor Visual Studio een eenvoudige manier om met Hive-query's op HDInsight te werken.

* Zie Wat is Apache Hive [en HiveQL op Azure HDInsight voor](hdinsight-use-hive.md) algemene informatie over Hive in HDInsight?

* Zie [MapReduce in Apache Hadoop op HDInsight gebruiken](hdinsight-use-mapreduce.md) voor meer informatie over andere manieren waarop u met Hadoop werken.

* Zie[Data Lake Tools voor Visual Studio gebruiken om verbinding te maken met Azure HDInsight en Apache Hive-query's uit te voeren voor](apache-hadoop-visual-studio-tools-get-started.md) meer informatie over de HDInsight-tools voor Visual Studio.
