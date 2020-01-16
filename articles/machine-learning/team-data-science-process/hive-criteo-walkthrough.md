---
title: Gebruik Azure HDInsight Hadoop-Cluster op de gegevensset van 1 TB - Team Data Science Process
description: Voor een end-to-end scenario die gebruikmaakt van een HDInsight Hadoop-cluster te bouwen en implementeren van een model met behulp van een grote (1 TB) openbaar beschikbare gegevensset met behulp van het Team Data Science Process
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8d47f6f5b983c0f785c76d1b2cede815dda699a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968732"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Het Team Data Science Process in de praktijk - met behulp van een Azure HDInsight Hadoop-Cluster op een gegevensset van 1 TB

In dit scenario ziet u hoe u het gebruik van het Team Data Science Process in een end-to-end-scenario met een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) wilt opslaan, verkennen, voorzien van engineering en voorbeeldgegevens uit een van de openbaar beschikbare omlaag[ Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) gegevenssets. Azure Machine Learning wordt gebruikt voor het bouwen van een binair classificeringsmodel in deze gegevens. U ziet ook hoe u een van deze modellen publiceren als een webservice.

Het is ook mogelijk met gebruik van een IPython notebook de taken die zijn gepresenteerd in deze procedure. Gebruikers die u wilt uitproberen van deze benadering contact opnemen met de [Criteo scenario met behulp van een Hive ODBC-verbinding](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) onderwerp.

## <a name="dataset"></a>Beschrijving van de gegevensset Criteo
De Criteo gegevens is een klik voorspelling gegevensset die is ongeveer 370GB gecomprimeerde gzip TSV-bestanden (niet-gecomprimeerd ~1.3TB), die bestaat uit meer dan 4.3 miljard records. Het is afkomstig uit 24 dagen van klikt u op gegevens die de [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Voor het gemak van gegevenswetenschappers zijn de gegevens beschikbaar voor ons om te experimenteren met uitgepakt.

Elke record in deze gegevensset bevat 40 kolommen:

* de eerste kolom is een labelkolom waarin wordt aangegeven of een gebruiker klikt op een **toevoegen** (waarde 1) of niet op één (de waarde 0)
* volgende 13 kolommen zijn numerieke, en
* laatste 26 zijn categorische kolommen

De kolommen zijn geanonimiseerd en gebruiken van een reeks geïnventariseerde namen: "Col1' (voor de labelkolom) naar ' Col40 ' (voor de laatste categorische kolom).

Hier volgt een fragment van de eerste 20 kolommen in twee metingen (rijen) uit deze gegevensset:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Zijn er ontbrekende waarden in de numerieke en categorische kolommen in deze gegevensset. Een eenvoudige methode voor het verwerken van de ontbrekende waarden wordt beschreven. Aanvullende details van de gegevens worden verkend bij het opslaan van deze in Hive-tabellen.

**Definitie:** *Clickthrough-tarief (CTR):* dit is het percentage muisklikken in de gegevens. In deze gegevensset Criteo is de CTR ongeveer 3.3% of 0.033.

## <a name="mltasks"></a>Voorbeelden van taken voor voorspelling
Twee voorbeeld voorspelling problemen worden in dit scenario behandeld:

1. **Binaire classificatie**: voorspeld of een gebruiker heeft geklikt toevoegen:

   * Klasse 0: Geen klikken
   * Klasse 1: klik op
2. **Regressie**: voorspelt de kans op een ad-klik van functies voor gebruikers.

## <a name="setup"></a>Instellen van een HDInsight Hadoop-cluster voor datatechnologie
**Opmerking:** dit is meestal een **Admin** taak.

Instellen van uw Azure Data Science-omgeving voor het bouwen van voorspellende analyseoplossingen met HDInsight-clusters in drie stappen:

1. [Maak een opslagaccount](../../storage/common/storage-account-create.md): dit storage-account wordt gebruikt voor het opslaan van gegevens in Azure Blob Storage. De gegevens die worden gebruikt in HDInsight-clusters wordt hier opgeslagen.
2. [Azure HDInsight Hadoop-Clusters aanpassen voor Data Science](customize-hadoop-cluster.md): deze stap maakt u een Azure HDInsight Hadoop-cluster met 64-bits Anaconda Python 2.7 geïnstalleerd op alle knooppunten. Er zijn twee belangrijke stappen (in dit onderwerp beschreven) om uit te voeren bij het aanpassen van het HDInsight-cluster.

   * Het opslagaccount dat in stap 1 met uw HDInsight-cluster gemaakt wanneer deze is gemaakt, moet u koppelen. Dit opslagaccount wordt gebruikt voor toegang tot gegevens die kunnen worden verwerkt binnen het cluster.
   * Nadat deze is gemaakt, moet u externe toegang inschakelen met het hoofdknooppunt van het cluster. Vergeet niet de RAS-referenties die u hier opgeeft (die afwijken van die zijn opgegeven voor het cluster op het is gemaakt): u moet de volgende procedures uitvoeren.
3. [Een Azure machine learning studio-werk ruimte (klassieke) maken](../studio/create-workspace.md): deze Azure machine learning-werk ruimte wordt gebruikt voor het bouwen van machine learning modellen na een eerste gegevens exploratie en-bemonstering op het HDInsight-cluster.

## <a name="getdata"></a>Ophalen en gegevens uit een openbare gegevensbron gebruiken
De [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) gegevensset kan worden geopend door te klikken op de koppeling, de gebruiksvoorwaarden accepteren en een naam geven. Hier ziet u een momentopname van hoe dit eruitziet:

![Criteo accepteren](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klik op **doorgaan naar de Download** voor meer informatie over de gegevensset en de beschikbaarheid ervan.

De gegevens zich bevinden in een openbare [Azure blob-opslag](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) locatie: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. De "wasb" verwijst naar Azure Blob Storage-locatie.

1. De gegevens in deze openbare blob-opslag bestaat uit drie submappen van de uitgepakte gegevens.

   1. De submap *onbewerkte/aantal/* bevat de eerste 21 dagen aan gegevens - vanaf dag\_00 dag\_20
   2. De submap *onbewerkte/train/* bestaat uit een enkele dag van de gegevens, dag\_21
   3. De submap *onbewerkte/testen/* bestaat uit twee dagen aan gegevens, dag\_22 en dag\_23
2. Voor diegenen die u wilt beginnen met de onbewerkte gzip-gegevens, deze zijn ook beschikbaar in de hoofdmap *onbewerkte /* als day_NN.gz, waarbij NN tussen 00 en 23 gaat.

Een alternatieve methode om toegang te krijgen tot, verkennen, en het model die deze gegevens waarvoor lokale downloads verderop in dit scenario wordt uitgelegd wanneer we Hive-tabellen maken.

## <a name="login"></a>Meld u aan bij het hoofdknooppunt van het cluster
Om aan te melden met het hoofdknooppunt van het cluster, gebruikt u de [Azure-portal](https://ms.portal.azure.com) te vinden van het cluster. Klik op het pictogram HDInsight elephant aan de linkerkant en dubbelklik vervolgens op de naam van uw cluster. Navigeer naar de **configuratie** tabblad, dubbelklik op het verbindingspictogram aan de onderkant van de pagina en geef uw referenties voor externe toegang wanneer u hierom wordt gevraagd. Hiermee gaat u naar het hoofdknooppunt van het cluster.

Hier volgt een typische eerste aan te melden bij het hoofdknooppunt van het cluster ziet eruit als:

![Meld u aan bij cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Is de ' Hadoop opdrachtregel ', dit onze werkpaard voor de gegevens verkennen is aan de linkerkant. U ziet twee nuttig URL's - 'Hadoop Yarn-Status' en 'Knooppunt Hadoop naam'. De URL van de yarn-status toont de voortgang van de taak en de URL voor de naam van knooppunt code geeft informatie over de configuratie van het cluster.

Nu zijn ingesteld en klaar om te beginnen met de eerste deel van de procedure: gegevens verkennen met behulp van Hive en ophalen van gegevens gereed voor Azure Machine Learning.

## <a name="hive-db-tables"></a> Hive-database en tabellen maken
Voor het maken van Hive-tabellen voor onze gegevensset Criteo, opent u de ***Hadoop-opdrachtregels*** op het bureaublad van het hoofdknooppunt en voer de Hive-map met de opdracht

    cd %hive_home%\bin

> [!NOTE]
> Alle Hive-opdrachten uitvoeren in dit overzicht van de Hive-bin / directory prompt. Dit zorgt voor problemen met pad automatisch. Kunt u de voorwaarden "Hive directory prompt", "Hive bin / directory prompt ', en ' Hadoop-opdrachtregels ' door elkaar.
>
> [!NOTE]
> Voor het uitvoeren van een Hive-query, kan een altijd gebruik van de volgende opdrachten:
>
>

        cd %hive_home%\bin
        hive

Nadat de REPL Hive wordt weergegeven met een ' hive > "Meld u, gewoon knippen en plakt u de query voor het uitvoeren van deze.

De volgende code maakt u een database 'criteo' en genereert vervolgens 4 tabellen:

* een *tabel voor het genereren van aantallen* die is gebouwd op dagen dag\_00 dag\_20,
* een *tabel voor gebruik als de gegevensset van de trein* die is gebouwd op dag\_21, en
* twee *tabellen voor gebruik als de test-gegevenssets* die is gebouwd op dag\_22 en dag\_23 respectievelijk.

De testgegevensset gesplitst in twee verschillende tabellen, omdat een van de dagen een feestdag. Het doel is om te bepalen als het model verschillen tussen een vakantie en niet uit het tarief doorklikken kunt detecteren.

Het script [voorbeeld&#95;hive&#95;maken&#95;criteo&#95;database&#95;en&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) Hier wordt weergegeven om u te helpen:

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

Alle deze tabellen zijn externe, zodat u gewoon met hun Azure Blob Storage (wasb)-locaties verwijzen kan.

**Er zijn twee manieren een Hive-query uit te voeren:**

1. **Met behulp van de opdrachtregel Hive-REPL**: de eerste is voor het verlenen van een opdracht "component" en kopieer en plak een query op de opdrachtregel Hive-REPL. U doet dit door het volgende te doen:

        cd %hive_home%\bin
        hive

     Nu op de opdrachtregel REPL voert knippen en plakken van de query dit.
2. **Query's naar een bestand opslaan en uitvoeren van de opdracht**: de tweede is om op te slaan van de query's naar een bestand .hql ([voorbeeld&#95;hive&#95;maken&#95;criteo&#95;database&#95;en&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) en vervolgens voert de volgende opdracht uit de query uit te voeren:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Controleer of de database en tabel maken
Bevestig vervolgens het maken van de database met de volgende opdracht uit de opslaglocatie Hive / directory prompt:

        hive -e "show databases;"

Dit biedt:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Hiermee bevestigt u het maken van de nieuwe database 'criteo'.

Als u wilt zien welke tabellen zijn gemaakt, geeft u gewoon de opdracht van de Hive-bin / directory prompt:

        hive -e "show tables in criteo;"

Vervolgens ziet u de volgende uitvoer:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Gegevens verkennen in Hive
U bent nu klaar voor enkele eenvoudige gegevensverkenning in Hive. U moet u eerst het aantal voorbeelden van de trein tellen en gegevenstabellen testen.

### <a name="number-of-train-examples"></a>Aantal voorbeelden van de trein
De inhoud van [voorbeeld&#95;hive&#95;aantal&#95;trainen&#95;tabel&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) hier worden weergegeven:

        SELECT COUNT(*) FROM criteo.criteo_train;

Dit resulteert in:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

U kunt ook een kan ook de volgende opdracht uit de opslaglocatie Hive / directory prompt:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Aantal voorbeelden van de test in de twee test-gegevenssets
Nu het aantal van de voorbeelden in de twee test-gegevenssets. De inhoud van [voorbeeld&#95;hive&#95;aantal&#95;criteo&#95;testen&#95;dag&#95;22&#95;tabel&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) zijn hier:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dit resulteert in:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Zoals gewoonlijk u ook het script kan aanroepen vanuit de Hive-bin / map vragen door de opdracht:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Ten slotte het onderzoeken van het aantal voorbeelden van de test in de set met testgegevens op basis van dag\_23.

De opdracht uit om dit te doen is die vergelijkbaar is met de slechts weergegeven (Raadpleeg [voorbeeld&#95;hive&#95;aantal&#95;criteo&#95;testen&#95;dag&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Dit biedt:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Labeldistributie in de gegevensset van de trein
De labeldistributie in de gegevensset van de trein is van belang zijn. Als u wilt zien dit, inhoud van weergeven [voorbeeld&#95;hive&#95;criteo&#95;label&#95;distributie&#95;trainen&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Dit geeft de labeldistributie:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Houd er rekening mee dat het percentage positieve labels ongeveer 3.3% (in overeenstemming met de oorspronkelijke gegevensset is).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distributies van sommige numerieke variabelen in de gegevensset van de trein
Kunt u de Hive-native ' histogram\_numerieke "functie om erachter te komen hoe de distributie van de numerieke variabelen eruitziet. Hier vindt u de inhoud van [voorbeeld&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dit resulteert in het volgende:

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

De LATERALE BEELD - exploderen combinatie van argumenten in Hive fungeert voor het produceren van een SQL-achtige uitvoer in plaats van de gebruikelijke lijst. Houd er rekening mee dat in deze tabel de eerste kolom komt overeen met de bin-center en de tweede de bin-frequentie.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Geschatte percentielen van sommige numerieke variabelen in de gegevensset van de trein
Is ook de berekening van de geschatte percentielen van belang met numerieke variabelen. Hive de systeemeigen "percentiel\_ongeveer" doet dit voor ons. De inhoud van [voorbeeld&#95;hive&#95;criteo&#95;geschatte&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) zijn:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dit resulteert in:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

De verdeling van de percentielen is nauw verwant aan de histogram distributie van een numerieke variabele meestal.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Aantal unieke waarden voor sommige categorische kolommen in de gegevensset van de trein vinden
Blijven de gegevens verkennen, zoeken, voor sommige categorische kolommen, het aantal unieke waarden die ze uitvoeren. U doet dit door de inhoud van weergeven [voorbeeld&#95;hive&#95;criteo&#95;unieke&#95;waarden&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dit resulteert in:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Houd er rekening mee dat Col15 19M unieke waarden bevat. Met behulp van technieken als 'een hot-codering' naïve is om te coderen die high-dimensionale categorische variabelen niet haalbaar is. In het bijzonder, een krachtige, robuuste techniek met de naam [Learning met telt](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) voor efficiënt aanpakken van dit probleem is uitgelegd en gedemonstreerd.

Ten slotte het aantal unieke waarden voor sommige andere categorische kolommen ook bekijken. De inhoud van [voorbeeld&#95;hive&#95;criteo&#95;unieke&#95;waarden&#95;meerdere&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) zijn:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Dit resulteert in:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Nogmaals, houd er rekening mee dat, met uitzondering van Col20, alle andere kolommen veel unieke waarden.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>CO exemplaar telt paren van categorische variabelen in de gegevensset van de trein

De collega exemplaar tellingen van paren van categorische variabelen is ook van belang zijn. Dit kan worden bepaald met de code in [voorbeeld&#95;hive&#95;criteo&#95;gekoppelde&#95;categorische&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Reverse de aantallen te sorteren op hun instantie en in dit geval de top 15 kijken. Dit biedt ons:

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

## <a name="downsample"></a> Onder de voorbeeldgegevenssets voor Azure Machine Learning
Met de gegevenssets verkend en wordt gedemonstreerd hoe u kunt doen van dit type verkenning van variabelen (waaronder combinaties), omlaag voorbeeld de gegevenssets zodat Azure Machine Learning-modellen kunnen worden gebouwd. Let erop dat de focus van het probleem is: uitgaande van een set voorbeeld kenmerken (functie waarden van Col2 - Col40), voorspellen als Col1 0 (geen klik) of 1 (klikken is).

Voorbeeld van de trein en gegevenssets test 1% van de oorspronkelijke grootte van Hive systeemeigen ASELECT() om functie te gebruiken. Het volgende script [voorbeeld&#95;hive&#95;criteo&#95;verkleinen&#95;trainen&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) doet dit voor de gegevensset van de trein:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dit resulteert in:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Het script [voorbeeld&#95;hive&#95;criteo&#95;verkleinen&#95;testen&#95;dag&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) doet het allemaal voor testgegevens, dag\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dit resulteert in:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Ten slotte het script [voorbeeld&#95;hive&#95;criteo&#95;verkleinen&#95;testen&#95;dag&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) doet het allemaal voor testgegevens, dag\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dit resulteert in:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Dit is bent u klaar voor gebruik van onze omlaag sample trainen en testen van gegevenssets voor het ontwikkelen van modellen in Azure Machine Learning.

Er is een belangrijk onderdeel voor uiteindelijke voordat u doorgaat met de Azure Machine Learning, die betrekking heeft op de count-tabel. In de volgende subsectie, wordt de count-tabel in bepaalde mate van detail besproken.

## <a name="count"></a> Een korte beschrijving van de count-tabel
Als u hebt gezien, hebben verschillende categorische variabelen een zeer hoge dimensionaliteit. In dit overzicht, een krachtige techniek met de naam [Learning met telt](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) voor het coderen van deze variabelen in een efficiënte, robuuste wijze wordt weergegeven. Meer informatie over deze techniek wordt de koppeling.

[!NOTE]
>In dit scenario is gericht op het aantal tabellen gebruiken voor het produceren van compact representaties van high-dimensionale categorische functies. Dit is niet de enige manier om te coderen categorische functies. voor meer informatie over andere technieken, willen gebruikers kunnen bekijken [één-hot-encoding](https://en.wikipedia.org/wiki/One-hot) en [hash-functies](https://en.wikipedia.org/wiki/Feature_hashing).
>

Voor het bouwen van het aantal tabellen op de gegevens over het aantal, de gegevens in de map onbewerkte/aantal te gebruiken. In de sectie modellering van gebruikers over het bouwen van deze tabellen aantal voor categorische functies vanaf het begin, worden weergegeven of u kunt ook een aantal vooraf gemaakte tabel gebruiken voor hun explorations. In welke volgt wanneer 'vooraf gemaakte aantal tabellen' worden genoemd, bedoelen we met behulp van het aantal tabellen die zijn opgegeven. Gedetailleerde instructies over hoe u toegang tot deze tabellen zijn opgegeven in de volgende sectie.

## <a name="aml"></a> Een model met Azure Machine Learning bouwen
Ons model voor het proces in Azure Machine Learning bouwen met de volgende stappen:

1. [De gegevens in Hive-tabellen ophalen in Azure Machine Learning](#step1)
2. [Het experiment maken: de gegevens opschonen en maken van een functie met aantal tabellen](#step2)
3. [Bouwen, te trainen en het model beoordelen](#step3)
4. [Het model evalueren](#step4)
5. [Publiceer het model als een webservice](#step5)

U bent nu gereed voor het bouwen van modellen in Azure Machine Learning studio. De gegevens van onze omlaag sample wordt opgeslagen als Hive-tabellen in het cluster. Gebruik de Azure Machine Learning **importgegevens** module om deze gegevens te lezen. De referenties voor toegang tot de storage-account van dit cluster zijn opgegeven in het volgende.

### <a name="step1"></a> Stap 1: Gegevens ophalen uit de Hive-tabellen in Azure Machine Learning met behulp van de module gegevens importeren en selecteert u deze voor een machine learning-experiment
Selecteer eerst een **+ nieuw** -> **EXPERIMENT** -> **leeg Experiment**. Vervolgens de **zoeken** vak op de bovenste links, zoek naar "Gegevens importeren". Sleep en zet de **gegevens importeren** module bij het experiment canvas (de middelste gedeelte van het scherm) aan de module gebruiken voor toegang tot gegevens.

Dit is wat de **importgegevens** ziet eruit als tijdens het ophalen van gegevens uit de Hive-tabel:

![Gegevens importeren gegevens worden opgehaald](./media/hive-criteo-walkthrough/i3zRaoj.png)

Voor de **importgegevens** -module, de waarden van de parameters die zijn opgegeven in de afbeelding zijn slechts voorbeelden van de sortering van de waarden die u moet opgeven. Hier volgt enkele algemene richtlijnen over het invullen van de parameter is ingesteld voor de **importgegevens** module.

1. Kies 'Hive-query' voor **gegevensbron**
2. In de **Hive-databasequery** vak, een eenvoudige SELECT * FROM < uw\_database\_name.your\_tabel\_naam >-voldoende.
3. **Hcatalog-server URI**: als uw cluster is "abc", wordt dit gedaan wordt: https://abc.azurehdinsight.net
4. **Hadoop-gebruikersaccountnaam**: de naam van de gebruiker gekozen op het moment van het bedrijf stellen van het cluster. (Niet de RAS-gebruikersnaam!)
5. **Het wachtwoord voor gebruikersaccount Hadoop**: het wachtwoord voor de naam van de gebruiker gekozen op het moment van het bedrijf stellen van het cluster. (Niet het wachtwoord voor externe toegang.)
6. **Locatie van de uitvoergegevens**: 'Azure' kiezen
7. **Azure storage-accountnaam**: het opslagaccount dat is gekoppeld aan het cluster
8. **Azure storage-accountsleutel**: de sleutel van het opslagaccount dat is gekoppeld aan het cluster.
9. **De containernaam van de Azure**: als de naam van het cluster is "abc", wordt dit gewoon "abc", meestal is.

Zodra de **importgegevens** klaar is met het ophalen van gegevens (u ziet de groene maatstreepjes op de Module), deze gegevens opslaan als een gegevensset (met een naam van uw keuze). Hoe dit eruitziet:

![Gegevens importeren opslaan gegevens](./media/hive-criteo-walkthrough/oxM73Np.png)

Met de rechtermuisknop op de uitvoerpoort van de **importgegevens** module. Hiermee wordt een **opslaan als gegevensset** optie en een **Visualize** optie. De **Visualize** optie als u klikt, wordt 100 rijen van de gegevens, samen met een deelvenster aan de rechterkant die handig is voor sommige samenvattende statistieken. Als gegevens wilt opslaan, selecteert u gewoon **opslaan als gegevensset** en volg de instructies.

Schakel de opgeslagen gegevensset voor gebruik in een machine learning-experiment, zoek de gegevenssets met behulp van de **zoeken** vak weergegeven in de volgende afbeelding. Typ vervolgens de naam hebt u de gegevensset gedeeltelijk te openen en sleep de gegevensset naar het hoofdpaneel gegeven. Slepen en neerzetten naar het hoofdpaneel selecteert voor gebruik in machine learning-modellen.

![Gegevensset naar het hoofd paneel slepen](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Doe dit voor zowel de trein en de test-gegevenssets. Vergeet ook niet om de databasenaam en tabelnamen maken die u hebt opgegeven voor dit doel te gebruiken. De waarden die worden gebruikt in de afbeelding zijn uitsluitend ter illustratie purposes.* *
>
>

### <a name="step2"></a> Stap 2: Een eenvoudig experiment maken in Azure Machine Learning om te voorspellen klikken / geen klikken
Onze Azure Machine Learning Studio (klassiek) experiment ziet er als volgt uit:

![Machine Learning-experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Bekijk nu de belangrijke onderdelen van dit experiment. Sleep onze opgeslagen train en gegevenssets die u aan bij onze experimentcanvas eerst testen.

#### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen
De **Clean Missing Data** module biedt wat de naam al aangeeft: er ontbreken gegevens op een manier die door gebruiker opgegeven worden kunnen opschonen. Zoeken in deze module om dit te zien:

![Schone, ontbrekende gegevens](./media/hive-criteo-walkthrough/0ycXod6.png)

Hier kiest u alle ontbrekende waarden vervangt door een 0. Er zijn andere opties, die kunnen worden gezien door te kijken in de vervolgkeuzelijsten in de module.

#### <a name="feature-engineering-on-the-data"></a>Functie-engineering op de gegevens
Er zijn miljoenen unieke waarden voor sommige categorische functies van grote gegevenssets. Met behulp van methoden, zoals een hot-codering voor de weergave van high-dimensionale categorische functies naïve is volledig onwerkbaar om. Dit scenario ziet u hoe u het aantal functies met behulp van ingebouwde Azure Machine Learning-modules voor het genereren van compact representaties van deze hoge-dimensionale categorische variabelen. Het eindresultaat is een kleinere model, snellere training tijden en maatstaven voor prestaties die erg vergelijkbaar zijn met andere technieken.

##### <a name="building-counting-transforms"></a>Het bouwen van tellen transformeert
Voor het bouwen van de functies count, gebruikt u de **bouwen tellen transformeren** module die beschikbaar is in Azure Machine Learning. De module er zo uit:

![Eigenschappen van module tellen transformeren bouwen](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![module bouwen tellen transformeren](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> In de **kolommen tellen** voert u de kolommen die u uitvoeren telt het aantal wilt op. Deze zijn meestal (zoals vermeld) hoog-dimensionale categorische kolommen. Houd er rekening mee dat de gegevensset Criteo 26 categorische kolommen heeft: van Col15 naar Col40. Hier, tellen op alle mappen en geven hun indexen (vanaf 15 tot 40 gescheiden door komma's, zoals wordt weergegeven).
>

Het gebruik van de module in de modus MapReduce (geschikt voor grote gegevenssets), moet u toegang tot een HDInsight Hadoop-cluster (gebruikt voor het verkennen van de functie opnieuw kan worden gebruikt voor dit doel ook) en de referenties. De vorige afbeelding ziet u wat de ingevulde waarden eruit (Vervang de waarden voor de afbeelding die relevant zijn voor uw eigen use-casescenario).

![Moduleparameters](./media/hive-criteo-walkthrough/05IqySf.png)

De voorgaande afbeelding ziet hoe u de locatie van de invoerblob invoeren. Deze locatie heeft de gegevens die zijn gereserveerd voor het aantal tabellen bouwen op.

Als deze module is voltooid, de transformatie voor later opslaan met de rechtermuisknop op de module en selecteert u de **opslaan als transformatie** optie:

![Optie 'Opslaan als transformatie'](./media/hive-criteo-walkthrough/IcVgvHR.png)

In onze experiment bovenstaande architectuur met de gegevensset "ytransform2" komt overeen met exact een transformatie opgeslagen aantal. Voor de rest van dit experiment wordt ervan uitgegaan dat de lezer gebruikt een **bouwen tellen transformeren** -module op enkele gegevens aan het aantal te genereren en kunt vervolgens met de aantallen voor het genereren van het aantal functies voor de gegevenssets trainen en testen.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Kiezen welke aantal functies voor het opnemen als onderdeel van de trein en test-gegevenssets
Nadat een aantal transformatie gereed is, de gebruiker kan kiezen welke functies wilt opnemen in hun trainen en testen met behulp van gegevenssets de **wijzigen aantal tabel Parameters** module. Deze module wordt voor de volledigheid, hier weergegeven. Maar uit oogpunt van eenvoud daadwerkelijk gebruik deze functie niet in ons experiment.

![De parameters voor het aantal wijzigen](./media/hive-criteo-walkthrough/PfCHkVg.png)

In dit geval, zoals u kunt zien, de log-kans die moeten worden gebruikt en de back-off kolom wordt genegeerd. U kunt ook parameters op, zoals de drempelwaarde voor garbagecollection bin, hoeveel pseudo eerdere voorbeelden om toe te voegen voor vloeiend maken en of alle ruis Laplacian of niet instellen. Al deze functies zijn geavanceerde en het is om te worden opgemerkt dat de standaardwaarden zijn een goed uitgangspunt voor gebruikers die niet bekend bent met dit type functie genereren.

##### <a name="data-transformation-before-generating-the-count-features"></a>Gegevenstransformatie vóór het genereren van de functies count
Nu de focus zich op een belangrijke verwijzen over het transformeren van de trein en testen van gegevens voorafgaand aan het genereren van daadwerkelijk aantal functies. Houd er rekening mee dat er twee zijn **R-Script uitvoeren** modules die worden gebruikt voordat de count-transformatie is toegepast op onze gegevens.

![R-Script modules uitvoeren](./media/hive-criteo-walkthrough/aF59wbc.png)

Dit is de eerste R-script:

![Eerste R-script](./media/hive-criteo-walkthrough/3hkIoMx.png)

Deze R-script wijzigt de naam van de kolommen naam 'Col1' naar 'Col40'. Dit komt doordat de count-transformatie wordt verwacht dat de namen van deze indeling.

Het tweede R-script geeft een balans tussen de verdeling tussen klassen positieve en negatieve (respectievelijk klassen 1 en 0) door de negatieve klasse down-sampling. Het R-script laat zien hoe om dit te doen:

![Tweede R-script](./media/hive-criteo-walkthrough/91wvcwN.png)

In dit eenvoudige R-script, de ' pos\_neg\_verhouding ' wordt gebruikt voor het instellen van de hoeveelheid balans tussen de positieve en negatieve klassen. Dit is belangrijk te doen omdat de klasse imbalance meestal prestatievoordelen heeft voor classificatie problemen waarbij de distributie is vervormd (terughalen die in dit geval hebt u 3.3% positief klasse en 96,7% negatieve klasse).

##### <a name="applying-the-count-transformation-on-our-data"></a>De count-transformatie toepassen op gegevens
Ten slotte kunt u de **transformatie toepassen** module voor het aantal transformaties toepassen op onze trainen en testen van gegevenssets. Deze module gebruikt de transformatie opgeslagen tellen als één invoer en van de trein of test gegevenssets als andere invoer en retourneert gegevens met een aantal functies. Hier wordt weergegeven:

![Module transformatie toepassen](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>In een fragment van wat het aantal functies er als volgt uitzien
Het is om te zien hoe de functies count eruit in ons geval instructieve. Hier volgt een fragment van deze:

![Aantal functies](./media/hive-criteo-walkthrough/FO1nNfw.png)

Dit fragment ziet u dat voor de kolommen geteld op, u de aantallen krijgt en kans naast eventuele relevante backoffs aanmelden.

U bent nu gereed voor het bouwen van een Azure Machine Learning-model met behulp van deze getransformeerde gegevenssets. In de volgende sectie wordt beschreven hoe u dit kunt doen.

### <a name="step3"></a> Stap 3: Bouwen, trainen en het model beoordelen

#### <a name="choice-of-learner"></a>Keuze van de cursist
U moet eerst een cursist kiezen. Gebruik een twee-class boosted-beslisboom als onze learner. Hier volgen de standaardopties voor deze learner:

![Two-Class Boosted beslissingsstructuur parameters](./media/hive-criteo-walkthrough/bH3ST2z.png)

Kies de standaardwaarden van het experiment. Houd er rekening mee dat de standaardwaarden meestal zinvolle zijn en een goede manier om snel basislijnen op de prestaties. U kunt op de prestaties verbeteren door verstrekkende parameters als u wilt beschikt u over een basislijn.

#### <a name="train-the-model"></a>Het model trainen
Voor de training, gewoon aanroepen een **Train Model** module. De twee invoeren toe zijn de cursist Two-Class Boosted beslissingsstructuur en onze gegevensset van de trein. Dit wordt hier weergegeven:

![Module Train-Model](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Het model scoren
Als u een getraind model hebt, bent u klaar om op de testgegevensset te beoordelen en om de prestaties te evalueren. Dit doen met behulp van de **Score Model** module weergegeven in de volgende afbeelding, samen met een **Evaluate Model** module:

![De module Score Model (Scoremodel)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Stap 4: Het model evalueren
Tot slot moet u modelprestaties analyseren. Meestal is een goede indicatie voor twee klasse (binaire) classificatie problemen, het AUC. Als u wilt dit visualiseren, koppelt de **Score Model** module aan een **Evaluate Model** -module voor dit. Te klikken op **Visualize** op de **Evaluate Model** module resulteert in een afbeelding, zoals de volgende uitvoer:

![Module BDT model evalueren](./media/hive-criteo-walkthrough/0Tl0cdg.png)

In binaire (of twee klasse) problemen met de classificatie, een goede indicatie van nauwkeurigheid is het gebied onder Curve (AUC). De volgende sectie ziet onze resultaten met behulp van dit model op onze testgegevensset. Als u deze, met de rechtermuisknop op de uitvoerpoort van de **Evaluate Model** module en vervolgens **Visualize**.

![Module Evaluate Model visualiseren](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Stap 5: Het model als een webservice publiceren
De mogelijkheid voor het publiceren van een Azure Machine Learning-model als webservices met een minimum van fuss is een zeer nuttige functie voor het maken van deze algemeen beschikbaar. Wanneer dat is gebeurd, kan iedereen aanroepen naar de webservice met invoergegevens dat ze nodig hebben voor en het model in de webservice wordt gebruikt om te retourneren die voorspellingen.

Om dit te doen, moet u eerst het getrainde model opslaan als een object van het getrainde Model. Dit wordt gedaan met de rechtermuisknop op de **Train Model** -module en met behulp van de **opslaan als het getrainde Model** optie.

Vervolgens maken van de invoer en uitvoer van poorten voor onze webservice:

* een invoerpoort neemt gegevens in hetzelfde formulier als de gegevens die u nodig hebt voor
* een uitvoerpoort retourneert de Scored Labels en de bijbehorende kansen.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecteer enkele rijen met gegevens voor de invoer-poort
Het is gemakkelijk te gebruiken een **SQL-transformatie toepassen** module te fungeren als de gegevens voor invoerpoort slechts 10 rijen selecteren. Selecteer alleen deze rijen met gegevens voor onze invoerpoort met behulp van de SQL-query die hier worden weergegeven:

![Invoerpoort gegevens](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webservice
U bent nu klaar om uit te voeren van een kleine experiment dat kan worden gebruikt om onze webservice publiceren.

#### <a name="generate-input-data-for-webservice"></a>De invoergegevens voor de webservice genereren
Als een stap zeroth omdat de tabel aantal groot is, worden een paar regels aan testgegevens en uitvoergegevens genereren van het aantal functies. Dit kan fungeren als de indeling van de invoergegevens voor onze webservice. Dit wordt hier weergegeven:

![De invoergegevens BDT maken](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Gebruik voor de indeling van de invoergegevens, de uitvoer van de **aantal Featurizer** module. Zodra dit klaar is met het uitvoeren van experimenten, sla de uitvoer van de **aantal Featurizer** module als een gegevensset. Deze gegevensset wordt gebruikt voor de invoergegevens in de webservice.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Scoring-experiment voor de webservice publiceren
Eerst, ziet u hoe dit eruitziet. De structuur van essentieel belang is een **Score Model** -module die accepteert onze getrainde model-object en een paar regels van ingevoerde gegevens die zijn gegenereerd in de vorige stappen met behulp van de **aantal Featurizer** module. Gebruik 'Select Columns in Dataset' aan het project de Scored labels en de Score kansen.

![Kolommen in gegevensset selecteren](./media/hive-criteo-walkthrough/kRHrIbe.png)

U ziet hoe de **Select Columns in Dataset** module kan worden gebruikt voor 'gefilterd op' gegevens uit een gegevensset. De inhoud worden hier weergegeven:

![Filteren met de Select Columns in Dataset-module](./media/hive-criteo-walkthrough/oVUJC9K.png)

Als u de blauwe invoer en uitvoerpoorten, klikt u op **voorbereiden webservice** aan de onderkant rechts. Uitvoeren van dit experiment ook kan we de webservice publiceren: klik op de **WEBSERVICE publiceren** pictogram aan de onderkant rechts, zoals weergegeven hier:

![Webservice publiceren](./media/hive-criteo-walkthrough/WO0nens.png)

Zodra de webservice is gepubliceerd, krijgen omgeleid naar een pagina die er zo uitziet:

![Dashboard van de webservice](./media/hive-criteo-walkthrough/YKzxAA5.png)

U ziet de twee koppelingen voor webservices aan de linkerkant:

* De **aanvraag/antwoord** Service (of RRS) is bedoeld voor één voorspellingen en wat is gebruikt in deze workshop is.
* De **BATCHUITVOERING** Service (BES) wordt gebruikt voor batch voorspellingen en vereist dat de ingevoerde gegevens gebruikt om voorspellingen bevinden zich in Azure Blob-opslag te maken.

Te klikken op de koppeling **aanvraag/antwoord** neemt ons naar een pagina die biedt ons de code in C#, python en R. vooraf blik Deze code kan gemakkelijk worden gebruikt voor het maken van aanroepen naar de webservice. Houd er rekening mee dat de API-sleutel op deze pagina moet worden gebruikt voor verificatie.

Is het handig moeten worden deze python-code gekopieerd naar een nieuwe cel in de IPython notebook.

Hier volgt een segment van de python-code met de juiste API-sleutel.

![Python-code](./media/hive-criteo-walkthrough/f8N4L4g.png)

Houd er rekening mee dat de standaard API-sleutel is vervangen door de API-sleutel van onze webservices. Te klikken op **uitvoeren** op deze cel in een IPython notebook resulteert in het volgende antwoord:

![IPython-antwoord](./media/hive-criteo-walkthrough/KSxmia2.png)

Test voor de twee voorbeelden over gevraagd (in de JSON-framework van het python-script), kunt u terug antwoorden in de vorm 'Scored Labels, Scored kansen'. In dit geval zijn de standaardwaarden gekozen dat de vooraf blik code (van 0 voor alle numerieke kolommen en de tekenreeks "waarde" voor alle categorische kolommen) biedt.

Hiermee is de procedure voor het verwerken van grote gegevensset met behulp van Azure Machine Learning. U aan de slag met een terabyte aan gegevens, een voorspellingsmodel gebouwd en geïmplementeerd als een webservice in de cloud.

