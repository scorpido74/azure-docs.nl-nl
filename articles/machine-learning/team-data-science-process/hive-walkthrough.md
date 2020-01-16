---
title: Gegevens verkennen in een Hadoop-cluster - Team Data Science Process
description: Met behulp van het Team Data Science Process voor een end-to-end-scenario, die gebruikmaakt van een HDInsight Hadoop-cluster te bouwen en implementeren van een model.
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
ms.openlocfilehash: 0549427cfc99703af9f13280cf7377106423367b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982012"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Het Team Data Science Process in actie: gebruik Azure HDInsight Hadoop-clusters
In dit scenario gebruiken we de [Team Data Science Process (TDSP)](overview.md) in een end-to-end-scenario. We gebruiken een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) wilt opslaan, verkennen, en de functie-engineering gegevens uit de openbaar beschikbare [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) gegevensset, en down-sampling van de gegevens. Voor het afhandelen van binaire en multiklassen classificatie en de voorspellende taken regressie, bouwen we modellen van de gegevens met Azure Machine Learning. 

Zie voor een overzicht waarin wordt getoond hoe u voor het afhandelen van een grotere gegevensset, [Team Data Science Process - met behulp van Azure HDInsight Hadoop-Clusters op een gegevensset van 1 TB](hive-criteo-walkthrough.md).

U kunt ook een IPython notebook gebruiken de taken die zijn gepresenteerd in deze stapsgewijze Kennismaking die gebruikmaakt van de gegevensset van 1 TB. Zie voor meer informatie, [Criteo scenario met behulp van een Hive ODBC-verbinding](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Beschrijving van de gegevensset NYC Taxi Trips
De reisgegevens NYC over taxi's is ongeveer 20 GB gecomprimeerde door komma's gescheiden waarden (CSV)-bestanden (~ 48 GB niet-gecomprimeerd). Het heeft meer dan 173 miljoen afzonderlijke trips en bevat de tarieven voor elke reis betaald. Elke record van de fietstocht bevat de locatie op te halen en dropoff en tijd, geanonimiseerde hack (van het stuurprogramma) licentienummer en straten nummer (unieke ID van de taxi). De gegevens bevat informatie over alle gegevens in het jaar 2013 en is beschikbaar in de volgende twee gegevenssets voor elke maand:

- De CSV-bestanden trip_data bevatten reis details. Dit omvat het aantal personen, op te halen en dropoff punten, duur van de tocht en lengte van de fietstocht. Hier volgen enkele voorbeeldrecords:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- De trip_fare CSV-bestanden bevatten details van het tarief voor elke reis betaald. Dit omvat betalingstype, fare bedrag, toeslag en belastingen, tips en tolwegen, en de totale hoeveelheid betaald. Hier volgen enkele voorbeeldrecords:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

De unieke sleutel voor deelname aan reis\_gegevens en reis\_fare bestaat uit de velden: straten hack\_licentie, en ophalen van\_datum/tijd. Als u alle details relevant zijn voor een bepaalde reis, is het voldoende om toe te voegen aan deze drie sleutels.

## <a name="mltasks"></a>Voorbeelden van taken voor voorspelling
Bepaal de aard van voorspellingen die u wilt maken op basis van gegevensanalyse. Dit helpt de taken die u wilt opnemen in uw proces te verduidelijken. Hier volgen drie voorbeelden van voorspelling problemen die we in dit scenario behandelen. Deze zijn gebaseerd op de *tip\_bedrag*:

- **Binaire classificatie**: voorspellen of een tip voor een reis is betaald. Dat wil zeggen, een *tip\_bedrag* die groter is dan $0 een voorbeeld van een positieve is, terwijl een *tip\_bedrag* van $0 is een voorbeeld van een negatief zijn.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Multiklassen classificatie**: het bereik van de tip bedragen betalen voor de reis te voorspellen. We delen de *tip\_bedrag* in vijf klassen:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regressie taak**: het bedrag van de tip betaald voor een reis te voorspellen.  

## <a name="setup"></a>Instellen van een HDInsight Hadoop-cluster voor geavanceerde analyses
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

U kunt een Azure-omgeving voor geavanceerde analyses, waardoor een HDInsight-cluster in drie stappen instellen:

1. [Maak een opslagaccount](../../storage/common/storage-account-create.md): dit storage-account wordt gebruikt voor het opslaan van gegevens in Azure Blob-opslag. De gegevens die worden gebruikt in HDInsight-clusters ook bevinden zich hier.
2. [Azure HDInsight Hadoop-clusters aanpassen voor de Advanced Analytics Process and Technology](customize-hadoop-cluster.md). Deze stap maakt u een HDInsight Hadoop-cluster met 64-bits Anaconda Python 2.7 geïnstalleerd op alle knooppunten. Er zijn twee belangrijke stappen om te onthouden tijdens het aanpassen van uw HDInsight-cluster.
   
   * Houd er rekening mee te koppelen van het opslagaccount dat in stap 1 met uw HDInsight-cluster gemaakt wanneer u deze maakt. Dit storage-account heeft toegang tot gegevens die wordt verwerkt binnen het cluster.
   * Nadat u het cluster hebt gemaakt, kunt u externe toegang inschakelen met het hoofdknooppunt van het cluster. Blader naar de **configuratie** tabblad, en selecteer **externe inschakelen**. Deze stap geeft de referenties van de gebruiker gebruikt voor externe aanmelding.
3. [Een Azure Machine Learning-werkruimte maken](../studio/create-workspace.md): U deze werkruimte kunt machine learning-modellen bouwen. Deze taak is gericht na het voltooien van een initiële gegevens verkennen en down-sampling, met behulp van het HDInsight-cluster.

## <a name="getdata"></a>De gegevens ophalen uit een openbare gegevensbron
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

Kopiëren van de [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) gegevensset naar de computer van de openbare locatie, gebruikt u een van de methoden die worden beschreven in [verplaatsen van gegevens en naar Azure Blob-opslag](move-azure-blob.md).

Hier wordt beschreven hoe u AzCopy gebruiken om over te dragen van de bestanden met gegevens. Volg de instructies op om te downloaden en installeren van AzCopy, [aan de slag met het AzCopy-opdrachtregelprogramma](../../storage/common/storage-use-azcopy.md).

1. Voer vanuit een opdracht prompt venster de volgende AzCopy-opdrachten uit en vervang *\<path_to_data_folder >* met de gewenste bestemming:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Wanneer de kopie is voltooid, ziet u een totaal van 24 ZIP-bestanden in de map data gekozen. Pak het gedownloade bestanden naar dezelfde map op uw lokale computer. Maak een notitie van de map waar de niet-gecomprimeerde bestanden zich bevinden. Deze map wordt de *\<pad genoemd\_naar\_unzipped_data\_bestanden* in wat volgt.

## <a name="upload"></a>De gegevens uploaden naar de standaardcontainer van het HDInsight Hadoop-cluster
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

In de volgende AzCopy-opdrachten, vervangt u de volgende parameters met de werkelijke waarden die u hebt opgegeven bij het maken van het Hadoop-cluster en het uitpakken van de gegevensbestanden.

* ***\<path_to_data_folder >*** De map (samen met het pad) op de computer die de uitgepakte gegevens bestanden bevat.  
* ***\<Storage-account naam van Hadoop-cluster >*** Het opslag account dat is gekoppeld aan uw HDInsight-cluster.
* ***\<standaard container van Hadoop-cluster >*** De standaard container die door uw cluster wordt gebruikt. Houd er rekening mee dat de naam van de standaard-container meestal dezelfde naam als het cluster zelf wordt. Bijvoorbeeld, als het cluster wordt 'abc123.azurehdinsight.net' genoemd, is de standaardcontainer abc123.
* \<van de sleutel voor het ***opslag account >*** De sleutel voor het opslag account dat door uw cluster wordt gebruikt.

Voer de volgende twee AzCopy-opdrachten uit vanaf de opdrachtprompt of een Windows PowerShell-venster.

Met deze opdracht wordt geüpload de reisgegevens op de ***nyctaxitripraw*** Active directory in de standaardcontainer van het Hadoop-cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Met deze opdracht uploadt de tarief-gegevens naar de ***nyctaxifareraw*** Active directory in de standaardcontainer van het Hadoop-cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

De gegevens worden nu in Blob-opslag en gereed is om te worden verbruikt binnen het HDInsight-cluster.

## <a name="#download-hql-files"></a>Meld u aan met het hoofdknooppunt van Hadoop-cluster en bereid voor experimentele data-analyse
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

Voor toegang tot het hoofdknooppunt van het cluster voor experimentele data-analyse en down-sampling van de gegevens, volgt u de procedure wordt beschreven in [toegang tot het hoofdknooppunt van Hadoop-Cluster](customize-hadoop-cluster.md).

In dit scenario gebruiken we voornamelijk query's die zijn geschreven in [Hive](https://hive.apache.org/), een SQL-achtige querytaal, voorlopige gegevens explorations uitvoeren. Het Hive-query's worden opgeslagen in .hql bestanden. We vervolgens down-sampling van deze gegevens moet worden gebruikt in Machine Learning voor het ontwikkelen van modellen.

Het cluster om voor te bereiden experimentele data-analyse, downloaden de .hql-bestanden met de relevante Hive-scripts uit [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) naar een lokale map (C:\temp) op het hoofdknooppunt. Om dit te doen, opent u de opdrachtprompt vanuit het hoofdknooppunt van het cluster en voer de volgende twee opdrachten uit:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Deze twee opdrachten downloaden alle .hql bestanden die nodig zijn in dit scenario naar een lokale map ***C:\temp&#92;***  in het hoofdknooppunt.

## <a name="#hive-db-tables"></a>Hive-database en tabellen is gepartitioneerd op basis van de maand maken
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

U bent nu klaar om te maken van Hive-tabellen voor de NYC taxi-gegevensset.
Open in het hoofdknooppunt van het Hadoop-cluster, de Hadoop-opdrachtregel op het bureaublad van het hoofdknooppunt. Geef de Hive-map met de volgende opdracht:

    cd %hive_home%\bin

> [!NOTE]
> Alle Hive-opdrachten uitvoeren in dit overzicht van de Hive-bin / directory prompt. Dit verwerkt pad problemen automatisch. We gebruiken de termen "Hive directory prompt", "Hive bin / directory prompt ', en"Hadoop-opdrachtregels' door elkaar in dit scenario.
> 
> 

Het Hive-directory-opdrachtprompt en voer de volgende opdracht in de Hadoop-opdrachtregel van het hoofdknooppunt. Hiermee wordt de Hive-query voor het maken van de Hive-database en tabellen verzonden:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Hier is de inhoud van de **C:\temp\sample\_hive\_maken\_db\_en\_tables.hql** bestand. Hiermee maakt u de Hive-database **nyctaxidb**, tabellen en **reis** en **fare**.

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

Deze Hive-script worden twee tabellen gemaakt:

* De **reis** tabel bevat details van de reis van elke rit (stuurprogrammagegevens, de tijd op te halen, reis afstand en tijden).
* De **fare** tabel bevat details fare (fare, tip het bedrag, tolwegen, en toeslagen).

Als u met deze procedures als u meer hulp nodig hebt, of u wilt onderzoeken alternatieve die, Zie de sectie [indienen Hive-query's rechtstreeks vanaf de opdrachtregel Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Laden van gegevens met Hive-tabellen partities
> [!NOTE]
> Dit is meestal een admin-taak.
> 
> 

De NYC taxi gegevensset heeft een natuurlijke partitioneren per maand, die we gebruiken om in te schakelen snellere verwerking en queryprestaties tijden. De volgende PowerShell-opdrachten (met behulp van de Hadoop-opdrachtregels uitgegeven door de Hive-map) laden van gegevens naar de reis en ritbedrag Hive-tabellen, gepartitioneerd op basis van de maand.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

De **voorbeeld\_hive\_laden\_gegevens\_door\_partitions.hql** -bestand bevat de volgende **laden** opdrachten:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Houd er rekening mee dat een aantal van de Hive-query's die hier worden gebruikt in het proces verkennen betrekking hebben op slechts één of twee partities kijken. Maar deze query's kunnen worden uitgevoerd in de hele gegevensset.

### <a name="#show-db"></a>Databases weergeven in het HDInsight Hadoop-cluster
Als u wilt weergeven van de databases die zijn gemaakt in HDInsight Hadoop-cluster in het Hadoop-opdrachtregelvenster, voer de volgende opdracht in de Hadoop-opdrachtregel:

    hive -e "show databases;"

### <a name="#show-tables"></a>Weergeven van de Hive-tabellen in de **nyctaxidb** database
Om weer te geven van de tabellen in de **nyctaxidb** database, voer de volgende opdracht in de Hadoop-opdrachtregel:

    hive -e "show tables in nyctaxidb;"

We kunt bevestigen dat de tabellen worden gepartitioneerd met de volgende opdracht:

    hive -e "show partitions nyctaxidb.trip;"

Dit is de verwachte uitvoer:

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

Op deze manier kunnen we dat de tarief-tabel is gepartitioneerd met de volgende opdracht:

    hive -e "show partitions nyctaxidb.fare;"

Dit is de verwachte uitvoer:

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

## <a name="#explore-hive"></a>Gegevens verkennen en feature-engineering in Hive
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

U kunt de Hive-query's gebruiken om uit te voeren gegevens verkennen en functie-engineering-taken voor de gegevens geladen in de Hive-tabellen. Hier volgen enkele voorbeelden van dergelijke taken:

* De top 10-records weergeven in beide tabellen.
* Verken gegevens distributies van enkele velden in verschillende tijdvensters.
* Onderzoek de kwaliteit van de gegevens van de velden breedtegraad en lengtegraad.
* Binaire en multiklassen classificatielabels op basis van de hoeveelheid tip genereren.
* Functies genereren door de directe reis afstanden computing.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Verkennen: De top 10-records in de tabel trip weergeven
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Als u wilt zien hoe de gegevens eruit ziet, controleert u 10 records uit elke tabel. Om te controleren records, de volgende twee query's uitvoeren afzonderlijk vanaf de opdrachtprompt Hive directory in de Hadoop-opdrachtregelconsole.

De top 10-records in de tabel trip ophalen uit de eerste maand:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

De top 10-records in de tabel fare ophalen uit de eerste maand:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

U kunt de records naar een bestand voor het weergeven van handige opslaan. Dit wordt bereikt door een kleine wijziging aan de voorgaande query:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Verkennen: Het aantal records in elk van de 12 partities weergeven
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Is waarop het aantal gegevens in het kalenderjaar varieert van belang zijn. Groeperen per maand laat zien dat de distributie van gegevens.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Dit biedt ons de volgende uitvoer:

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

Hier, de eerste kolom is de maand en het tweede is het aantal trips voor die maand.

We kunnen ook het totale aantal records tellen in onze gegevensset reis door het uitvoeren van de volgende opdracht bij de prompt Hive-directory:

    hive -e "select count(*) from nyctaxidb.trip;"

Dit resulteert in:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Met behulp van de opdrachten is vergelijkbaar met die wordt weergegeven voor de reis-gegevensset, kunnen we Hive-query's vanuit de opdrachtprompt van de Hive-map voor de gegevensset fare voor het valideren van het aantal records geven.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Dit biedt ons de volgende uitvoer:

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

Houd er rekening mee dat er precies hetzelfde aantal bezoeken per maand wordt geretourneerd voor beide gegevenssets. Dit biedt de eerste validatie van de gegevens correct zijn geladen.

U kunt het totale aantal records in de gegevensset fare met behulp van de volgende opdracht achter de opdrachtprompt van de directory Hive tellen:

    hive -e "select count(*) from nyctaxidb.fare;"

Dit resulteert in:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Het totale aantal records in beide tabellen is ook hetzelfde. Dit biedt een tweede validatie van de gegevens correct zijn geladen.

### <a name="exploration-trip-distribution-by-medallion"></a>Verkennen: Verdeling reis straten
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

In dit voorbeeld geeft de medallions (taxi getallen) met meer dan 100 trips binnen een bepaalde periode. De query profiteren van de toegang tot gepartitioneerde tabellen, omdat deze zijn afhankelijk van de variabele partitie **maand**. De queryresultaten worden geschreven naar een lokaal bestand **queryoutput.tsv**in `C:\temp` op het hoofdknooppunt.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Hier is de inhoud van de **voorbeeld\_hive\_reis\_aantal\_door\_medallion.hql** bestand voor inspectie.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

De straten in de NYC taxi-gegevensset geeft een unieke CAB-bestand. U kunt bepalen welke cab-bestanden zijn relatief bezet door te stellen welke die meer dan een bepaald aantal trips in een bepaalde periode. Het volgende voorbeeld identificeert CAB-bestanden die meer dan honderd trips aangebracht in de eerste drie maanden en slaat de queryresultaten naar een lokaal bestand **C:\temp\queryoutput.tsv**.

Hier is de inhoud van de **voorbeeld\_hive\_reis\_aantal\_door\_medallion.hql** bestand voor inspectie.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Het Hive-directory-opdrachtprompt en voer de volgende opdracht:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Verkennen: Reis distributie per licentie straten en hack
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Wanneer een gegevensset verkennen, willen we vaak CO-aantallen van groepen van waarden te controleren. Deze sectie bevat een voorbeeld van hoe u dit doet voor CAB-bestanden en stuurprogramma's.

De **voorbeeld\_hive\_reis\_aantal\_door\_straten\_license.hql** bestand worden gegroepeerd op de gegevensset fare **straten** en **hack_license**, en retourneert, telt elke combinatie. Hier volgen de inhoud ervan:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Deze query retourneert combinaties van CAB-bestand en stuurprogramma's, geordend op Aflopend aantal bezoeken.

Het Hive-directory-opdrachtprompt en voert u de volgende uit:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

De queryresultaten worden geschreven naar een lokaal bestand **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Verkennen: Beoordeling van de kwaliteit van de gegevens door te zoeken naar een ongeldige lengte of breedte records
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Een algemene doelstelling van experimentele data-analyse is te selecteren van ongeldige of onjuiste records. Het voorbeeld in deze sectie bepaalt of de lengte of de breedte velden een waarde ver buiten de NYC bevatten. Omdat dit waarschijnlijk dat deze records een foutieve Breedtegraadwaarde van de lengtegraad hebben, willen we deze in alle gegevens die moet worden gebruikt voor het maken van modellering te elimineren.

Hier is de inhoud van **voorbeeld\_hive\_kwaliteit\_assessment.hql** bestand voor inspectie.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Het Hive-directory-opdrachtprompt en voert u de volgende uit:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

De *-S* argument is opgenomen in deze opdracht wordt de afdruk van het scherm status van de taken voor toewijzen/verminderen Hive onderdrukt. Dit is handig omdat het scherm afdrukken van de Hive-query-uitvoer beter leesbaar.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Verkennen: Binaire klasse-distributies reis tips
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Voor de binair klassificatieprobleem die worden beschreven in de [voorbeelden van taken die voorspelling](hive-walkthrough.md#mltasks) sectie, is het handig om te weten of een tip is opgegeven of niet. Deze verdeling van tips is binaire:

* Tip gegeven (klasse 1, tip\_bedrag > $0)  
* Er is geen tip (0, klasse, tip\_bedrag = $0)

De volgende **voorbeeld\_hive\_punt\_frequencies.hql** bestand doet dit:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Het Hive-directory-opdrachtprompt en voert u de volgende uit:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Verkennen: Klasse-distributies in de instelling voor multiklassen
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Voor de regressieprobleem die worden beschreven in de [voorbeelden van taken die voorspelling](hive-walkthrough.md#mltasks) sectie deze gegevensset ook gepaard met een natuurlijke classificatie te voorspellen van het bedrag van de tips gegeven. We kunnen opslaglocaties gebruiken voor het bereiken van de tip definiëren in de query. Als u de klasse-Distributies voor de verschillende bereiken tip, gebruikt de **voorbeeld\_hive\_tip\_bereik\_frequencies.hql** bestand. Hier vindt u de inhoud ervan.

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

Voer de volgende opdracht uit vanaf de opdrachtregel Hadoop-console:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Verkennen: Berekenen van de directe afstand tussen de twee lengtegraad, breedtegraad locaties
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Mogelijk wilt u weten of er een verschil tussen de directe afstand tussen twee locaties en de werkelijke reis afstand van de taxi. Een passagier mogelijk minder waarschijnlijk, als ze weten dat het stuurprogramma opzettelijk ze heeft genomen voor een langere route tip.

Om te zien van de vergelijking tussen de werkelijke reis afstand en de [Haversine afstand](https://en.wikipedia.org/wiki/Haversine_formula) tussen twee lengtegraad, breedtegraad punten (de "great cirkel" afstand), kunt u de trigonometrische functies die beschikbaar zijn in de component:

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

In de voorgaande query R is de radius van de aarde in mijl en pi wordt omgezet in radialen. Houd er rekening mee dat de lengtegraad, breedtegraad-punten worden gefilterd, zodat het verwijderen van de waarden die ver van het gebied NYC ligt.

In dit geval, schrijven we de resultaten naar een map genaamd **queryoutputdir**. De volgorde van de volgende opdrachten maakt eerst deze uitvoermap en voert vervolgens de Hive-opdracht.

Het Hive-directory-opdrachtprompt en voert u de volgende uit:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


De queryresultaten worden geschreven naar negen Azure-blobs (**queryoutputdir/000000\_0** naar **queryoutputdir/000008\_0**), onder de standaardcontainer van het Hadoop-cluster.

Als u wilt zien van de grootte van de afzonderlijke blobs, voer de volgende opdracht achter de opdrachtprompt van de directory Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Als u wilt zien van de inhoud van een bepaald bestand, zeg **000000\_0**, gebruik van Hadoop `copyToLocal` opdracht.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` erg langzaam is voor grote bestanden en wordt niet aanbevolen voor gebruik met hen.  
> 
> 

Een belang rijk voor deel van het gebruik van deze gegevens bevindt zich in een Azure-Blob. Dit kan de gegevens in Machine Learning verkennen met behulp van de module [gegevens importeren][import-data] .

## <a name="#downsample"></a>Down-sampling van gegevens en Machine Learning-modellen te bouwen
> [!NOTE]
> Dit is meestal een gegevenswetenschappers van elk gegevens taak.
> 
> 

Na de analysefase experimentele gegevens bent er nu klaar om down-sampling van de gegevens voor het ontwikkelen van modellen in Machine Learning. In deze sectie laten we zien hoe u een Hive-query voor down-sampling van de gegevens. Machine Learning opent deze vervolgens vanuit de module [gegevens importeren][import-data] .

### <a name="down-sampling-the-data"></a>De gegevens down-sampling
Er zijn twee stappen in deze procedure. Eerst we toevoegen de **nyctaxidb.trip** en **nyctaxidb.fare** tabellen op de drie sleutels die aanwezig in alle records zijn: **straten**, **hack\_ licentie**, en **ophalen\_datum-/** . We vervolgens een label binaire classificatie genereren **punt**, en een multiklassen classificatielabel **tip\_klasse**.

Als u de gegevens van de voor gaande steek proef rechtstreeks vanuit de module [gegevens importeren][import-data] in machine learning wilt gebruiken, moet u de resultaten van de vorige query opslaan in een interne Hive-tabel. In het volgende, we een interne Hive-tabel maken en vullen van de inhoud ervan met de gegevens van een domein en down-voorbeelden worden gemaakt.

De query is van toepassing standaardfuncties Hive rechtstreeks voor het genereren van het volgende achter de **ophalen\_datum-/** veld:
- uur van de dag
- week van jaar
- dag van de week (1 staat voor maandag en 7 staat voor zondag)

De query ook de directe afstand tussen de locaties op te halen en dropoff gegenereerd. Zie voor een volledige lijst van dergelijke functies, [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

De query vervolgens omlaag-voorbeelden van de gegevens zodat de queryresultaten in Azure Machine Learning Studio inpassen kunnen. Alleen ongeveer 1 procent van de oorspronkelijke gegevensset is geïmporteerd in de studio.

Hier vindt u de inhoud van **voorbeeld\_hive\_voorbereiden\_voor\_aml\_full.hql** -bestand dat worden gegevens voorbereid voor het bouwen van in Machine Learning-model:

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

Deze query uitvoeren vanaf de opdrachtprompt van de Hive-map:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

We hebben nu een interne tabel, **nyctaxidb. nyctaxi_downsampled_dataset**, die toegankelijk is via de module [gegevens importeren][import-data] vanuit machine learning. We kunnen bovendien deze gegevensset gebruiken voor het bouwen van Machine Learning-modellen.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>De module gegevens importeren in Machine Learning gebruiken voor toegang tot de gegevens naar beneden steekproef
Als u Hive-query's wilt uitgeven in de module [gegevens importeren][import-data] van machine learning, moet u toegang hebben tot een machine learning-werk ruimte. U moet ook toegang tot de referenties van het cluster en de gekoppelde storage-account.

Hier volgen enkele details over de module [gegevens importeren][import-data] en de para meters die moeten worden ingevoerd:

**HCatalog server-URI**: als de naam van het cluster is **abc123**, dit is eenvoudig: https://abc123.azurehdinsight.net.

**Hadoop-gebruikersaccountnaam**: de naam van de gebruiker gekozen voor het cluster (niet de RAS-gebruikersnaam).

**Accountwachtwoord voor Hadoop-ser**: het wachtwoord dat is gekozen voor het cluster (niet het wachtwoord voor externe toegang).

**Locatie van de uitvoergegevens**: dit is gekozen om te worden van Azure.

**Azure storage-accountnaam**: naam van het standaardopslagaccount dat is gekoppeld aan het cluster.

**De containernaam van de Azure**: dit is de standaardnaam van de container voor het cluster en is meestal hetzelfde als de naam van het cluster. Voor een cluster met de naam **abc123**, dit abc123 is.

> [!IMPORTANT]
> Elke tabel die u wilt opvragen met behulp van de module [gegevens importeren][import-data] in machine learning moet een interne tabel zijn.
> 
> 

Hier wordt beschreven hoe u bepalen of een tabel **T** in een database **D.db** is een interne tabel. Het Hive-directory-opdrachtprompt en voer de volgende opdracht:

    hdfs dfs -ls wasb:///D.db/T

Als de tabel een interne tabel is en dit is ingevuld, moeten de inhoud ervan hier weergegeven.

Een andere manier om te bepalen of een tabel een interne tabel is is het gebruik van Azure Storage Explorer. Gebruik deze om te navigeren naar de standaardnaam van de container van het cluster en vervolgens filteren op naam van de tabel. Als de tabel en de inhoud ervan wordt weergegeven, bevestigt dit dat het is een interne tabel.

Hier volgt een scherm opname van de Hive-query en de module [gegevens importeren][import-data] :

![Schermafbeelding van de Hive-query voor de module gegevens importeren](./media/hive-walkthrough/1eTYf52.png)

Omdat de gegevens van onze omlaag steekproef zich in de standaardcontainer, is de resulterende Hive-query op basis van Machine Learning is zeer eenvoudig. Het is alleen een **selecteren * van nyctaxidb.nyctaxi\_verkleind\_gegevens**.

De gegevensset kan nu worden gebruikt als startpunt voor het bouwen van Machine Learning-modellen.

### <a name="mlmodel"></a>Stel modellen samen in Machine Learning
U kunt nu doorgaan naar modellen bouwen en implementeren van modellen in [Machine Learning](https://studio.azureml.net). De gegevens zijn gereed voor gebruik bij het oplossen van problemen met de voorspelling eerder hebt geïdentificeerd:

- **Binaire classificatie**: om te voorspellen of een tip is betaald voor een reis.

  **Learner gebruikt:** Two-class logistieke regressie

  a. Voor dit probleem, het doel (of klasse) het label is **punt**. De oorspronkelijke omlaag steekproef gegevensset heeft enkele kolommen die lekken van het doel van deze classificatie-experiment zijn. In het bijzonder **tip\_klasse**, **tip\_bedrag**, en **totale\_bedrag** onthullen informatie over het doel dat label is niet beschikbaar bij het testen van de tijd. We verwijderen deze kolommen van overweging met behulp van de module [select columns in dataset][select-columns] .

  Het volgende diagram toont onze experiment te voorspellen of een tip voor een bepaalde reis is betaald:

  ![Diagram van experiment te voorspellen als tip is betaald](./media/hive-walkthrough/QGxRz5A.png)

  b. Ons doel label distributies zijn voor dit experiment, ongeveer 1:1.

   Het volgende diagram toont de distributie van tip klasse labels voor het probleem binaire classificatie:

  ![Diagram van de distributie van tip klasse labels](./media/hive-walkthrough/9mM4jlD.png)

    Als gevolg hiervan, krijgen we een gebied onder de curve (AUC) van 0.987, zoals wordt weergegeven in de volgende afbeelding:

  ![Grafiek van AUC waarde](./media/hive-walkthrough/8JDT0F8.png)

- **Multiklassen classificatie**: om te voorspellen van het bereik van de tip bedragen zijn betaald voor de reis door met behulp van de eerder gedefinieerde klassen.

  **Learner gebruikt:** Multiklassen logistieke regressie

  a. Voor dit probleem is het ons doel (of klasse) label **tip\_klasse**, die kan duren voordat een van vijf waarden (0,1,2,3,4). Zoals in het geval binaire classificatie hebben we een paar kolommen die doel lekken voor dit experiment. In het bijzonder **punt**, **tip\_bedrag**, en **totale\_bedrag** geven informatie over het doellabel dat momenteel niet beschikbaar testen van tijd. Deze kolommen worden verwijderd met behulp van de module [select columns in dataset][select-columns] .

  Het volgende diagram toont het experiment te voorspellen in welke opslaglocaties een tip is waarschijnlijk vallen. De opslaglocaties zijn: klasse 0: tip = $0, 1 van de klasse: tip > $0 en tip < = $5, klasse 2: tip > $5 en tip < = $10, klasse 3: tip > $10 en tip < = $20 en klasse 4: tip > $20.

  ![Diagram van experiment te voorspellen opslaglocatie voor de tip](./media/hive-walkthrough/5ztv0n0.png)

  Nu laten we zien hoe de distributie van de klasse werkelijke test eruitziet. Klasse 0 en 1 van de klasse heersen en de andere klassen zijn zeldzaam.

  ![Diagram van de distributie die test](./media/hive-walkthrough/Vy1FUKa.png)

  b. Voor dit experiment gebruiken we een verwarringsmatrix om te kijken naar de nauwkeurigheden voorspelling. Dit wordt hier weergegeven:

  ![Verwarringsmatrix](./media/hive-walkthrough/cxFmErM.png)

  Houd er rekening mee dat de klasse nauwkeurigheden op de gangbare klassen zijn heel goed, het model wordt niet goed werk 'learning' op de zeldzame klassen.

- **Regressie taak**: om te voorspellen van de hoeveelheid tip voor een reis betaald.

  **Learner gebruikt:** Boosted decision tree

  a. Voor dit probleem, het doel (of klasse) het label is **tip\_bedrag**. De doel-lekken in dit geval zijn: **punt**, **tip\_klasse**, en **totale\_bedrag**. Alle deze variabelen geven informatie over de tip-bedrag dat is doorgaans niet beschikbaar was vanaf het moment testen. Deze kolommen worden verwijderd met behulp van de module [select columns in dataset][select-columns] .

  Het volgende diagram toont het experiment te voorspellen van het bedrag van de opgegeven tip:

  ![Diagram van experiment te voorspellen hoeveelheid tip](./media/hive-walkthrough/11TZWgV.png)

  b. Problemen met regressie meten we de nauwkeurigheden van de voorspelling door te kijken de gekwadrateerde fout in de voorspellingen en de determinatiecoëfficiënt:

  ![Schermafbeelding van de statistieken voor voorspelling](./media/hive-walkthrough/Jat9mrz.png)

  De determinatiecoëfficiënt is 0.709, wordt die ongeveer 71 procent van de variantie van wat hier uitgelegd door de coëfficiënten model.

> [!IMPORTANT]
> Zie voor meer informatie over Machine Learning en hoe u toegang tot en gebruik deze [wat is Machine Learning](../studio/what-is-machine-learning.md). Bovendien de [Azure AI Gallery](https://gallery.cortanaintelligence.com/) meer informatie over een assortiment van experimenten en biedt een uitgebreide inleiding in het bereik van de mogelijkheden van Machine Learning.
> 
> 

## <a name="license-information"></a>Licentie-informatie
In dit voorbeeld scenario en de bijbehorende scripts worden gedeeld door Microsoft onder de MIT-licentie. Zie voor meer informatie de **LICENSE.txt** bestand in de map van de voorbeeldcode op GitHub.

## <a name="references"></a>Naslaginformatie
• [Andrés Monroy NYC Taxi Trips downloadpagina](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi reisgegevens door Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi en Limousine Commissie onderzoek en statistieken](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



