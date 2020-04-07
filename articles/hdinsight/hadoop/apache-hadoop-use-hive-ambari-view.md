---
title: Apache Ambari Hive View gebruiken met Apache Hadoop in Azure HDInsight
description: Meer informatie over het gebruik van de Hive View vanuit uw webbrowser om Hive-query's in te dienen. De Hive View maakt deel uit van de Ambari Web UI die is voorzien van uw HDInsight-cluster op Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745092"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Apache Ambari Hive-weergave gebruiken met Apache Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het uitvoeren van Hive-query's met apache Ambari Hive View. Met de Hive View u Hive-query's schrijven, optimaliseren en uitvoeren vanuit uw webbrowser.

## <a name="prerequisites"></a>Vereisten

Een Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

1. Selecteer uw cluster in de [Azure-portal.](https://portal.azure.com/)  Zie [Clusters weergeven en weergeven](../hdinsight-administer-use-portal-linux.md#showClusters) voor instructies. Het cluster wordt geopend in een nieuwe portalweergave.

1. Selecteer **ambari-weergaven**in **clusterdashboards**. Wanneer u wordt gevraagd om te `admin`verifiëren, gebruikt u de accountnaam en het wachtwoord van het cluster dat u hebt opgegeven toen u het cluster hebt gemaakt. U ook `https://CLUSTERNAME.azurehdinsight.net/#/main/views` in uw `CLUSTERNAME` browser navigeren waar de naam van uw cluster is.

1. Selecteer __Hive View__in de lijst met weergaven .

    ![Apache Ambari selecteert Apache Hive-weergave](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    De hive-weergavepagina is vergelijkbaar met de volgende afbeelding:

    ![Afbeelding van het querywerkblad voor de hive-weergave](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Plak op het tabblad __Query__ de volgende HiveQL-instructies in het werkblad:

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

    Deze verklaringen doen de volgende acties:

    |Verklaring | Beschrijving |
    |---|---|
    |KEUZELIJST LATEN VALLEN|Hiermee verwijdert u de tabel en het gegevensbestand, voor het geval de tabel al bestaat.|
    |EXTERNE TABEL MAKEN|Hiermee maakt u een nieuwe 'externe' tabel in Hive. Externe tabellen slaan alleen de tabeldefinitie op in Hive. De gegevens worden op de oorspronkelijke locatie achtergelaten.|
    |RIJNOTATIE|Laat zien hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
    |OPGESLAGEN ALS TEXTFILE-LOCATIE|Hiermee wordt weergegeven waar de gegevens worden opgeslagen en dat deze als tekst worden opgeslagen.|
    |SELECT|Hiermee selecteert u een aantal rijen waarin kolom t4 de waarde bevat [FOUT].|

   > [!IMPORTANT]  
   > Laat de __databaseselectie__ __standaard staan__. De voorbeelden in dit document maken gebruik van de standaarddatabase die is opgenomen met HDInsight.

1. Als u de query wilt starten, selecteert u **Uitvoeren** onder het werkblad. De knop wordt oranje en de tekst verandert in **Stoppen**.

1. Nadat de query is voltooid, worden op het tabblad **Resultaten** de resultaten van de bewerking weergegeven. De volgende tekst is het resultaat van de query:

        loglevel       count
        [ERROR]        3

    U het tabblad **LOG** gebruiken om de logboekregistratiegegevens weer te geven die de taak heeft gemaakt.

   > [!TIP]  
   > Resultaten downloaden of opslaan uit het vervolgkeuzedialoogvenster **Acties** onder het tabblad **Resultaten.**

### <a name="visual-explain"></a>Visuele uitleg

Als u een visualisatie van het queryplan wilt weergeven, selecteert u het tabblad **Visuele uitleg** onder het werkblad.

De **weergave Visuele uitleg** van de query kan nuttig zijn bij het begrijpen van de stroom van complexe query's.

### <a name="tez-ui"></a>Tez-gebruikersinterface

Als u de Gebruikersinterface van Tez voor de query wilt weergeven, selecteert u het tabblad **UI tez** onder het werkblad.

> [!IMPORTANT]  
> Tez wordt niet gebruikt om alle query's op te lossen. U veel query's oplossen zonder Tez te gebruiken.

## <a name="view-job-history"></a>Taakgeschiedenis weergeven

Op het tabblad __Vacatures__ wordt een geschiedenis van Hive-query's weergegeven.

![Tabbladgeschiedenis van Apache Hive-weergave](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Databasetabellen

U het tabblad __Tabellen__ gebruiken om te werken met tabellen in een Hive-database.

![Afbeelding van het tabblad Apache Hive-tabellen](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Opgeslagen query's

Op het tabblad **Query** u optioneel query's opslaan. Nadat u een query hebt opgeslagen, u deze opnieuw gebruiken via het tabblad __Opgeslagen query's.__

![Tabblad Opgeslagen query's van Apache Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Opgeslagen query's worden opgeslagen in de standaardclusteropslag. U de opgeslagen query's onder het pad `/user/<username>/hive/scripts`vinden. Deze worden opgeslagen als `.hql` plain-text bestanden.
>
> Als u het cluster verwijdert, maar de opslag behoudt, u een hulpprogramma zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) of Data Lake Storage Explorer (uit de Azure [Portal)](https://portal.azure.com)gebruiken om de query's op te halen.

## <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

U Hive uitbreiden via door de gebruiker gedefinieerde functies (UDF). Gebruik een UDF om functionaliteit of logica te implementeren die niet gemakkelijk is gemodelleerd in HiveQL.

Declareer en sla een set UDF's op met het tabblad **UDF** boven aan de Hive-weergave. Deze UDF's kunnen worden gebruikt met de **queryeditor**.

![Tabblad UDF's van Apache Hive-weergave](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Onder aan de **queryeditor**wordt een **knop Udfs invoegen** weergegeven. In dit item wordt een vervolgkeuzelijst weergegeven met de UDF's die zijn gedefinieerd in de hive-weergave. Als u een UDF selecteert, worden HiveQL-instructies toegevoegd aan uw query om de UDF in te schakelen.

Als u bijvoorbeeld een UDF hebt gedefinieerd met de volgende eigenschappen:

* Resourcenaam: myudfs

* Resourcepad: /myudfs.jar

* UDF naam: myawesomeudf

* UDF klasse naam: com.myudfs.Awesome

Met de **knop Udfs invoegen** wordt een vermelding met de naam **myudfs**weergegeven, met een andere vervolgkeuzelijst voor elke UDF die voor die resource is gedefinieerd. In dit geval is het **myawesomeudf.** Als u dit item selecteert, wordt het volgende toegevoegd aan het begin van de query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

U de UDF vervolgens gebruiken in uw query. Bijvoorbeeld `SELECT myawesomeudf(name) FROM people;`.

Zie de volgende artikelen voor meer informatie over het gebruik van UDF's met Hive op HDInsight:

* [Python gebruiken met Apache Hive en Apache Pig in HDInsight](python-udf-hdinsight.md)
* [Gebruik een Java UDF met Apache Hive in HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive-instellingen

U verschillende Hive-instellingen wijzigen, zoals het wijzigen van de uitvoeringsengine voor Hive van Tez (de standaardinstelling) in MapReduce.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over Hive over HDInsight:

* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [Gebruik Apache Pig met Apache Hadoop op HDInsight](hdinsight-use-pig.md)
* [MapGebruikenReduce met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
