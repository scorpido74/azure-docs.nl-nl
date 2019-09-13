---
title: Apache Ambari-weer gaven gebruiken om te werken met hive op HDInsight (Apache Hadoop)-Azure
description: Meer informatie over het gebruik van de Hive-weer gave in uw webbrowser om Hive-query's in te dienen. De Hive-weer gave maakt deel uit van de Ambari-webinterface die is meegeleverd met het HDInsight-cluster op basis van Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 3ab2bf0334b58f3a5ac8ad4abacfcc45e0366240
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917741"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Apache Ambari-Hive-weer gave gebruiken met Apache Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het uitvoeren van Hive-query's met behulp van de Apache Ambari-Hive-weer gave. Met de Hive-weer gave kunt u Hive-query's ontwerpen, optimaliseren en uitvoeren vanuit uw webbrowser.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Een webbrowser

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Selecteer uw cluster in de [Azure Portal](https://portal.azure.com/).  Zie een [lijst en clusters weer geven](../hdinsight-administer-use-portal-linux.md#showClusters) voor instructies. Het cluster wordt geopend in een nieuwe portal-Blade.

2. Selecteer **Ambari-weer gaven**in **cluster dashboards**. Wanneer u wordt gevraagd om te verifiëren, gebruikt u de `admin`account naam en het wacht woord voor de cluster aanmelding (standaard) die u hebt opgegeven tijdens het maken van het cluster.

3. Selecteer in de lijst met weer gaven de __Hive-weer gave__.

    ![De Hive-weer gave die is geselecteerd](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    De pagina Hive-weer gave is vergelijkbaar met de volgende afbeelding:

    ![Afbeelding van het query werkblad voor de Hive-weer gave](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. Plak op het tabblad __query__ de volgende HiveQL-instructies in het werk blad:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Met deze instructies worden de volgende acties uitgevoerd:

   * `DROP TABLE`: Hiermee verwijdert u de tabel en het gegevens bestand, voor het geval de tabel al bestaat.

   * `CREATE EXTERNAL TABLE`: Hiermee maakt u een nieuwe tabel ' External ' in de component.
     Externe tabellen slaan alleen de tabel definitie in Hive op. De gegevens blijven op de oorspronkelijke locatie.

   * `ROW FORMAT`: Laat zien hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.

   * `STORED AS TEXTFILE LOCATION`: Hier wordt weer gegeven waar de gegevens worden opgeslagen en dat deze worden opgeslagen als tekst.

   * `SELECT`: Hiermee wordt het aantal rijen geselecteerd waarin de kolom T4 de waarde [ERROR] bevat.

   > [!IMPORTANT]  
   > De __database__ selectie __standaard__behouden. De voor beelden in dit document gebruiken de standaard database die is opgenomen in HDInsight.

5. Als u de query wilt starten, selecteert u onder het werk blad **uitvoeren** . De knop wordt oranje en de tekst verandert in **stoppen**.

6. Nadat de query is voltooid, worden de resultaten van de bewerking weer gegeven op het tabblad **resultaten** . De volgende tekst is het resultaat van de query:

        loglevel       count
        [ERROR]        3

    U kunt het tabblad **logboek** gebruiken om de logboek gegevens weer te geven die de taak heeft gemaakt.

   > [!TIP]  
   > Down load of sla de resultaten op in het dialoog venster **acties** , onder het tabblad **resultaten** .

### <a name="visual-explain"></a>Visuele uitleg

Als u een visualisatie van het query plan wilt weer geven, selecteert u het tabblad **visuele uitleg** onder het werk blad.

De **visuele uitleg** weergave van de query kan nuttig zijn bij het inzicht in de stroom van complexe query's.

### <a name="tez-ui"></a>TEZ-gebruikers interface

Als u de TEZ-gebruikers interface voor de query wilt weer geven, selecteert u het tabblad **TEZ gebruikers interface** onder het werk blad.

> [!IMPORTANT]  
> TEZ wordt niet gebruikt om alle query's op te lossen. U kunt veel query's oplossen zonder TEZ te gebruiken. 

## <a name="view-job-history"></a>Taakgeschiedenis weergeven

Op het tabblad __taken__ wordt een overzicht van Hive-query's weer gegeven.

![Afbeelding van de taak geschiedenis](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Database tabellen

U kunt het tabblad __tabellen__ gebruiken om te werken met tabellen in een Hive-data base.

![Afbeelding van het tabblad tabellen](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Opgeslagen query's

Op het tabblad **query** kunt u desgewenst query's opslaan. Nadat u een query hebt opgeslagen, kunt u deze opnieuw gebruiken op het tabblad __opgeslagen query's__ .

![Afbeelding van het tabblad opgeslagen query's](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Opgeslagen query's worden opgeslagen in de standaard cluster opslag. U kunt de opgeslagen query's vinden in het pad `/user/<username>/hive/scripts`. Deze worden opgeslagen als bestanden met tekst `.hql` zonder opmaak.
>
> Als u het cluster verwijdert, maar de opslag gebruikt, kunt u een hulp programma zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) of Data Lake Storage Verkenner (vanuit de [Azure-Portal](https://portal.azure.com)) gebruiken om de query's op te halen.

## <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

U kunt het onderdeel uitbreiden via door de gebruiker gedefinieerde functies (UDF). Gebruik een UDF om functionaliteit of logica te implementeren die niet eenvoudig kan worden gemodelleerd in HiveQL.

Declareer en sla een set Udf's op met behulp van het tabblad **UDF** boven aan de Hive-weer gave. Deze Udf's kunnen worden gebruikt in combi natie met de **query-editor**.

![Afbeelding van het tabblad UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Nadat u een UDF hebt toegevoegd aan de Hive-weer gave, wordt de knop **Udf's invoegen** weer gegeven onder aan de **query-editor**. Als u dit item selecteert, wordt een vervolg keuzelijst weer gegeven met de Udf's die in de Hive-weer gave zijn gedefinieerd. Als u een UDF selecteert, worden HiveQL-instructies aan uw query toegevoegd om de UDF in te scha kelen.

Als u bijvoorbeeld een UDF hebt gedefinieerd met de volgende eigenschappen:

* Resource naam: myudfs

* Bronpad:/myudfs.jar

* UDF-naam: myawesomeudf

* Naam UDF-klasse: com. myudfs. meester

Met behulp van de knop **Udfs invoegen** wordt een vermelding weer gegeven met de naam **myudfs**, met een andere vervolg keuzelijst voor elke UDF die voor die bron is gedefinieerd. In dit geval is het **myawesomeudf**. Als u deze vermelding selecteert, voegt u het volgende toe aan het begin van de query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

U kunt de UDF vervolgens gebruiken in uw query. Bijvoorbeeld `SELECT myawesomeudf(name) FROM people;`.

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Udf's met hive in HDInsight:

* [Python gebruiken met Apache Hive en Apache varken in HDInsight](python-udf-hdinsight.md)
* [Een aangepast Apache Hive UDF toevoegen aan HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive-instellingen

U kunt verschillende Hive-instellingen wijzigen, zoals het wijzigen van de engine voor het uitvoeren van een component van TEZ (de standaard instelling) in MapReduce.

## <a id="nextsteps"></a>Volgende stappen

Voor algemene informatie over de Hive op HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Pig gebruiken met Apache Hadoop op HDInsight](hdinsight-use-pig.md)
* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
