---
title: Azure HDInsight Hadoop-cluster gebruiken op de gegevensset van 1 TB - Team Data Science-proces
description: Het Team Data Science-proces gebruiken voor een end-to-end scenario dat gebruik maakt van een HDInsight Hadoop-cluster om een model te bouwen en te implementeren met behulp van een grote (1 TB) openbaar beschikbare gegevensset
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 218fb96f6960e194f0fc4a4a3a3e603388b961c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760807"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Het Team Data Science-proces in actie - Een Azure HDInsight Hadoop-cluster gebruiken op een 1 TB-gegevensset

Deze walkthrough laat zien hoe u het Team Data Science-proces gebruiken in een end-to-end scenario met een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) om voorbeeldgegevens van een van de openbaar beschikbare [Criteo-gegevenssets](https://labs.criteo.com/downloads/download-terabyte-click-logs/) op te slaan, te verkennen, te verkennen en naar beneden te brengen. Het maakt gebruik van Azure Machine Learning om een binair classificatiemodel op deze gegevens te bouwen. Het laat ook zien hoe je een van deze modellen te publiceren als een webservice.

Het is ook mogelijk om een IPython-notebook te gebruiken om de taken te uitvoeren die in deze walkthrough worden gepresenteerd. Gebruikers die deze aanpak willen proberen, moeten de [Criteo-walkthrough raadplegen met behulp van een Hive ODBC-verbindingsonderwerp.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Beschrijving van criteo-gegevensset
De Criteo-gegevens is een meetvoorspellingsgegevensset die 370 GB gzip gecomprimeerde TSV-bestanden (~ 1,3 TB ongecomprimeerd is), bestaande uit meer dan 4,3 miljard records. Het is afkomstig van 24 dagen klikgegevens beschikbaar gesteld door [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Voor het gemak van data scientists zijn de gegevens waarover we kunnen experimenteren uitgepakt.

Elke record in deze gegevensset bevat 40 kolommen:

* de eerste kolom is een labelkolom die aangeeft of een gebruiker op een **toevoeging** klikt (waarde 1) of niet op een kolom klikt (waarde 0)
* volgende 13 kolommen zijn numeriek, en
* laatste 26 zijn categorische kolommen

De kolommen worden geanonimiseerd en gebruiken een reeks opgesomde namen: "Col1" (voor de labelkolom) tot 'Col40' (voor de laatste categorische kolom).

Hier is een fragment van de eerste 20 kolommen van twee waarnemingen (rijen) uit deze gegevensset:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Er ontbreken waarden in zowel de numerieke als de categorische kolommen in deze gegevensset. Een eenvoudige methode voor het hanteren van de ontbrekende waarden wordt beschreven. Aanvullende details van de gegevens worden onderzocht wanneer ze worden opgeslagen in Hive-tabellen.

**Definitie:** *Klikfrequentie (CTR):* deze statistiek is het percentage klikken in de gegevens. In deze Criteo-gegevensset bedraagt de CTR ongeveer 3,3% of 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voorbeelden van voorspellingstaken
Twee voorbeeldvoorspellingsproblemen worden in deze walkthrough aangepakt:

1. **Binaire classificatie:** voorspelt of een gebruiker op een toevoegen heeft geklikt:

   * Klasse 0: Geen klik
   * Klasse 1: Klik
2. **Regressie:** voorspelt de waarschijnlijkheid van een advertentieklik van gebruikersfuncties.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Een HDInsight Hadoop-cluster instellen voor data science
> [!NOTE]
> Deze stap is meestal een **beheertaak.**

Stel uw Azure Data Science-omgeving in voor het bouwen van voorspellende analyseoplossingen met HDInsight-clusters in drie stappen:

1. [Een opslagaccount maken:](../../storage/common/storage-account-create.md)dit opslagaccount wordt gebruikt om gegevens op te slaan in Azure Blob Storage. De gegevens die worden gebruikt in HDInsight-clusters worden hier opgeslagen.
2. [Azure HDInsight Hadoop Clusters for Data Science](customize-hadoop-cluster.md)aanpassen: met deze stap wordt een Azure HDInsight Hadoop-cluster gemaakt met 64-bits Anaconda Python 2.7 geïnstalleerd op alle knooppunten. Er zijn twee belangrijke stappen (beschreven in dit onderwerp) om te voltooien bij het aanpassen van het HDInsight-cluster.

   * Koppel het opslagaccount dat in stap 1 is gemaakt aan uw HDInsight-cluster wanneer het wordt gemaakt. Dit opslagaccount wordt gebruikt voor toegang tot gegevens die binnen het cluster kunnen worden verwerkt.
   * Externe toegang tot het hoofdknooppunt van het cluster inschakelen nadat het is gemaakt. Denk aan de externe toegangsreferenties die u hier opgeeft (anders dan de referenties die zijn opgegeven bij het maken van het cluster): voer de volgende procedures uit.
3. [Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte](../studio/create-workspace.md)maken: deze Azure Machine Learning-werkruimte wordt gebruikt voor het bouwen van machine learning-modellen na een eerste gegevensverkenning en downsampling op het HDInsight-cluster.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Gegevens uit een openbare bron ophalen en consumeren
De [Criteo-gegevensset](https://labs.criteo.com/downloads/download-terabyte-click-logs/) is toegankelijk door op de link te klikken, de gebruiksvoorwaarden te accepteren en een naam op te geven. Een momentopname wordt hier getoond:

![Accepteer Criteo-voorwaarden](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klik **op Doorgaan om te downloaden** om meer te lezen over de gegevensset en de beschikbaarheid ervan.

De gegevens bevinden zich op wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/een [Azure blob-opslaglocatie:](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) . De 'wasb' verwijst naar azure blob-opslaglocatie.

1. De gegevens in deze Azure blob-opslag bestaan uit drie submappen met uitgepakte gegevens.

   1. De submap *raw/count/* bevat de eerste 21\_dagen aan\_gegevens - van dag 00 tot dag 20
   2. De submap *raw/train/* bestaat uit één\_dag aan gegevens, dag 21
   3. De submap *raw/test/* bestaat uit twee\_dagen aan\_gegevens, dag 22 en dag 23
2. De ruwe gzip-gegevens zijn ook beschikbaar in de hoofdmap *raw/* als day_NN.gz, waar NN van 00 naar 23 gaat.

Een alternatieve benadering voor het openen, verkennen en modelleren van deze gegevens waarvoor geen lokale downloads nodig zijn, wordt later in deze walkthrough uitgelegd wanneer we Hive-tabellen maken.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Inloggen op de clusterheadnode
Als u zich wilt aanmelden bij de headnode van het cluster, gebruikt u de [Azure-portal](https://ms.portal.azure.com) om het cluster te vinden. Klik op het pictogram HDInsight-olifant aan de linkerkant en dubbelklik op de naam van uw cluster. Navigeer naar het tabblad **Configuratie,** dubbelklik op het pictogram VERBINDEN onder aan de pagina en voer desgevraagd uw externe toegangsreferenties in, waarbij u naar de headnode van het cluster gaat.

Hier is wat een typische eerste login op het cluster headnode eruit ziet:

![Inloggen bij cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Aan de linkerkant is de "Hadoop Command Line", dat is ons werkpaard voor de data-exploratie. Let op twee nuttige URL's - "Hadoop Garen Status" en "Hadoop Name Node". De URL van de garenstatus toont de voortgang van de taak en de URL van het naamknooppunt geeft details over de clusterconfiguratie.

Nu bent u ingesteld en klaar om het eerste deel van de walkthrough te beginnen: gegevensverkenning met Hive en het klaarmaken van gegevens voor Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Hive-database en -tabellen maken
Als u Hive-tabellen wilt maken voor onze Criteo-gegevensset, opent u de ***Hadoop-opdrachtregel*** op het bureaublad van het hoofdknooppunt en voert u de Hive-map in door de opdracht in te voeren

    cd %hive_home%\bin

> [!NOTE]
> Voer alle Hive-opdrachten uit in deze walkthrough vanuit de Hive-opslaglocatie/mapprompt. Dit zorgt voor eventuele problemen met het pad automatisch. U de termen 'Hive directory prompt', 'Hive bin/directory prompt' en 'Hadoop Command Line' door elkaar gebruiken.
>
> [!NOTE]
> Om een Hive-query uit te voeren, kan men altijd de volgende opdrachten gebruiken:
>
>        cd %hive_home%\bin
>        hive

Nadat de Hive REPL wordt weergegeven met een teken "hive >", knipt en plakt u de query om deze uit te voeren.

Met de volgende code wordt een database "criteo" gemaakt en worden vervolgens vier tabellen gegenereerd:

* een *tabel voor het genereren van tellingen* gebouwd op dag\_dag 00 tot dag\_20;
* een *tabel voor gebruik als de treingegevensset* die is gebouwd op dag\_21, en
* twee *tabellen voor gebruik als testdatasets* die respectievelijk op dag\_22 en dag\_23 zijn gebouwd.

Splits de testgegevensset op in twee verschillende tabellen omdat een van de dagen een feestdag is. Het doel is om te bepalen of het model verschillen kan detecteren tussen een vakantie en niet-vakantie van de klikfrequentie.

Het [scriptvoorbeeld&#95;hive&#95;&#95;criteo&#95;database&#95;en&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) hier voor het gemak te maken:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Al deze tabellen zijn extern, zodat u wijzen op hun Azure Blob Storage (wasb) locaties.

**Er zijn twee manieren om elke Hive-query uit te voeren:**

* **Met behulp van de Opdrachtregel Hive REPL:** De eerste is het uitgeven van een opdracht 'hive' en een query kopiëren en plakken op de opdrachtregel Hive REPL:

        cd %hive_home%\bin
        hive

     Nu voert bij de REPL-opdrachtregel het knippen en plakken van de query uit.
* **Query's opslaan in een bestand en de opdracht uitvoeren:** De tweede is het opslaan van de query's in een bestand '.hql'[(voorbeeld&#95;hive&#95;&#95;criteo&#95;-database&#95;en&#95;tables.hql)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)maken en vervolgens de volgende opdracht uitgeven om de query uit te voeren:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Database- en tabelcreatie bevestigen
Bevestig vervolgens de aanmaak van de database met de volgende opdracht uit de opdracht Hive-opslaglocatie/mapprompt:

        hive -e "show databases;"

Dit geeft:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Dit bevestigt de oprichting van de nieuwe database, "criteo".

Als u wilt zien welke tabellen zijn gemaakt, geeft u de opdracht hier uit de opdracht Hive-opslaglocatie/mapprompt:

        hive -e "show tables in criteo;"

U ziet dan de volgende uitvoer:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Data-exploratie in Hive
Nu bent u klaar om een aantal fundamentele gegevens exploratie te doen in Hive. U begint met het tellen van het aantal voorbeelden in de trein en test gegevenstabellen.

### <a name="number-of-train-examples"></a>Aantal treinvoorbeelden
De inhoud van [de&#95;bijenkorf&#95;tellen&#95;trein&#95;tabel&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) worden hier weergegeven:

        SELECT COUNT(*) FROM criteo.criteo_train;

Dit levert op:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

U ook de volgende opdracht uit de Hive-opslaglocatie/mapprompt geven:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Aantal testvoorbeelden in de twee testgegevenssets
Tel nu het aantal voorbeelden in de twee testgegevenssets. De inhoud van [de&#95;&#95;bijenkorf&#95;tellen&#95;criteo&#95;test&#95;dag&#95;22&#95;tabel&#95;voorbeelden.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) zijn hier:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dit levert op:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Zoals gebruikelijk u het script ook aanroepen vanuit de hivebin/ mapprompt door de opdracht uit te geven:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Tot slot onderzoekt u het aantal testvoorbeelden in\_de testgegevensset op basis van dag 23.

De opdracht om dit te doen is vergelijkbaar met de getoonde (zie&#95;[bijenkorf&#95;tellen&#95;criteo&#95;test&#95;dag&#95;23&#95;examples.hql):](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Dit geeft:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Labeldistributie in de treingegevensset
De labeldistributie in de treindataset is van belang. Om dit te zien, toon de inhoud van [de steekproef&#95;bijenkorf&#95;criteo&#95;label&#95;distributie&#95;trein&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Dit levert de labeldistributie op:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Het percentage positieve labels is ongeveer 3,3% (in overeenstemming met de oorspronkelijke gegevensset).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogramverdelingen van enkele numerieke variabelen in de treingegevensset
U hive's native histogram\_numerieke functie gebruiken om erachter te komen hoe de verdeling van de numerieke variabelen eruit ziet. Hier zijn de inhoud van [de steekproef&#95;bijenkorf&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dit levert het volgende op:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

De LATERAL VIEW - explodeercombinatie in Hive dient om een SQL-achtige uitvoer te produceren in plaats van de gebruikelijke lijst. In deze tabel komt de eerste kolom overeen met het opslagpuntcentrum en de tweede met de opslaglocatiefrequentie.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Geschatte percentiels van enkele numerieke variabelen in de treingegevensset
Ook van belang met numerieke variabelen is de berekening van benaderende percentiels. Hive's native "percentiel\_ca. " doet dit voor ons. De inhoud van [monster&#95;bijenkorf&#95;criteo&#95;ongeveer&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) zijn:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dit levert op:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

De verdeling van percentiels is nauw verwant aan de histogramverdeling van een numerieke variabele meestal.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Aantal unieke waarden zoeken voor sommige categorische kolommen in de gegevensset van de trein
Voortzetting van de gegevens exploratie, vinden, voor sommige categorische kolommen, het aantal unieke waarden die ze nemen. Toon hiervoor de inhoud van [&#95;de&#95;bijenkorf&#95;criteo&#95;unieke&#95;waarden&#95;categorisch.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dit levert op:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 heeft unieke waarden van 19m! Het gebruik van naïeve technieken zoals "one-hot encoding" om dergelijke hoogdimensionale categorische variabelen te coderen is niet haalbaar. Met name een krachtige, robuuste techniek genaamd [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) om dit probleem efficiënt aan te pakken wordt uitgelegd en gedemonstreerd.

Tot slot kijken naar het aantal unieke waarden voor een aantal andere categorische kolommen ook. De inhoud van [monster&#95;bijenkorf&#95;criteo&#95;unieke&#95;waarden&#95;meerdere&#95;categorisch.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) zijn:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Dit levert op:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Nogmaals, merk op dat, met uitzondering van Col20, alle andere kolommen hebben vele unieke waarden.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Aantal paren categorische variabelen in de treingegevensset

De tellingsverdelingen van paren van categorische variabelen zijn ook van belang. Dit kan worden bepaald met behulp van de code in [de steekproef&#95;bijenkorf&#95;criteo&#95;gekoppeld&#95;categorische&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Omgekeerde volgorde van de tellingen door hun optreden en kijk naar de top 15 in dit geval. Dit geeft ons:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Voorbeeld van de gegevenssets voor Azure Machine Learning omlaag
Nadat u de gegevenssets hebt verkend en hebt aangetoond hoe u dit type verkenning uitvoeren voor variabelen (inclusief combinaties), u de gegevenssets naar beneden bekijken zodat modellen in Azure Machine Learning kunnen worden gebouwd. Bedenk dat de focus van het probleem is: gezien een set van voorbeeldkenmerken (functiewaarden van Col2 - Col40), voorspellen of Col1 een 0 is (geen klik) of een 1 (klik).

Gebruik de native RAND() functie van Hive om de trein te samplen en de gegevenssets te testen tot 1% van de oorspronkelijke grootte. Het volgende script, [voorbeeld&#95;hive&#95;criteo&#95;downsample&#95;trein&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) doet dit voor de treindataset:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dit levert op:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Het script [monster&#95;hive&#95;criteo&#95;downsample&#95;test&#95;dag&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) doet het voor testgegevens, dag\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dit levert op:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Ten slotte, het script [monster&#95;hive&#95;criteo&#95;downsample&#95;test&#95;dag&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) doet het voor testgegevens, dag\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dit levert op:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Hiermee bent u klaar om onze naar beneden gesamplede trein en testgegevenssets te gebruiken voor het bouwen van modellen in Azure Machine Learning.

Er is een laatste belangrijk onderdeel voordat u overgaat tot Azure Machine Learning, dat betrekking heeft op de teltabel. In de volgende onderafdeling wordt de teltabel in detail besproken.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>Een korte discussie over de teltafel
Zoals je zag, hebben verschillende categorische variabelen een hoge dimensionaliteit. In de walkthrough wordt een krachtige techniek genaamd [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) gepresenteerd om deze variabelen op een efficiënte, robuuste manier te coderen. Meer informatie over deze techniek is te vinden in de link.

>[!NOTE]
>In deze walkthrough ligt de focus op het gebruik van teltabellen om compacte representaties van hoogdimensionale categorische functies te produceren. Dit is niet de enige manier om categorische functies te coderen; voor meer informatie over andere technieken, kunnen geïnteresseerde gebruikers kijken op [one-hot-encoding](https://en.wikipedia.org/wiki/One-hot) en [functie hashing](https://en.wikipedia.org/wiki/Feature_hashing).
>

Als u teltabellen wilt maken op de telgegevens, gebruikt u de gegevens in de map raw/count. In de sectie modellering krijgen gebruikers te zien hoe ze deze teltabellen kunnen maken voor categorische functies vanaf nul, of als alternatief om een vooraf gebouwde teltabel te gebruiken voor hun verkenningen. In wat volgt, wanneer "vooraf gebouwde teltabellen" worden genoemd, bedoelen we het gebruik van de teltabellen die zijn verstrekt. Gedetailleerde instructies over hoe toegang te krijgen tot deze tabellen zijn opgenomen in de volgende sectie.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Een model maken met Azure Machine Learning
Ons modelbouwproces in Azure Machine Learning volgt de volgende stappen:

1. [De gegevens uit Hive-tabellen ophalen in Azure Machine Learning](#step1)
2. [Het experiment maken: de gegevens schoonmaken en er een functie van maken met teltabellen](#step2)
3. [Bouw, train en scoor het model](#step3)
4. [Het model evalueren](#step4)
5. [Het model publiceren als een webservice](#step5)

Nu bent u klaar om modellen te bouwen in Azure Machine Learning studio. Onze down sampled data worden opgeslagen als Hive tabellen in het cluster. Gebruik de module **Importgegevens van** Azure Machine Learning om deze gegevens te lezen. De referenties voor toegang tot het opslagaccount van dit cluster worden verstrekt in wat volgt.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>Stap 1: Gegevens uit Hive-tabellen ophalen in Azure Machine Learning met de module Gegevens importeren en selecteren voor een machine learning-experiment
Begin met het selecteren van een **+NIEUW** -> **EXPERIMENT** -> **Leeg Experiment**. Zoek vervolgens in het vak **Zoeken** linksboven naar 'Gegevens importeren'. Sleep en plaats de module **Gegevens importeren** op het experimentcanvas (het middelste gedeelte van het scherm) om de module te gebruiken voor gegevenstoegang.

Zo ziet de **importgegevens** eruit terwijl gegevens uit de tabel Hive worden opgehaald:

![Importgegevens krijgen gegevens](./media/hive-criteo-walkthrough/i3zRaoj.png)

Voor de module **Gegevens importeren** zijn de waarden van de parameters die in de afbeelding worden weergegeven slechts voorbeelden van het soort waarden dat u moet opgeven. Hier vindt u een aantal algemene richtlijnen voor het invullen van de parameterset voor de module **Gegevens importeren.**

1. Kies 'Hive-query' voor **gegevensbron**
2. In het queryvak **hive-database** is een\_\_eenvoudige SELECT\_\_* FROM <uw databasenaam.uw tabelnaam> - voldoende.
3. **Hcatalog server URI:** Als uw cluster is "abc", dan is dit gewoon:https://abc.azurehdinsight.net
4. **Hadoop gebruikersnaam naam:** De gebruikersnaam gekozen op het moment van inbedrijfstelling van het cluster. (NIET de gebruikersnaam van rastoegang!)
5. **Hadoop gebruikersaccount wachtwoord:** Het wachtwoord voor de gebruikersnaam gekozen op het moment van de inbedrijfstelling van het cluster. (NIET het wachtwoord voor externe toegang!)
6. **Locatie van uitvoergegevens**: Kies "Azure"
7. **Azure Storage-accountnaam:** het opslagaccount dat is gekoppeld aan het cluster
8. **Azure Storage-accountsleutel:** de sleutel van het opslagaccount dat aan het cluster is gekoppeld.
9. **Azure container naam:** Als de clusternaam is "abc", dan is dit gewoon "abc", meestal.

Zodra de **importgegevens** klaar zijn met het verkrijgen van gegevens (u ziet de groene teek op de module), slaat u deze gegevens op als een gegevensset (met een naam naar keuze). Hoe dit eruit ziet:

![Gegevens opslaan van gegevens importeren](./media/hive-criteo-walkthrough/oxM73Np.png)

Klik met de rechtermuisknop op de uitvoerpoort van de module **Gegevens importeren.** Dit onthult een **optie Opslaan als gegevensset** en een optie **Visualiseren.** Met de optie **Visualiseren** wordt, als er op wordt geklikt, 100 rijen van de gegevens weergegeven, samen met een rechterpaneel dat handig is voor sommige overzichtsstatistieken. Als u gegevens wilt opslaan, selecteert u **Opslaan als gegevensset** en volgt u instructies.

Als u de opgeslagen gegevensset wilt selecteren voor gebruik in een machine learning-experiment, zoekt u de gegevenssets met het vak **Zoeken** in de volgende afbeelding. Typ vervolgens gewoon de naam die u de gegevensset hebt gegeven om er gedeeltelijk toegang toe te krijgen en sleep de gegevensset naar het hoofdpaneel. Als u het op het hoofdpaneel laat vallen, wordt het gebruikt voor machine learning-modellering.

![Gegevensset naar het hoofddeelvenster slepen](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Doe dit voor zowel de trein als de testdatasets. Vergeet ook niet om de databasenaam en tabelnamen te gebruiken die u hiervoor hebt opgegeven. De waarden die in de figuur worden gebruikt, zijn uitsluitend bedoeld ter illustratie.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>Stap 2: Een experiment maken in Azure Machine Learning om klikken / geen klikken te voorspellen
Ons Azure Machine Learning Studio (klassieke) experiment ziet er als volgt uit:

![Machine Learning-experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Bestudeer nu de belangrijkste onderdelen van dit experiment. Sleep eerst onze opgeslagen trein en test datasets naar ons experimentcanvas.

#### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen
De clean **missing data** module doet wat de naam suggereert: het reinigt ontbrekende gegevens op een manier die kan worden opgegeven door de gebruiker. Kijk in deze module om dit te zien:

![Ontbrekende gegevens opschonen](./media/hive-criteo-walkthrough/0ycXod6.png)

Kies hier om alle ontbrekende waarden te vervangen door een 0. Er zijn ook andere opties, die kunnen worden gezien door te kijken naar de dropdowns in de module.

#### <a name="feature-engineering-on-the-data"></a>Functie-engineering op de gegevens
Er kunnen miljoenen unieke waarden zijn voor sommige categorische kenmerken van grote gegevenssets. Het gebruik van naïeve methoden zoals one-hot encoding voor het vertegenwoordigen van dergelijke hoogdimensionale categorische kenmerken is volledig onhaalbaar. Deze walkthrough laat zien hoe je telfuncties gebruiken met ingebouwde Azure Machine Learning-modules om compacte weergaven van deze hoogdimensionale categorische variabelen te genereren. Het eindresultaat is een kleinere modelgrootte, snellere trainingstijden en prestatiestatistieken die vergelijkbaar zijn met het gebruik van andere technieken.

##### <a name="building-counting-transforms"></a>Het tellen van het gebouw transformaties
Als u telfuncties wilt bouwen, gebruikt u de module **Het tellen van** bouwen die beschikbaar is in Azure Machine Learning. De module ziet er als volgt uit:

![Eigenschappen van bouwteltransformatiemodule](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![Build Counting Transform module](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Voer in het vak **Kolommen tellen** de kolommen in waarop u wilt uitvoeren. Meestal zijn dit (zoals vermeld) hoogdimensionale categorische kolommen. Vergeet niet dat de Criteo-gegevensset 26 categorische kolommen heeft: van Col15 tot Col40. Hier, rekenen op alle van hen en geven hun indexen (van 15 tot 40 gescheiden door komma's zoals getoond).
>

Als u de module wilt gebruiken in de mapreduce-modus (geschikt voor grote gegevenssets), hebt u toegang nodig tot een HDInsight Hadoop-cluster (het cluster dat wordt gebruikt voor functieverkenning kan ook voor dit doel worden hergebruikt) en de referenties ervan. De vorige cijfers illustreren hoe de ingevulde waarden eruit zien (vervang de voor illustratie verstrekte waarden met de waarden die relevant zijn voor uw eigen use-case).

![Moduleparameters](./media/hive-criteo-walkthrough/05IqySf.png)

De voorgaande afbeelding geeft aan hoe u de locatie van de invoerblob invoert. Op deze locatie zijn de gegevens gereserveerd voor het tellen van de tabellen.

Wanneer deze module klaar is met werken, slaat u de transformatie voor later op door met de rechtermuisknop op de module te klikken en de optie **Opslaan als transformeren te** selecteren:

![Optie Opslaan als transformeren](./media/hive-criteo-walkthrough/IcVgvHR.png)

In onze bovenstaande experimentarchitectuur komt de gegevensset "ytransform2" precies overeen met een opgeslagen teltransformatie. Voor de rest van dit experiment wordt ervan uitgegaan dat de lezer een **build counting transform-module** op sommige gegevens heeft gebruikt om tellingen te genereren en deze tellingen vervolgens kan gebruiken om telfuncties op de trein te genereren en gegevenssets te testen.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Kiezen welke functies als onderdeel van de trein- en testgegevenssets moeten worden opgenomen
Zodra een count-transformatie gereed is, kan de gebruiker kiezen welke functies hij in de trein wilt opnemen en gegevenssets testen met de module **Teltabelparameters wijzigen.** Voor compleetheid wordt deze module hier getoond. Maar in het belang van de eenvoud niet daadwerkelijk gebruiken in ons experiment.

![Parameters van de teltabel wijzigen](./media/hive-criteo-walkthrough/PfCHkVg.png)

In dit geval, zoals kan worden gezien, de log-odds moeten worden gebruikt en de back-off kolom wordt genegeerd. U ook parameters instellen, zoals de drempel waardevan de prullenbak, hoeveel pseudo-eerdere voorbeelden u moet toevoegen voor het gladstrijken en of u laplacian-ruis wilt gebruiken of niet. Al deze zijn geavanceerde functies en het is op te merken dat de standaardwaarden zijn een goed uitgangspunt voor gebruikers die nieuw zijn voor dit soort functie generatie.

##### <a name="data-transformation-before-generating-the-count-features"></a>Gegevenstransformatie voordat de telfuncties worden genereren
Nu ligt de focus op een belangrijk punt over het transformeren van onze trein- en testgegevens voordat we daadwerkelijk telfuncties genereren. Er zijn twee **Execute R Script-modules** gebruikt voordat de teltransformatie wordt toegepast op onze gegevens.

![R Script-modules uitvoeren](./media/hive-criteo-walkthrough/aF59wbc.png)

Hier is het eerste R-script:

![Eerste R-script](./media/hive-criteo-walkthrough/3hkIoMx.png)

Dit R-script hernoemt onze kolommen in de namen "Col1" naar "Col40". Dit komt omdat de telling smeuaal transformatie verwacht namen van deze indeling.

Het tweede R-script balanceert de verdeling tussen positieve en negatieve klassen (respectievelijk klassen 1 en 0) door de negatieve klasse te downsamplen. Het R-script hier laat zien hoe u dit doen:

![Tweede R-script](./media/hive-criteo-walkthrough/91wvcwN.png)

In dit eenvoudige R-script\_\_wordt de "pos neg ratio" gebruikt om de balans tussen de positieve en de negatieve klassen in te stellen. Dit is belangrijk om te doen, omdat het verbeteren van klasse onbalans heeft meestal prestatievoordelen voor classificatieproblemen waar de klasse verdeling is scheef (herinneren dat in dit geval, je hebt 3,3% positieve klasse en 96,7% negatieve klasse).

##### <a name="applying-the-count-transformation-on-our-data"></a>De tellingstransformatie toepassen op onze gegevens
Ten slotte u de module **Transformatie toepassen** gebruiken om de teltransformaties toe te passen op onze trein- en testgegevenssets. Deze module neemt de opgeslagen telling transformatie als een input en de trein of test datasets als de andere input, en retourneert gegevens met tellen functies. Het is hier te zien:

![Transformatiemodule toepassen](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Een fragment van hoe de telfuncties eruit zien
Het is leerzaam om te zien hoe de telfuncties er in ons geval uitzien. Hier is een fragment van dit:

![Aantalfuncties](./media/hive-criteo-walkthrough/FO1nNfw.png)

Dit fragment laat zien dat voor de kolommen waarop wordt gerekend, u de tellingen en logodds krijgt naast alle relevante back-offs.

U bent nu klaar om een Azure Machine Learning-model te bouwen met behulp van deze getransformeerde gegevenssets. In het volgende gedeelte laat zien hoe dit kan worden gedaan.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Stap 3: Bouw, train en scoor het model

#### <a name="choice-of-learner"></a>Keuze van de leerling
Eerst moet je een leerling kiezen. Gebruik een twee-klasse versterkte beslissingsstructuur als onze leerling. Dit zijn de standaardopties voor deze leerling:

![Tweeklassen versterkte beslissingsstructuurparameters](./media/hive-criteo-walkthrough/bH3ST2z.png)

Kies voor het experiment de standaardwaarden. De standaardinstellingen zijn zinvol en een goede manier om snelle basislijnen op prestaties te krijgen. U de prestaties verbeteren door parameters te vegen als u ervoor kiest om een basislijn te hebben.

#### <a name="train-the-model"></a>Het model trainen
Voor training u eenvoudig een **treinmodelmodule** aanroepen. De twee ingangen zijn de Two-Class Boosted Decision Tree-leerling en onze treingegevensset. Dit is hier te zien:

![Treinmodelmodule](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Het model scoren
Zodra u een getraind model hebt, bent u klaar om te scoren op de testgegevensset en de prestaties ervan te evalueren. Doe dit door de module **Scoremodel** in de volgende afbeelding te gebruiken, samen met een module **Model evalueren:**

![De module Score Model (Scoremodel)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>Stap 4: Evalueer het model
Ten slotte moet u de prestaties van het model analyseren. Meestal, voor twee klasse (binaire) classificatie problemen, een goede maatregel is de AUC. Als u deze curve wilt visualiseren, sluit u de module **Scoremodel** aan op een module **Model evalueren.** Als u op **De** module **Model evalueren klikt,** wordt een afbeelding als de volgende afbeelding opgevolgd:

![Module BDT-model evalueren](./media/hive-criteo-walkthrough/0Tl0cdg.png)

In binaire (of twee klasse) classificatie problemen, een goede maatregel van voorspelling nauwkeurigheid is het gebied onder curve (AUC). De volgende sectie toont onze resultaten met behulp van dit model op onze testdataset. Klik met de rechtermuisknop op de uitvoerpoort van de module **Model evalueren** en **vervolgens visualiseren**.

![Module Model evalueren visualiseren](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>Stap 5: Het model publiceren als webservice
De mogelijkheid om een Azure Machine Learning-model te publiceren als webservices met een minimum aan gedoe is een waardevolle functie om het op grote schaal beschikbaar te maken. Zodra dat is gedaan, kan iedereen bellen naar de webservice met invoergegevens waarvoor ze voorspellingen nodig hebben, en de webservice gebruikt het model om die voorspellingen terug te geven.

Sla eerst ons getrainde model op als een getraind modelobject door met de rechtermuisknop op de module **Treinmodel** te klikken en de optie **Opslaan als getraind model te** gebruiken.

Maak vervolgens invoer- en uitvoerpoorten voor onze webservice:

* een invoerpoort neemt gegevens aan in dezelfde vorm als de gegevens waarvoor u voorspellingen nodig hebt
* een uitvoerpoort retourneert de gescoorde labels en de bijbehorende waarschijnlijkheden.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Een paar rijen gegevens voor de invoerpoort selecteren
Het is handig om een **Module SQL-transformatie toepassen** te gebruiken om slechts 10 rijen te selecteren om als invoerpoortgegevens te dienen. Selecteer alleen deze rijen met gegevens voor onze invoerpoort met behulp van de SQL-query die hier wordt weergegeven:

![Invoerpoortgegevens](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webservice
Nu bent u klaar om een klein experiment uit te voeren dat kan worden gebruikt om onze webservice te publiceren.

#### <a name="generate-input-data-for-webservice"></a>Invoergegevens genereren voor webservice
Als een zeroth stap, omdat de telling tabel is groot, neem een paar regels van testgegevens en genereren output gegevens van het met tellen functies. Deze uitvoer kan dienen als de invoergegevensindeling voor onze webservice, zoals hier wordt weergegeven:

![BDT-invoergegevens maken](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Gebruik voor de indeling invoergegevens de uitvoer van de **module Featurizer van tellen.** Zodra dit experiment is uitgevoerd, slaat u de uitvoer van de **Module Featurizer** van tellen op als gegevensset. Deze gegevensset wordt gebruikt voor de invoergegevens in de webservice.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Scoreexperiment voor het publiceren van webservice
Ten eerste is de essentiële structuur een **scoremodelmodule** die ons getrainde modelobject accepteert en een paar regels invoergegevens die in de vorige stappen zijn gegenereerd met behulp van de **Module Graaf Featurizer.** Gebruik 'Kolommen selecteren in gegevensset' om de labels Scored en de kansop score uit te projecteren.

![Kolommen in gegevensset selecteren](./media/hive-criteo-walkthrough/kRHrIbe.png)

Merk op hoe de module **Kolommen selecteren in gegevensset** kan worden gebruikt voor het 'filteren' van gegevens uit een gegevensset. De inhoud wordt hier getoond:

![Filteren met de module Kolommen selecteren in gegevensset](./media/hive-criteo-walkthrough/oVUJC9K.png)

Om de blauwe invoer- en uitvoerpoorten te krijgen, klikt u rechtsonder op **webservice voorbereiden.** Door dit experiment uit te voeren, kunnen we ook de webservice publiceren: klik op het pictogram **WEBSERVICE PUBLICEREN** rechtsonder, hier weergegeven:

![Webservice publiceren](./media/hive-criteo-walkthrough/WO0nens.png)

Zodra de webservice is gepubliceerd, wordt u doorgestuurd naar een pagina die er zo uitziet:

![Dashboard van de webservice](./media/hive-criteo-walkthrough/YKzxAA5.png)

Let op de twee links voor webservices aan de linkerkant:

* De **REQUEST/RESPONSE** Service (of RRS) is bedoeld voor enkele voorspellingen en is wat er in deze workshop is gebruikt.
* De **BATCH EXECUTION** Service (BES) wordt gebruikt voor batchvoorspellingen en vereist dat de invoergegevens die worden gebruikt om voorspellingen te doen, zich in Azure Blob Storage bevinden.

Door op de link **REQUEST/RESPONSE** te klikken, gaan we naar een pagina die ons vooraf ingeblikte code geeft in C#, python en R. Deze code kan gemakkelijk worden gebruikt voor het maken van gesprekken naar de webservice. De API-sleutel op deze pagina moet worden gebruikt voor verificatie.

Het is handig om deze python-code te kopiëren naar een nieuwe cel in het IPython-notitieblok.

Hier is een segment van python-code met de juiste API-sleutel.

![Python-code](./media/hive-criteo-walkthrough/f8N4L4g.png)

De standaard API-sleutel is vervangen door de API-sleutel van onze webservice. Als u op **Deze** cel in een IPython-notitieblok klikt, wordt het volgende antwoord gegeven:

![IPython-antwoord](./media/hive-criteo-walkthrough/KSxmia2.png)

Voor de twee testvoorbeelden die in het Python-script JSON-framework worden gevraagd, krijgt u antwoorden terug in de vorm "Scored Labels, Scored Probabilities". In dit geval zijn de standaardwaarden gekozen die de vooraf ingeblikte code biedt (0's voor alle numerieke kolommen en de tekenreeks "waarde" voor alle categorische kolommen).

Tot slot laat onze walkthrough zien hoe u met Azure Machine Learning omgaat met grootschalige gegevenssets. U begon met een terabyte aan gegevens, bouwde een voorspellingsmodel en zette het uit als een webservice in de cloud.

