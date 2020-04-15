---
title: 'Zelfstudie: ETL-bewerkingen met interactieve query - Azure HDInsight'
description: Zelfstudie - Meer informatie over het extraheren van gegevens uit een ruwe CSV-gegevensset. Transformeer het met interactieve query op HDInsight. Laad de getransformeerde gegevens vervolgens in Azure SQL-database met Apache Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 7413a32fdddb579bad61c9cfe539be6aaeae9881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313747"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Zelfstudie: Gegevens extraheren, transformeren en laden met interactieve query in Azure HDInsight

In deze zelfstudie downloadt u een ruw CSV-gegevensbestand met openbaar beschikbare vluchtgegevens. Importeer deze in HDInsight-clusteropslag en transformeer de gegevens met interactieve query in Azure HDInsight. Als de gegevens zijn getransformeerd, laadt u die gegevens met behulp van [Apache Sqoop](https://sqoop.apache.org/) in een Azure SQL-database.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * De voorbeeldbestanden met vluchtgegevens downloaden
> * Gegevens uploaden naar een HDInsight-cluster
> * De gegevens transformeren met interactieve query
> * Een tabel maken in een Azure SQL-database
> * Sqoop gebruiken om gegevens te exporteren naar een Azure SQL-database

## <a name="prerequisites"></a>Vereisten

* Een cluster interactieve query's op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Interactieve query** voor **clustertype**.

* Een Azure SQL-database. U gebruikt een Azure SQL-database als doelgegevensopslag. Als u geen SQL-database hebt, raadpleegt u [Een Azure SQL-database maken in Azure Portal](/azure/sql-database/sql-database-single-database-get-started).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>De vluchtgegevens downloaden

1. Blader naar [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Schakel op de pagina alle velden uit en selecteer vervolgens de volgende waarden:

   | Name | Waarde |
   | --- | --- |
   | Filterjaar |2019 |
   | Filterperiode |Januari |
   | Velden |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Selecteer **Download**. U krijgt een ZIP-bestand met de gegevensvelden die u hebt geselecteerd.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Gegevens uploaden naar een HDInsight-cluster

Er zijn veel manieren om gegevens te uploaden naar de opslag die is gekoppeld aan een HDInsight-cluster. In dit gedeelte gebruikt u `scp` om gegevens te uploaden. Zie [Gegevens uploaden naar HDInsight](../hdinsight-upload-data.md) voor informatie over andere manieren om gegevens te uploaden.

1. Upload het .zip-bestand naar het HDInsight-clusterhoofdknooppunt. Bewerk de onderstaande `FILENAME` opdracht door te vervangen door `CLUSTERNAME` de naam van het .zip-bestand en met de naam van het HDInsight-cluster. Open vervolgens een opdrachtprompt, stel uw werkmap in op de bestandslocatie en voer de opdracht in.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Voer ja of nee in om door te gaan als daarom wordt gevraagd. De tekst is niet zichtbaar in het venster terwijl u typt.

2. Nadat het uploaden is voltooid, maakt u via SSH verbinding met het cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door te vervangen door de naam van het HDInsight-cluster. Voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Omgevingsvariabele instellen zodra een SSH-verbinding is vastgesteld. Vervangen `FILE_NAME` `SQL_SERVERNAME`, `SQL_DATABASE` `SQL_USER`, `SQL_PASWORD` , en met de juiste waarden. Voer vervolgens de opdracht in:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rits het .zip-bestand uit door onderstaande opdracht in te voeren:

    ```bash
    unzip $FILENAME.zip
    ```

5. Maak een map over HDInsight-opslag en kopieer het CSV-bestand naar de map door de onderstaande opdracht in te voeren:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Gegevens transformeren met behulp van een Hive-query

Er zijn veel manieren om een Hive-taak uit te voeren in een HDInsight-cluster. In dit gedeelte gebruikt u [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) om een Hive-taak uit te voeren. Zie [Hive in Apache HDInsight gebruiken](../hadoop/hdinsight-use-hive.md) voor meer informatie over de andere methoden voor het uitvoeren van een Hive-taak.

Als onderdeel van de Hive-taak, importeert u de gegevens uit het CSV-bestand naar een Hive-tabel met de naam **Delays**.

1. Gebruik de volgende opdracht om een nieuw bestand met de naam **flightdelays.hql**te maken en te bewerken via de SSH-prompt die u al hebt voor het HDInsight-cluster:

    ```bash
    nano flightdelays.hql
    ```

2. Gebruik de volgende tekst als de inhoud van dit bestand:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Als u het bestand wilt opslaan, drukt u op **Ctrl + X**en vervolgens op **y.**

4. Gebruik de volgende opdracht om Hive te starten en het bestand **flightdelays.hql** uit te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Als het script **flightdelays.hql** is voltooid, gebruikt u de volgende opdracht om een interactieve Beeline-sessie te openen:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Wanneer u de prompt `jdbc:hive2://localhost:10001/>` ziet, gebruikt u de volgende query om gegevens op te halen uit de geïmporteerde gegevens van vertraagde vluchten:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Met deze query haalt u een lijst op met plaatsen waar er vertragingen door het weer zijn ontstaan, samen met de gemiddelde vertragingstijd. Deze gegevens worden opgeslagen in `/tutorials/flightdelays/output`. Later worden de gegevens met Sqoop vanaf deze locatie gelezen en geëxporteerd naar Azure SQL Database.

7. Sluit Beeline af door `!quit` in te voeren bij de opdrachtprompt.

## <a name="create-a-sql-database-table"></a>Een SQL-databasetabel maken

Er zijn veel manieren om verbinding te maken met SQL Database en een tabel te maken. In de volgende stappen wordt [FreeTDS](http://www.freetds.org/) gebruikt vanuit het HDInsight-cluster.

1. Als u FreeTDS wilt installeren, gebruikt u de volgende opdracht van de geopende SSH-verbinding met het cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Nadat de installatie is voltooid, gebruikt u de volgende opdracht om verbinding te maken met de SQL Database-server.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    U ziet uitvoer die vergelijkbaar is met de volgende tekst:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Voer de volgende regels in bij de prompt `1>`:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Wanneer u de instructie `GO` invoert, worden de vorige instructies geëvalueerd. Met deze instructie wordt een tabel met de naam **vertragingen**met een geclusterde index met naam van de naam weergegeven.

    Gebruik de volgende query om te controleren of de tabel is gemaakt:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    De uitvoer lijkt op het volgende:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Voer `exit` als reactie op de prompt `1>` om het hulpprogramma tsql af te sluiten.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Gegevens met Apache Sqoop exporteren naar een SQL-database

In de vorige gedeelten hebt u de getransformeerde gegevens op `/tutorials/flightdelays/output` gekopieerd. In deze sectie gebruikt u Sqoop om de gegevens in `/tutorials/flightdelays/output` te exporteren naar de tabel die u hebt gemaakt in Azure SQL-database.

1. Controleer of Sqoop uw SQL-database kan zien door onderstaande opdracht in te voeren:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Met deze opdracht wordt een lijst met databases `delays` geretourneerd, inclusief de database waarin u de tabel eerder hebt gemaakt.

2. Exporteer gegevens `/tutorials/flightdelays/output` van `delays` naar de tabel door onderstaande opdracht in te voeren:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop maakt verbinding met `delays` de database die de `/tutorials/flightdelays/output` tabel `delays` bevat en exporteert gegevens uit de map naar de tabel.

3. Nadat de sqoop-opdracht is voltooid, gebruikt u het hulpprogramma tsql om verbinding te maken met de database door de onderstaande opdracht in te voeren:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Gebruik de volgende instructies om te controleren of de gegevens zijn geëxporteerd naar de tabel delays:

    ```sql
    SELECT * FROM delays
    GO
    ```

    U ziet als het goed is een lijst met gegevens in de tabel. De tabel bevat de plaatsnaam en de gemiddelde vertragingstijd voor vluchten van en naar die plaats.

    Typ `exit` om het hulpprogramma tsql af te sluiten.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een ruw CSV-gegevensbestand gebruikt, geïmporteerd in een HDInsight-clusteropslag en vervolgens de gegevens omgezet met interactieve query in Azure HDInsight.  Ga naar de volgende zelfstudie om meer te weten te komen over de Apache Hive Warehouse Connector.

> [!div class="nextstepaction"]
> [Integreer Apache Spark en Apache Hive met de Hive Warehouse Connector](./apache-hive-warehouse-connector.md)
