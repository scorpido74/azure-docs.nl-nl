---
title: Apache Hive met Data Lake-hulpprogram ma's voor Visual Studio-Azure HDInsight
description: Meer informatie over het gebruik van de Data Lake-hulpprogram ma's voor Visual Studio voor het uitvoeren van Apache Hive query's met Apache Hadoop in azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: bded3ac97732a7cd18411cc9f4ef1707dbdf68e3
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735818"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive query's uitvoeren met de Data Lake-hulpprogram ma's voor Visual Studio

Meer informatie over het gebruik van de Data Lake-hulpprogram ma's voor Visual Studio voor het opvragen van Apache Hive. Met de Data Lake-hulpprogram ma's kunt u eenvoudig Hive-query's maken, verzenden en bewaken om te Apache Hadoop op Azure HDInsight.

## <a id="prereq"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (een van de volgende versies):

    * Visual Studio 2015, 2017 (alle edities)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate met update 4

* HDInsight-hulpprogram ma's voor Visual Studio of Azure Data Lake-hulpprogram ma's voor Visual Studio. Zie [aan de slag met Visual Studio Hadoop-hulpprogram ma's voor HDInsight](apache-hadoop-visual-studio-tools-get-started.md) voor meer informatie over het installeren en configureren van de hulpprogram ma's.

## <a id="run"></a>Apache Hive query's uitvoeren met Visual Studio

U hebt twee opties voor het maken en uitvoeren van Hive-query's:

* Ad-hocquery's maken
* Een Hive-toepassing maken

### <a name="ad-hoc"></a>Ad-hoc

Ad hoc-query's kunnen worden uitgevoerd in **batch** of **interactieve** modus.

1. Open **Visual Studio**.

2. Ga vanuit **Server Explorer**naar **Azure** > **HDInsight**.

3. Vouw **HDInsight**uit en klik met de rechter muisknop op het cluster waar u de query wilt uitvoeren en selecteer vervolgens **een Hive-query schrijven**.

4. Voer de volgende Hive-query in:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecteer **Uitvoeren**. Houd er rekening mee dat de uitvoerings modus standaard wordt ingesteld op **interactief**.

    ![Schermafbeelding van het uitvoeren van interactieve Hive-query’s](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Als u dezelfde query wilt uitvoeren in de **batch** modus, schakelt u in de vervolg keuzelijst van **interactief** naar **batch**. Houd er rekening mee dat de knop uitvoering wordt gewijzigd van **uitvoeren** naar **verzenden**.

    ![Schermafbeelding van het verzenden van een Hive-query](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    De Hive-editor ondersteunt IntelliSense. Data Lake Tools voor Visual Studio biedt ondersteuning voor het laden van externe metagegevens wanneer u het Hive-script bewerkt. Als u bijvoorbeeld typt `SELECT * FROM`, geeft IntelliSense alle voorgestelde tabel namen weer. Wanneer een tabelnaam wordt opgegeven, geeft IntelliSense de kolomnamen weer. De hulpprogramma's ondersteunen de meeste DML-instructies, subquery's en ingebouwde UDF's van Hive. IntelliSense suggereert alleen de metagegevens van het cluster dat in de HDInsight-werkbalk is geselecteerd.

    ![Schermafbeelding van een HDInsight Visual Studio Tools IntelliSense-voorbeeld 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Schermafbeelding van een HDInsight Visual Studio Tools IntelliSense-voorbeeld 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Selecteer **Verzenden** of **Verzenden (geavanceerd)** .

   Als u de optie voor geavanceerd verzenden selecteert, configureert u de **Taaknaam**, **Argumenten**, **Aanvullende configuraties** en **Statusmap** voor het script:

    ![Schermafbeelding van een HDInsight Hadoop Hive-query](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Query's verzenden")

### <a name="hive-application"></a>Hive-toepassing

1. Open **Visual Studio**.

2. Ga in de menu balk naar **bestand** > **Nieuw** > **project**.

3. Navigeer in het venster **New Project** naar **sjablonen** > **Azure data Lake** > **Hive (HDInsight)**  > **component Application**. 

4. Geef een naam op voor dit project en selecteer **OK**.

5. Open het **script. HQL** -bestand dat is gemaakt met dit project en plak de volgende HiveQL-instructies:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Met deze instructies worden de volgende acties uitgevoerd:

   * `DROP TABLE`: Als de tabel bestaat, wordt deze door deze instructie verwijderd.

   * `CREATE EXTERNAL TABLE`: Hiermee maakt u een nieuwe externe tabel in Hive. Externe tabellen slaan de tabel definitie in Hive alleen op (de gegevens blijven op de oorspronkelijke locatie).

     > [!NOTE]  
     > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een MapReduce-taak of Azure-service.
     >
     > Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar alleen de tabel definitie.

   * `ROW FORMAT`: Hiermee wordt aangegeven hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.

   * `STORED AS TEXTFILE LOCATION`: Hiermee wordt aangegeven dat de gegevens worden opgeslagen in de map voor beeld/gegevens en dat deze als tekst wordt opgeslagen.

   * `SELECT`: Selecteer een telling van alle rijen waarin de `t4` kolom de waarde `[ERROR]`bevat. Deze instructie retourneert een waarde van `3` , omdat er drie rijen met deze waarde zijn.

   * `INPUT__FILE__NAME LIKE '%.log'`-Geeft aan dat de Hive alleen gegevens moet retour neren van bestanden die eindigen op. log. Met deze component wordt de zoek opdracht beperkt tot het voorbeeld logboek bestand dat de gegevens bevat.

6. Selecteer op de werk balk het **HDInsight-cluster** dat u wilt gebruiken voor deze query. Selecteer **verzenden** om de instructies als een Hive-taak uit te voeren.

   ![Verzend balk](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Het **overzicht** van de Hive-taak wordt weer gegeven en geeft informatie weer over de taak die wordt uitgevoerd. Gebruik de koppeling **vernieuwen** om de taak informatie te vernieuwen totdat de **taak status** is gewijzigd in **voltooid**.

   ![taak overzicht waarin een voltooide taak wordt weer gegeven](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Gebruik de koppeling **taak uitvoer** om de uitvoer van deze taak weer te geven. Dit wordt `[ERROR] 3`weer gegeven. Dit is de waarde die wordt geretourneerd door deze query.

### <a name="additional-example"></a>Extra voor beeld

Dit voor beeld is afhankelijk van `log4jLogs` de tabel die in de vorige stap is gemaakt.

1. Klik vanuit **Server Explorer**met de rechter muisknop op uw cluster en selecteer **een Hive-query schrijven**.

2. Voer de volgende Hive-query in:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Met deze instructies worden de volgende acties uitgevoerd:

    * `CREATE TABLE IF NOT EXISTS`: Hiermee wordt een tabel gemaakt als deze nog niet bestaat. Omdat het `EXTERNAL` sleutel woord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-Data Warehouse en worden beheerd door Hive.
    
    > [!NOTE]  
    > In tegens telling tot `EXTERNAL` tabellen, verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

    * `STORED AS ORC`: Hiermee worden de gegevens opgeslagen in de ORC-indeling (Optimized Row in kolommen). ORC is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.
    
    * `INSERT OVERWRITE ... SELECT`: Hiermee worden rijen uit `log4jLogs` de tabel die `[ERROR]`bevatten en worden de gegevens in de `errorLogs` tabel ingevoegd.

3. Voer de query uit in **batch** modus.

4. Als u wilt controleren of de tabel is gemaakt, gebruikt u **Server Explorer** en vouwt u **Azure** > **hdinsight** uit > uw HDInsight-cluster > **Hive-data bases** > **standaard**. De tabel **errorLogs** en de tabel **log4jLogs** worden weer gegeven.

## <a id="nextsteps"></a>Volgende stappen

Zoals u kunt zien, bieden de HDInsight-hulpprogram ma's voor Visual Studio een eenvoudige manier om te werken met hive-query's op HDInsight.

Voor algemene informatie over Hive in HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Pig gebruiken met Apache Hadoop op HDInsight](hdinsight-use-pig.md)

* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)

Voor meer informatie over de HDInsight-hulpprogram ma's voor Visual Studio:

* [Aan de slag met HDInsight-hulpprogram ma's voor Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)