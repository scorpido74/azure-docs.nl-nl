---
title: Gegevens verkennen in een Hadoop-cluster - Team Data Science Process
description: Met behulp van het Team Data Science Process voor een end-to-end scenario, met behulp van een HDInsight Hadoop cluster te bouwen en implementeren van een model.
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
ms.openlocfilehash: 005d4fe1b6ec59e7f05be3dd2ab3e72d0e7aa8e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283418"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Het Team Data Science-proces in actie: Azure HDInsight Hadoop-clusters gebruiken
In deze walkthrough gebruiken we het [Team Data Science Process (TDSP)](overview.md) in een end-to-end scenario. We gebruiken een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) om gegevens uit de openbaar beschikbare [NYC Taxi Trips-gegevensset](https://www.andresmh.com/nyctaxitrips/) op te slaan, te verkennen en te beheren en de gegevens te downsamplen. Om binaire en multiclass classificatie- en regressievoorspellende taken te verwerken, bouwen we modellen van de gegevens met Azure Machine Learning. 

Zie Team Data Science Process - Azure HDInsight Hadoop Clusters gebruiken voor een walkthrough die laat zien hoe u met een grotere gegevensset omgaat, [azure HDInsight Hadoop-clusters gebruiken voor een gegevensset van 1 TB.](hive-criteo-walkthrough.md)

U ook een IPython-notitieblok gebruiken om de taken uit te voeren die worden gepresenteerd in de walkthrough die de 1 TB-gegevensset gebruikt. Zie [Criteo-walkthrough met behulp van een Hive ODBC-verbinding](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)voor meer informatie.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Trips dataset beschrijving
De NYC Taxi Trip gegevens is ongeveer 20 GB van gecomprimeerde komma-gescheiden waarden (CSV) bestanden (~ 48 GB ongecomprimeerd). Het heeft meer dan 173 miljoen individuele reizen, en omvat de tarieven betaald voor elke reis. Elke reis record omvat de pick-up en dropoff locatie en tijd, geanonimiseerde hack (driver's) licentienummer, en medaillon nummer (de taxi's unieke ID). De gegevens hebben betrekking op alle reizen in het jaar 2013 en worden voor elke maand in de volgende twee gegevenssets opgenomen:

- De trip_data CSV-bestanden bevatten reisgegevens: het aantal passagiers, ophaal- en afleverpunten, reisduur en reislengte. Hier zijn een paar voorbeeldrecords:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- De trip_fare CSV-bestanden bevatten details over het betaalde tarief voor elke reis: betalingstype, tariefbedrag, toeslag en belastingen, tips en tolgelden en het totale betaalde bedrag. Hier zijn een paar voorbeeldrecords:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De unieke sleutel\_om deel\_te nemen aan reisgegevens en\_reistarief bestaat\_uit de velden: medaillon, hacklicentie en ophaaldatum. Om alle details die relevant zijn voor een bepaalde reis te krijgen, is het voldoende om mee te doen met deze drie sleutels.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Voorbeelden van voorspellingstaken
Bepaal het soort voorspellingen dat u wilt maken op basis van gegevensanalyse om de vereiste procestaken te verduidelijken. Hier zijn drie voorbeelden van voorspellingsproblemen die we in deze walkthrough aanpakken, allemaal gebaseerd op het *tipbedrag:\_*

- **Binaire classificatie**: Voorspel of er al dan niet een fooi is betaald voor een reis. Dat wil zeggen, een *tip\_bedrag* dat groter is dan $ 0 is een positief voorbeeld, terwijl een tip *\_bedrag* van $ 0 is een negatief voorbeeld.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Classificatie van meerdere klassen**: Voorspel het bereik van de tipbedragen die voor de reis zijn betaald. We verdelen het *tipbedrag\_* in vijf klassen:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressietaak:** Voorspel het bedrag van de fooi die voor een reis is betaald.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Een HDInsight Hadoop-cluster instellen voor geavanceerde analyses
> [!NOTE]
> Dit is meestal een beheertaak.
> 
> 

U in drie stappen een Azure-omgeving instellen voor geavanceerde analyses die een HDInsight-cluster gebruiken:

1. [Een opslagaccount maken:](../../storage/common/storage-account-create.md)dit opslagaccount wordt gebruikt voor het opslaan van gegevens in Azure Blob-opslag. De gegevens die worden gebruikt in HDInsight clusters bevinden zich ook hier.
2. [Azure HDInsight Hadoop-clusters aanpassen voor het Advanced Analytics-proces en -technologie.](customize-hadoop-cluster.md) Deze stap creëert een HDInsight Hadoop-cluster met 64-bits Anaconda Python 2.7 geïnstalleerd op alle knooppunten. Er zijn twee belangrijke stappen om te onthouden tijdens het aanpassen van uw HDInsight-cluster.
   
   * Vergeet niet om het opslagaccount dat in stap 1 is gemaakt, te koppelen aan uw HDInsight-cluster wanneer u het maakt. Dit opslagaccount heeft toegang tot gegevens die binnen het cluster worden verwerkt.
   * Nadat u het cluster hebt gemaakt, schakelt u Rastoegang in tot het hoofdknooppunt van het cluster. Blader naar het tabblad **Configuratie** en selecteer **Extern inschakelen**. Deze stap geeft de gebruikersreferenties op die worden gebruikt voor extern inloggen.
3. [Een Azure Machine Learning-werkruimte maken:](../studio/create-workspace.md)u gebruikt deze werkruimte om machine learning-modellen te bouwen. Deze taak wordt aangepakt na het voltooien van een eerste gegevensverkenning en down-sampling, met behulp van het HDInsight-cluster.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Haal de gegevens uit een openbare bron
> [!NOTE]
> Dit is meestal een beheertaak.
> 
> 

Als u de gegevensset [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) vanaf de openbare locatie naar uw machine wilt kopiëren, gebruikt u een van de methoden die zijn beschreven in Gegevens verplaatsen van en naar Azure [Blob-opslag.](move-azure-blob.md)

Hier beschrijven we hoe je AzCopy gebruiken om de bestanden met gegevens over te dragen. Als u AzCopy wilt downloaden en installeren, volgt u de instructies bij [Aan de slag met het AzCopy-opdrachtregelhulpprogramma.](../../storage/common/storage-use-azcopy.md)

1. Voer in een opdrachtpromptvenster de volgende AzCopy-opdrachten uit, waarbij * \<path_to_data_folder>* wordt vervangen door de gewenste bestemming:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Wanneer de kopie is voltooid, ziet u in totaal 24 geritste bestanden in de gekozen gegevensmap. Rits de gedownloade bestanden uit naar dezelfde map op uw lokale machine. Noteer de map waar de niet-gecomprimeerde bestanden zich bevinden. Deze map wordt aangeduid als het * \<pad\_\_naar unzipped_data\_bestanden\> * in wat volgt.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Upload de gegevens naar de standaardcontainer van het HDInsight Hadoop-cluster
> [!NOTE]
> Dit is meestal een beheertaak.
> 
> 

Vervang in de volgende AzCopy-opdrachten de volgende parameters door de werkelijke waarden die u hebt opgegeven bij het maken van het Hadoop-cluster en het uitpakken van de gegevensbestanden.

* *** \<path_to_data_folder>*** De map (samen met het pad) op uw machine die de uitgepakte gegevensbestanden bevat.  
* ***de naam van het opslagaccount van hadoop-cluster>\<*** Het opslagaccount dat is gekoppeld aan uw HDInsight-cluster.
* standaardcontainer van Hadoop-cluster>*** \<*** De standaardcontainer die door uw cluster wordt gebruikt. De naam van de standaardcontainer is meestal dezelfde naam als het cluster zelf. Als het cluster bijvoorbeeld 'abc123.azurehdinsight.net' wordt genoemd, is de standaardcontainer abc123.
* *** \<opslagaccountsleutel>*** De sleutel voor het opslagaccount dat door uw cluster wordt gebruikt.

Voer in een opdrachtprompt of een Windows PowerShell-venster de volgende twee AzCopy-opdrachten uit.

Met deze opdracht worden de reisgegevens geüpload naar de ***nyctaxitripraw-map*** in de standaardcontainer van het Hadoop-cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Met deze opdracht worden de tariefgegevens geüpload naar de ***nyctaxifareraw-map*** in de standaardcontainer van het Hadoop-cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

De gegevens moeten nu in blob-opslag zijn en klaar zijn om te worden verbruikt binnen het HDInsight-cluster.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Meld u aan bij het hoofdknooppunt van het Hadoop-cluster en bereid u voor op verkennende gegevensanalyse
> [!NOTE]
> Dit is meestal een beheertaak.
> 
> 

Als u toegang wilt krijgen tot het hoofdknooppunt van het cluster voor verkennende gegevensanalyse en down-sampling van de gegevens, volgt u de procedure die wordt beschreven in [Access the head node van Hadoop Cluster](customize-hadoop-cluster.md).

In deze walkthrough gebruiken we voornamelijk query's die zijn geschreven in [Hive](https://hive.apache.org/), een SQL-achtige querytaal, om voorlopige gegevensverkenningen uit te voeren. De Hive-query's worden opgeslagen in '.hql'-bestanden. Vervolgens down-samplen we deze gegevens die binnen Machine Learning moeten worden gebruikt voor het bouwen van modellen.

Als u het cluster wilt voorbereiden op verkennende gegevensanalyse, downloadt u de '.hql'-bestanden met de relevante Hive-scripts van [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) naar een lokale map (C:\temp) op het hoofdknooppunt. Open de opdrachtprompt vanuit het hoofdknooppunt van het cluster en voer de volgende twee opdrachten uit:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Deze twee commando's downloaden alle '.hql' bestanden die nodig zijn in deze walkthrough naar de lokale directory ***C:\temp&#92;*** in het hoofdknooppunt.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Hive-database en tabellen per maand maken
> [!NOTE]
> Deze taak is meestal voor een beheerder.
> 
> 

U bent nu klaar om Hive-tabellen te maken voor de NYC-taxigegevensset.
Open in het hoofdknooppunt van het Hadoop-cluster de hadoop-opdrachtregel op het bureaublad van het hoofdknooppunt. Voer de hive-map in door de volgende opdracht uit te voeren:

    cd %hive_home%\bin

> [!NOTE]
> Voer alle Hive-opdrachten uit in deze walkthrough vanuit de Hive-opslaglocatie/mapprompt. Hiermee worden eventuele padproblemen automatisch verwerkt. We gebruiken de termen "Hive directory prompt", "Hive bin / directory prompt", en "Hadoop command line" door elkaar in deze walkthrough.
> 
> 

Voer in de hive-mapprompt de volgende opdracht uit in de opdrachtregel Hadoop van het hoofdknooppunt waarmee de Hive-database en -tabellen worden gemaakt:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Hier is de inhoud van de **C:\temp\sample\_hive\_maken\_db\_en\_tables.hql** bestand dat de Hive database **nyctaxidb**creëert, en de tabellen **reis** en **tarief**.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Met dit Hive-script worden twee tabellen gemaakt:

* De **reistafel** bevat reisdetails van elke rit (chauffeursgegevens, ophaaltijd, reisafstand en tijden).
* De **tarieftabel** bevat tariefgegevens (tariefbedrag, tipbedrag, tolgelden en toeslagen).

Als u extra hulp nodig hebt bij deze procedures, of als u alternatieve procedures wilt onderzoeken, raadpleegt u de sectie [Hive-query's rechtstreeks verzenden vanaf de opdrachtregel Hadoop.](move-hive-tables.md#submit)

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Gegevens laden naar Hive-tabellen door partities
> [!NOTE]
> Deze taak is meestal voor een beheerder.
> 
> 

De NYC taxi dataset heeft een natuurlijke partitionering per maand, die we gebruiken om snellere verwerking en query tijden mogelijk te maken. De volgende PowerShell-opdrachten (uitgegeven vanuit de Hive-map met behulp van de Hadoop-opdrachtregel) laden gegevens naar de tabellen van de reis- en tariefhive, verdeeld per maand.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

De **\_gegevens\_\_van\_de steekproefkorfbelasting\_per partitions.hql-bestand** bevat de volgende **opdrachten voor laden:**

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Een aantal van de Hive query's die hier in het verkenningsproces worden gebruikt, omvatten het bekijken van slechts één of twee partities. Maar deze query's kunnen worden uitgevoerd in de hele gegevensset.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Databases weergeven in het HDInsight Hadoop-cluster
Als u de databases wilt weergeven die zijn gemaakt in het HDInsight Hadoop-cluster in het opdrachtregelvenster Hadoop, voert u de volgende opdracht uit in de opdrachtregel Hadoop:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Toon de Hive-tabellen in de **nyctaxidb-database**
Als u de tabellen in de **nyctaxidb-database wilt weergeven,** voert u de volgende opdracht uit in de opdrachtregel Hadoop:

    hive -e "show tables in nyctaxidb;"

We kunnen bevestigen dat de tabellen zijn verdeeld door de volgende opdracht uit te voeren:

    hive -e "show partitions nyctaxidb.trip;"

Hier is de verwachte output:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Op dezelfde manier kunnen we ervoor zorgen dat de tarieftabel wordt verdeeld door de volgende opdracht uit te voeren:

    hive -e "show partitions nyctaxidb.fare;"

Hier is de verwachte output:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Data-exploratie en feature engineering in Hive
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

U Hive-query's gebruiken om gegevensverkenning uit te voeren en technische taken voor de gegevens die in de Hive-tabellen zijn geladen, uit te voeren. Hier volgen voorbeelden van dergelijke taken:

* Bekijk de top 10 records in beide tabellen.
* Bekijk gegevensdistributies van een paar velden in verschillende tijdvensters.
* Onderzoek de gegevenskwaliteit van de lengte- en breedtevelden.
* Genereer binaire en meerklassenclassificatielabels op basis van het tipbedrag.
* Genereer functies door de directe reisafstanden te berekenen.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Verkenning: bekijk de top 10 records in tabelreis
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Als u wilt zien hoe de gegevens eruit zien, onderzoekt u 10 records uit elke tabel. Als u de records wilt inspecteren, voert u de volgende twee query's afzonderlijk uit van de hive-mapprompt in de hadoop-opdrachtregelconsole.

Ga als volgende voor de top 10 records in de reistabel van de eerste maand:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Ga als bedoeld als volgende naar de top 10-records in de tarieftabel van de eerste maand:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

U de records opslaan in een bestand voor een handige weergave met een kleine wijziging in de vorige query:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Verkenning: het aantal records in elk van de 12 partities weergeven
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Van belang is hoe het aantal reizen varieert tijdens het kalenderjaar. Groepering per maand toont de verdeling van de reizen.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Met deze opdracht wordt de volgende uitvoer uitgevoerd:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Hier is de eerste kolom de maand en de tweede is het aantal reizen voor die maand.

We kunnen ook het totale aantal records in onze reisgegevensset tellen door de volgende opdracht uit te voeren op de Hive-mapprompt:

    hive -e "select count(*) from nyctaxidb.trip;"

Deze opdracht levert op:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Met opdrachten die vergelijkbaar zijn met die voor de gegevensset voor de reis, kunnen we Hive-query's uit de Hive-mapprompt uitvoeren om de tariefgegevensset te laten valideren om het aantal records te valideren.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Met deze opdracht wordt deze uitvoer uitgevoerd:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Exact hetzelfde aantal ritten per maand wordt geretourneerd voor beide gegevenssets, waardoor de eerste validatie wordt gegeven dat de gegevens correct zijn geladen.

U het totale aantal records in de tariefgegevensset tellen met behulp van de volgende opdracht uit de hive-mapprompt:

    hive -e "select count(*) from nyctaxidb.fare;"

Deze opdracht levert op:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Het totale aantal records in beide tabellen is ook hetzelfde, waardoor een tweede validatie wordt verstrekt dat de gegevens correct zijn geladen.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploratie: De distributie van de reis door medaillon
> [!NOTE]
> Deze analyse is meestal een taak voor gegevenswetenschapper.
> 
> 

Dit voorbeeld identificeert de medaillons (taxinummers) met meer dan 100 ritten binnen een bepaalde periode. De query profiteert van de partitietabeltoegang, omdat deze wordt bepaald door de partitievariabele **maand**. De queryresultaten worden naar een lokaal bestand geschreven, `C:\temp` **queryoutput.tsv**, in op het hoofdknooppunt.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Hier is de inhoud van de **\_steekproef hive\_trip\_tellen\_door\_medaillon.hql** bestand voor inspectie.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Het medaillon in de NYC taxi dataset identificeert een unieke cabine. U bepalen welke taxi's relatief druk zijn door te vragen welke meer dan een bepaald aantal ritten in een bepaalde periode hebben gemaakt. In het volgende voorbeeld worden cabines geïdentificeerd die meer dan honderd ritten hebben gemaakt in de eerste drie maanden en worden de queryresultaten opgeslagen in een lokaal bestand, **C:\temp\queryoutput.tsv**.

Hier is de inhoud van de **\_steekproef hive\_trip\_tellen\_door\_medaillon.hql** bestand voor inspectie.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Voer in de map Hive-prompt de volgende opdracht uit:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploratie: Trip distributie door medaillon en hack licentie
> [!NOTE]
> Deze taak is meestal voor een gegevenswetenschapper.
> 
> 

Bij het verkennen van een gegevensset willen we vaak de verdeling van groepen waarden onderzoeken. In dit gedeelte vindt u een voorbeeld van hoe u deze analyse voor cabines en chauffeurs uitvoeren.

De **\_\_voorbeeldhive\_\_trip\_telt\_per medaillon license.hql** bestandsgroepen de tariefgegevensset op **medaillon** en **hack_license**, en retourneert het aantal van elke combinatie. Hier zijn de inhoud:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Deze query retourneert cabine- en chauffeurscombinaties, geordend op dalend aantal ritten.

Voer vanuit de hive-mapprompt het als:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

De queryresultaten worden naar een lokaal bestand geschreven, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Verkenning: de kwaliteit van de gegevens beoordelen door te controleren op ongeldige lengte- of breedtegraadrecords
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Een gemeenschappelijk doel van verkennende gegevensanalyse is het uitdelen van ongeldige of slechte records. In het voorbeeld in deze sectie wordt bepaald of de lengte- of breedtevelden een waarde bevatten die ver buiten het NYC-gebied ligt. Aangezien het waarschijnlijk is dat dergelijke records een onjuiste lengtegraad-breedtegraad waarde hebben, willen we ze te elimineren uit alle gegevens die moeten worden gebruikt voor het modelleren.

Hier is de inhoud van **de steekproef\_hive\_kwaliteit\_beoordeling.hql** bestand voor inspectie.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Voer vanuit de hive-mapprompt het als:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Het argument *-S* in deze opdracht onderdrukt de statusschermafdruk van de taken van Hive Map/Reduce. Deze opdracht is handig omdat de schermafdruk van de hive-queryuitvoer beter leesbaar wordt.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploratie: Binaire klasse verdelingen van reis tips
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Voor het binaire classificatieprobleem dat wordt beschreven in de sectie [Voorbeelden van voorspellingstaken,](hive-walkthrough.md#mltasks) is het handig om te weten of er een tip is gegeven of niet. Deze verdeling van tips is binair:

* tip gegeven (Klasse\_1, tip bedrag > $0)  
* geen tip (klasse\_0, tipbedrag = $0)

In **het\_\_volgende\_voorbeeld hive-kantelfrequenties.hql-bestand** wordt de opdracht weergegeven die moet worden uitgevoerd:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Voer vanuit de hive-mapprompt het als:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Verkenning: klasseverdelingen in de instelling voor meerdere klassen
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Voor het classificatieprobleem van meerdere klassen dat wordt beschreven in de sectie [Voorbeelden van voorspellingstaken,](hive-walkthrough.md#mltasks) leent deze gegevensset zich ook voor een natuurlijke classificatie om het bedrag van de gegeven tips te voorspellen. We kunnen opslaglocaties gebruiken om tipbereiken in de query te definiëren. Gebruik het **monster\_hive\_tip\_range\_frequencies.hql** bestand om de klassenverdelingen voor de verschillende tipbereiken te krijgen. Hier zijn de inhoud.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Voer de volgende opdracht uit vanaf de opdrachtregelconsole Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Verkenning: Bereken de directe afstand tussen twee locaties met lengtegraad
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

U wilt misschien weten of er een verschil is tussen de directe afstand tussen twee locaties en de werkelijke reisafstand van de taxi. Een passagier kan minder snel tip als ze erachter komen dat de bestuurder opzettelijk heeft genomen hen door een langere route.

Om de vergelijking tussen de werkelijke reisafstand en de [Haversine-afstand](https://en.wikipedia.org/wiki/Haversine_formula) tussen twee lengtegraadpunten (de afstand grote cirkel te zien), u de trigonometrische functies gebruiken die beschikbaar zijn in Hive:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

In de voorgaande query is R de straal van de aarde in mijlen, en pi wordt omgezet in radialen. De lengtegraad-breedtepunten worden gefilterd om waarden te verwijderen die ver van het NYC-gebied liggen.

In dit geval schrijven we de resultaten naar een map genaamd **queryoutputdir**. De volgorde van de volgende opdrachten maakt eerst deze uitvoermap en voert vervolgens de opdracht Hive uit.

Voer vanuit de hive-mapprompt het als:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


De queryresultaten worden geschreven naar negen Azure-blobs **(queryoutputdir/000000\_0** naar **queryoutputdir/000008\_0)** onder de standaardcontainer van het Hadoop-cluster.

Als u de grootte van de afzonderlijke blobs wilt weergeven, voert u de volgende opdracht uit in de opdracht Hive-map:

    hdfs dfs -ls wasb:///queryoutputdir

Als u de inhoud van een bepaald bestand wilt zien, gebruikt u `copyToLocal` de opdracht **\_hadoop**om de inhoud van een bepaald bestand te zien.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`kan zeer traag zijn voor grote bestanden, en wordt niet aanbevolen voor gebruik met hen.  
> 
> 

Een belangrijk voordeel van het hebben van deze gegevens in een Azure-blob is dat we de gegevens binnen Machine Learning kunnen verkennen met behulp van de module [Gegevens importeren.][import-data]

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Down-sample gegevens en bouw modellen in Machine Learning
> [!NOTE]
> Dit is meestal een taak voor gegevenswetenschapper.
> 
> 

Na de verkennende data-analysefase zijn we nu klaar om de gegevens voor het bouwen van modellen in Machine Learning te downsamplen. In deze sectie laten we zien hoe u een Hive-query gebruiken om de gegevens te downsamplen. Machine Learning kan deze vervolgens openen via de module [Gegevens importeren.][import-data]

### <a name="down-sampling-the-data"></a>Down-sampling van de gegevens
Er zijn twee stappen in deze procedure. Eerst sluiten we ons aan bij de **nyctaxidb.trip** en **nyctaxidb.fare** tafels op drie sleutels die aanwezig zijn in alle records: **medaillon,** **hack\_licentie**, en **pick-up\_datetime**. Vervolgens genereren we een binair classificatielabel, **een fooi**en een classificatielabel van meerdere klassen, **\_tipklasse.**

Als u de down-sampled-gegevens rechtstreeks vanuit de module [Gegevens importeren][import-data] in Machine Learning wilt kunnen gebruiken, moet u de resultaten van de vorige query opslaan in een interne Hive-tabel. In wat volgt, maken we een interne Hive-tabel en vullen we de inhoud ervan in met de samengevoegde en down-sampled gegevens.

De query past standaard Hive-functies rechtstreeks toe om de volgende tijdparameters te genereren vanuit het veld **Datumdatum ophalen:\_**
- uur van de dag
- week van het jaar
- doordeweeks ('1' staat voor maandag, en '7' staat voor zondag)

De query genereert ook de directe afstand tussen de ophaal- en dropofflocaties. Zie [LanguageManual UDF voor](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)een volledige lijst van dergelijke functies.

De query down-samples de gegevens, zodat de query resultaten kunnen passen in Azure Machine Learning Studio. Slechts ongeveer 1 procent van de oorspronkelijke dataset wordt geïmporteerd in de studio.

Hier zijn de inhoud van **de steekproef\_korf\_\_voor\_te bereiden op aml\_full.hql** bestand dat gegevens voor modelbuilding in Machine Learning bereidt:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Ga als volgt te werk om deze query uit de hive-mapprompt uit te voeren:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

We hebben nu een interne tabel, **nyctaxidb.nyctaxi_downsampled_dataset**, die toegankelijk is met behulp van de [Import Data][import-data] module van Machine Learning. Bovendien kunnen we deze dataset gebruiken voor het bouwen van Machine Learning-modellen.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Gebruik de module Gegevens importeren in Machine Learning om toegang te krijgen tot de gegevens die naar beneden zijn gesampled
Als u Hive-query's wilt uitgeven in de module [Gegevens importeren][import-data] van Machine Learning, hebt u toegang nodig tot een Machine Learning-werkruimte. U hebt ook toegang nodig tot de referenties van het cluster en het bijbehorende opslagaccount.

Hier volgen enkele details over de module [Gegevens importeren][import-data] en de parameters die moeten worden ingevoerd:

**HCatalog server URI:** Als de clusternaam **abc123**is, gebruik dan: https://abc123.azurehdinsight.net.

**Hadoop gebruikersnaamnaam:** de gebruikersnaam gekozen voor het cluster (niet de gebruikersnaam voor externe toegang).

**Hadoop gebruikersaccount wachtwoord:** Het wachtwoord gekozen voor het cluster (niet de externe toegang wachtwoord).

**Locatie van uitvoergegevens**: Gekozen als Azure.

**Azure Storage-accountnaam:** naam van het standaardopslagaccount dat aan het cluster is gekoppeld.

**Azure-containernaam:** de standaardcontainernaam voor het cluster en is doorgaans hetzelfde als de clusternaam. Voor een cluster genaamd **abc123**, de naam is abc123.

> [!IMPORTANT]
> Elke tabel die we willen opvragen met behulp van de module [Gegevens importeren][import-data] in Machine Learning moet een interne tabel zijn.
> 
> 

Hier u bepalen of een tabel **T** in een database **D.db** een interne tabel is. Voer in de map Hive-prompt de volgende opdracht uit:

    hdfs dfs -ls wasb:///D.db/T

Als de tabel een interne tabel is en deze wordt ingevuld, moet de inhoud hier worden weergegeven.

Een andere manier om te bepalen of een tabel een interne tabel is, is door Azure Storage Explorer te gebruiken. Gebruik deze optie om naar de standaardcontainernaam van het cluster te navigeren en vervolgens te filteren op de tabelnaam. Als de tabel en de inhoud ervan worden weergegeven, bevestigt dit dat het een interne tabel is.

Hier is een screenshot van de Hive query en de [Import Data][import-data] module:

![Schermafbeelding van Hive-query voor module Gegevens importeren](./media/hive-walkthrough/1eTYf52.png)

Omdat onze down-sampled gegevens zich in de standaardcontainer bevinden, is de resulterende Hive-query van Machine Learning eenvoudig. Het is gewoon een **select * van\_nyctaxidb.nyctaxi\_downsampled gegevens**.

De dataset kan nu worden gebruikt als uitgangspunt voor het bouwen van Machine Learning-modellen.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Modellen bouwen in Machine Learning
U nu overgaan tot modelbouw en modelimplementatie in [Machine Learning.](https://studio.azureml.net) De gegevens zijn klaar voor ons om te gebruiken bij het aanpakken van de eerder geïdentificeerde voorspellingsproblemen:

- **Binaire classificatie**: Om te voorspellen of er al dan niet een fooi is betaald voor een reis.

  **Leerling gebruikt:** Twee-klasse logistieke regressie

  a. Voor dit probleem wordt het label target (of **klasse) getipt**. De oorspronkelijke down-sampled dataset heeft een paar kolommen die doellekken zijn voor dit classificatie-experiment. In het bijzonder, **\_tip klasse,** **\_tip bedrag,** en **de totale\_hoeveelheid** onthullen informatie over het doellabel die niet beschikbaar is op het testen tijd. We verwijderen deze kolommen uit overweging met behulp van de module [Kolommen selecteren in gegevensset.][select-columns]

  Het volgende diagram toont ons experiment om te voorspellen of er al dan niet een fooi is betaald voor een bepaalde reis:

  ![Diagram van experiment om te voorspellen of de tip is betaald](./media/hive-walkthrough/QGxRz5A.png)

  b. Voor dit experiment waren onze doellabeldistributies ongeveer 1:1.

   In de volgende grafiek ziet u de verdeling van de labels van de tipklasse voor het binaire classificatieprobleem:

  ![Grafiek van de verdeling van tipsklassenlabels](./media/hive-walkthrough/9mM4jlD.png)

    Als gevolg hiervan verkrijgen we een gebied onder de curve (AUC) van 0,987, zoals blijkt uit de volgende figuur:

  ![Grafiek van AUC-waarde](./media/hive-walkthrough/8JDT0F8.png)

- **Classificatie van meerdere klassen**: Het bereik van de voor de reis betaalde tipbedragen voorspellen door gebruik te maken van de eerder gedefinieerde klassen.

  **Leerling gebruikt:** Multiclass logistieke regressie

  a. Voor dit probleem is ons doellabel (of klasse) **tipklasse,\_** die een van de vijf waarden kan aannemen (0,1,2,3,4). Net als in de binaire classificatie geval, we hebben een paar kolommen die doel lekken voor dit experiment. In het bijzonder, **getipt,** **tip\_bedrag**, en **de totale\_hoeveelheid** onthullen informatie over het doellabel die niet beschikbaar is op het testen tijd. We verwijderen deze kolommen met de module [Kolommen selecteren in gegevensset.][select-columns]

  In het volgende diagram ziet u het experiment om te voorspellen in welke opslaglocatie een tip waarschijnlijk zal vallen. De bakken zijn: Klasse 0: tip = $0, Klasse 1: tip > $0 en tip <= $5, Klasse 2: tip > $5 en tip <= $10, Klasse 3: tip > $10 en tip <= $20, en Klasse 4: tip > $20.

  ![Diagram van experiment om opslaglocatie voor uiteinde te voorspellen](./media/hive-walkthrough/5ztv0n0.png)

  We laten nu zien hoe de eigenlijke testklasseverdeling eruit ziet. Klasse 0 en klasse 1 komen veel voor en de andere klassen zijn zeldzaam.

  ![Grafiek van de verdeling van de testklasse](./media/hive-walkthrough/Vy1FUKa.png)

  b. Voor dit experiment gebruiken we een verwarringsmatrix om te kijken naar de nauwkeurigheid van de voorspelling zoals hier wordt weergegeven:

  ![Verwarringsmatrix](./media/hive-walkthrough/cxFmErM.png)

  Terwijl de klasse nauwkeurigheid op de heersende klassen zijn goed, het model niet goed werk van "leren" op de zeldzamere klassen.

- **Regressietaak:** Het bedrag van de fooi voor een reis voorspellen.

  **Leerling gebruikt:** Verhoogde beslissingsboom

  a. Voor dit probleem is het doellabel (of klasse) **het tipbedrag.\_** De doellekken in dit geval zijn: **getipt,** **\_tipklasse**en **totaal\_bedrag**. Al deze variabelen onthullen informatie over het tipbedrag dat doorgaans niet beschikbaar is tijdens het testen. We verwijderen deze kolommen met de module [Kolommen selecteren in gegevensset.][select-columns]

  In het volgende diagram ziet u het experiment om de hoeveelheid van de gegeven tip te voorspellen:

  ![Diagram van experiment om de hoeveelheid tip te voorspellen](./media/hive-walkthrough/11TZWgV.png)

  b. Voor regressieproblemen meten we de nauwkeurigheid van de voorspelling door te kijken naar de kwadraatfout in de voorspellingen en de bepalingscoëfficiënt:

  ![Schermafbeelding van voorspellingsstatistieken](./media/hive-walkthrough/Jat9mrz.png)

  Hier is de bepalingscoëfficiënt 0,709, wat impliceert dat ongeveer 71 procent van de variantie wordt verklaard door de modelcoëfficiënten.

> [!IMPORTANT]
> Zie [Wat is Machine Learning](../studio/what-is-machine-learning.md)voor meer informatie over Machine Learning en hoe u deze openen en gebruiken. Daarnaast omvat de [Azure AI Gallery](https://gallery.cortanaintelligence.com/) een scala aan experimenten en biedt een grondige introductie in het scala aan mogelijkheden van Machine Learning.
> 
> 

## <a name="license-information"></a>Licentiegegevens
Deze voorbeeld-walkthrough en de bijbehorende scripts worden gedeeld door Microsoft onder de MIT-licentie. Zie het bestand **LICENSE.txt** in de map van de voorbeeldcode op GitHub voor meer informatie.

## <a name="references"></a>Verwijzingen
• [Andrés Monroy NYC Taxi Trips Download Pagina](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC's Taxi Trip Data door Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi en Limousine Commissie Onderzoek en Statistieken](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



