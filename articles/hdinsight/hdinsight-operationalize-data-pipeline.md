---
title: Een pijplijn voor gegevensanalyse operationaliseren - Azure
description: Een voorbeeldgegevenspijplijn instellen en uitvoeren die wordt geactiveerd door nieuwe gegevens en beknopte resultaten oplevert.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: 16c7af4d66bd550eb4a286de7c86c436b1fe10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922669"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Een pijplijn voor gegevensanalyse operationeel maken

*Datapipelines* zijn onder meer veel data-analyseoplossingen. Zoals de naam al doet vermoeden, neemt een gegevenspijplijn ruwe gegevens op, reinigt en hervormt deze indien nodig opnieuw en voert deze doorgaans berekeningen of aggregaties uit voordat de verwerkte gegevens worden opgeslagen. De verwerkte gegevens worden verbruikt door clients, rapporten of API's. Een gegevenspijplijn moet herhaalbare resultaten opleveren, zowel volgens een planning als wanneer deze wordt geactiveerd door nieuwe gegevens.

In dit artikel wordt beschreven hoe u uw gegevenspijplijnen operationaliseren voor herhaalbaarheid, met Oozie die wordt uitgevoerd op HDInsight Hadoop-clusters. Het voorbeeldscenario leidt u door een gegevenspijplijn die gegevens over vluchttijdreeksen van luchtvaartmaatschappijen voorbereidt en verwerkt.

In het volgende scenario zijn de invoergegevens een plat bestand met een batch vluchtgegevens gedurende een maand. Deze vluchtgegevens omvatten informatie zoals de herkomst en bestemmingluchthaven, de gevlogen mijlen, de vertrek- en aankomsttijden, enzovoort. Het doel met deze pijplijn is om de dagelijkse prestaties van luchtvaartmaatschappijen samen te vatten, waarbij elke luchtvaartmaatschappij één rij heeft voor elke dag met de gemiddelde vertragingen voor vertrek en aankomst in minuten en de totale gevlogen mijlen die dag.

| YEAR | MONTH | DAY_OF_MONTH | Vervoerder |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | Aa | 10.142229 | 7.862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9.435449 | 5.482143 | 572289 |
| 2017 | 1 | 3 | Dl | 6.935409 | -2.1893024 | 1909696 |

De voorbeeldpijplijn wacht tot de vluchtgegevens van een nieuwe periode binnenkomen en worden die gedetailleerde vluchtgegevens opgeslagen in uw Apache Hive-gegevensmagazijn voor langetermijnanalyses. De pijplijn maakt ook een veel kleinere gegevensset die alleen de dagelijkse vluchtgegevens samenvat. Deze dagelijkse vluchtoverzichtsgegevens worden naar een SQL-database verzonden om rapporten te verstrekken, zoals voor een website.

In het volgende diagram wordt de voorbeeldpijplijn geïllustreerd.

![OVERZICHT van hdi-vluchtvoorbeeldgegevenspijplijn](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Overzicht apache Oozie-oplossing

Deze pijplijn maakt gebruik van Apache Oozie die wordt uitgevoerd op een HDInsight Hadoop-cluster.

Oozie beschrijft de pijplijnen in termen van *acties*, *workflows*en *coördinatoren*. Acties bepalen het werkelijke werk dat moet worden uitgevoerd, zoals het uitvoeren van een Hive-query. Werkstromen definiëren de volgorde van acties. Coördinatoren bepalen de planning voor wanneer de werkstroom wordt uitgevoerd. Coördinatoren kunnen ook wachten op de beschikbaarheid van nieuwe gegevens voordat ze een exemplaar van de workflow starten.

Het volgende diagram toont het ontwerp op hoog niveau van dit voorbeeld Oozie-pijplijn.

![Oozie Vlucht voorbeeld Gegevenspijplijn](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Azure-bronnen inrichten

Voor deze pijplijn is een Azure SQL Database en een HDInsight Hadoop-cluster op dezelfde locatie vereist. De Azure SQL Database slaat zowel de overzichtsgegevens op die door de pijplijn zijn geproduceerd als het Oozie-metagegevensarchief.

### <a name="provision-azure-sql-database"></a>Azure SQL-database inrichten

1. Maak een Azure SQL Database. Zie [Een Azure SQL-database maken in de Azure-portal](../sql-database/sql-database-single-database-get-started.md).

1. Als u ervoor wilt zorgen dat uw HDInsight-cluster toegang heeft tot de verbonden Azure SQL Database, configureert u Azure SQL Database-firewallregels zodat Azure-services en -bronnen toegang krijgen tot de server. U deze optie inschakelen in de Azure-portal door **Serverfirewall instellen**te selecteren en **ONDER** **Azure-services en -bronnen toestaan om toegang te krijgen tot deze server** voor de Azure SQL Database-server of -database. Zie [IP-firewallregels maken en beheren](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)voor meer informatie.

1. Gebruik [queryeditor](../sql-database/sql-database-single-database-get-started.md#query-the-database) om de volgende SQL-instructies uit te voeren om de `dailyflights` tabel te maken die de samengevatte gegevens van elke uitvoering van de pijplijn opslaat.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Uw Azure SQL Database is nu klaar.

### <a name="provision-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster inrichten

Maak een Apache Hadoop-cluster met een aangepaste metastore. Zorg ervoor dat u tijdens het maken van het cluster vanuit de portal via het tabblad **Opslag** uw SQL-database selecteert onder **Metastore-instellingen.** Zie Een aangepaste metastore selecteren tijdens het maken van een metastore voor meer informatie over het selecteren van een [metastore.](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) Zie Aan de slag [met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over het maken van clusteren.

## <a name="verify-ssh-tunneling-set-up"></a>SSH-tunneling controleren

Als u de Oozie-webconsole wilt gebruiken om de status van uw coördinator en werkstroominstanties weer te geven, stelt u een SSH-tunnel in voor uw HDInsight-cluster. Zie [SSH Tunnel](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

> [!NOTE]  
> U Chrome ook gebruiken met de [Foxy Proxy-extensie](https://getfoxyproxy.org/) om door de webbronnen van uw cluster in de SSH-tunnel te bladeren. Configureer het naar proxy `localhost` alle aanvragen via de host op de poort van de tunnel 9876. Deze aanpak is compatibel met het Windows-subsysteem voor Linux, ook bekend als Bash op Windows 10.

1. Voer de volgende opdracht uit om een SSH-tunnel naar uw cluster te openen, waar `CLUSTERNAME` is de naam van uw cluster:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Controleer of de tunnel operationeel is door op uw hoofdknooppunt naar Ambari te navigeren door te bladeren naar:

    `http://headnodehost:8080`

1. Als u de **Oozie-webconsole** vanuit Ambari wilt openen, navigeert u naar **Oozie** > **Quick Links** > [Active server] > **Oozie Web UI.**

## <a name="configure-hive"></a>Hive configureren

### <a name="upload-data"></a>Gegevens uploaden

1. Download een voorbeeld CSV-bestand dat vluchtgegevens bevat voor een maand. Download het `2017-01-FlightData.zip` ZIP-bestand uit de [HDInsight GitHub-repository](https://github.com/hdinsight/hdinsight-dev-guide) en rits het uit naar het CSV-bestand. `2017-01-FlightData.csv`

1. Kopieer dit CSV-bestand naar het Azure Storage-account dat is `/example/data/flights` gekoppeld aan uw HDInsight-cluster en plaats het in de map.

    1. Gebruik SCP om de bestanden van uw lokale machine te kopiëren naar de lokale opslag van uw HDInsight-clusterkopknooppunt.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Gebruik [de ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw cluster te vervangen en voer de opdracht in:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Gebruik de opdracht HDFS om het bestand te kopiëren van de lokale opslag van uw hoofdknooppunt naar Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Tabellen maken

De voorbeeldgegevens zijn nu beschikbaar. De pijplijn vereist echter twee Hive-tabellen voor verwerking,`rawFlights`een voor de binnenkomende gegevens ( ) en een voor de samengevatte gegevens (`flights`). Maak deze tabellen als volgt in Ambari.

1. Log in bij Ambari `http://headnodehost:8080`door te navigeren naar .

2. Selecteer **Hive**in de lijst met services.

    ![Lijst met Apache Ambari-services met de selectie van Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Selecteer **Ga naar weergave** naast het Hive View 2.0-label.

    ![Ambari Apache Hive overzichtslijst](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Plak in het gebied van querytekst `rawFlights` de volgende instructies om de tabel te maken. De `rawFlights` tabel bevat een schema-on-read voor `/example/data/flights` de CSV-bestanden in de map in Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Selecteer **Uitvoeren** om de tabel te maken.

    ![hdi ambari diensten hive query](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Als u `flights` de tabel wilt maken, vervangt u de tekst in het querytekstgebied door de volgende instructies. De `flights` tabel is een door Hive beheerde tabel die gegevens partities die erin zijn geladen per jaar, maand en maand. Deze tabel bevat alle historische vluchtgegevens, met de laagste granulariteit aanwezig in de brongegevens van één rij per vlucht.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Selecteer **Uitvoeren** om de tabel te maken.

## <a name="create-the-oozie-workflow"></a>De Oozie-werkstroom maken

Pijplijnen verwerken gegevens doorgaans in batches met een bepaald tijdsinterval. In dit geval verwerkt de pijplijn de vluchtgegevens dagelijks. Deze aanpak maakt het mogelijk voor de input CSV-bestanden te komen dagelijks, wekelijks, maandelijks, of jaarlijks.

De voorbeeldworkflow verwerkt de vluchtgegevens van dag tot dag in drie belangrijke stappen:

1. Voer een Hive-query uit om de gegevens voor het datumbereik van `rawFlights` die dag uit `flights` het bronCSV-bestand te halen dat door de tabel wordt weergegeven en de gegevens in de tabel in te voegen.
2. Voer een Hive-query uit om dynamisch een faseringstabel te maken in Hive voor de dag, die een kopie bevat van de vluchtgegevens die zijn samengevat per dag en luchtvaartmaatschappij.
3. Gebruik Apache Sqoop om alle gegevens uit de dagelijkse `dailyflights` faseringstabel in Hive te kopiëren naar de doeltabel in Azure SQL Database. Sqoop leest de bronrijen van de gegevens achter de Hive-tabel die zich in Azure Storage bevinden en laadt deze in SQL-database met behulp van een JDBC-verbinding.

Deze drie stappen worden gecoördineerd door een Oozie workflow.

1. Maak vanaf uw lokale werkstation een bestand met de naam `job.properties`. Gebruik de tekst hieronder als startinhoud voor het bestand.
Werk vervolgens de waarden voor uw specifieke omgeving bij. De tabel onder de tekst geeft een overzicht van elk van de eigenschappen en geeft aan waar u de waarden voor uw eigen omgeving vinden.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Eigenschap | Waardebron |
    | --- | --- |
    | nameNode | Het volledige pad naar de Azure Storage Container die is gekoppeld aan uw HDInsight-cluster. |
    | jobTracker | De interne hostnaam van het YARN-hoofdknooppunt van uw actieve cluster. Selecteer op de startpagina van Ambari GAREN in de lijst met services en kies Active Resource Manager. De hostname URI wordt boven aan de pagina weergegeven. Sluit de poort 8050 toe. |
    | queueName | De naam van de GARENwachtrij die wordt gebruikt bij het plannen van de Hive-acties. Laat als standaard. |
    | oozie.use.system.libpath | Laat als waar. |
    | appBase | Het pad naar de submap in Azure Storage waar u de Oozie-werkstroom implementeert en ondersteunende bestanden. |
    | oozie.wf.application.path | De locatie van de `workflow.xml` Oozie-werkstroom die moet worden uitgevoerd. |
    | hiveScriptLoadPartition | Het pad in Azure Storage `hive-load-flights-partition.hql`naar het Hive-querybestand . |
    | hiveScript'sMaakDailyTable | Het pad in Azure Storage `hive-create-daily-summary-table.hql`naar het Hive-querybestand . |
    | hiveDailyTableName | De dynamisch gegenereerde naam die u wilt gebruiken voor de faseringstabel. |
    | hiveDataFolder | Het pad in Azure Storage naar de gegevens van de faseringstabel. |
    | sqlDatabaseConnectionString | De jdbc-syntaxisverbindingstekenreeks met uw Azure SQL-database. |
    | sqlDatabaseTableName | De naam van de tabel in Azure SQL Database waarin overzichtsrijen worden ingevoegd. Vertrek `dailyflights`als . |
    | jaar | De jaarcomponent van de dag waarvoor vluchtsamenvattingen worden berekend. Ga weg zoals het is. |
    | maand | De maandcomponent van de dag waarvoor vluchtsamenvattingen worden berekend. Ga weg zoals het is. |
    | day | De dag van de maand component van de dag waarvoor vlucht samenvattingen worden berekend. Ga weg zoals het is. |

1. Maak vanaf uw lokale werkstation een bestand met de naam `hive-load-flights-partition.hql`. Gebruik de onderstaande code als de inhoud voor het bestand.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie-variabelen gebruiken `${variableName}`de syntaxis . Deze variabelen zijn ingesteld `job.properties` in het bestand. Oozie vervangt de werkelijke waarden bij runtime.

1. Maak vanaf uw lokale werkstation een bestand met de naam `hive-create-daily-summary-table.hql`. Gebruik de onderstaande code als de inhoud voor het bestand.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Met deze query wordt een faseringstabel opgesteld die alleen de samengevatte gegevens voor één dag opslaat, kennis neemt van de SELECT-instructie die de gemiddelde vertragingen en het totaal van de afstand per vervoerder per dag berekent. De gegevens die in deze tabel zijn ingevoegd, zijn opgeslagen op een bekende locatie (het pad dat wordt aangegeven door de variabele hiveDataFolder), zodat deze in de volgende stap als bron voor Sqoop kunnen worden gebruikt.

1. Maak vanaf uw lokale werkstation een bestand met de naam `workflow.xml`. Gebruik de onderstaande code als de inhoud voor het bestand. Deze bovenstaande stappen worden uitgedrukt als afzonderlijke acties in oozie-werkstroombestand.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

De twee Hive-query's worden geopend via hun pad in Azure `job.properties` Storage en de resterende variabele waarden worden door het bestand geleverd. In dit bestand wordt de werkstroom geconfigureerd voor de datum 3 januari 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>De Oozie-werkstroom implementeren en uitvoeren

Gebruik SCP vanuit uw bash-sessie om`workflow.xml`uw Oozie-werkstroom ( ), de Hive-query's (en`hive-load-flights-partition.hql` `hive-create-daily-summary-table.hql`) en de taakconfiguratie (`job.properties`) te implementeren.  In Oozie kan `job.properties` alleen het bestand bestaan op de lokale opslag van de headnode. Alle andere bestanden moeten worden opgeslagen in HDFS, in dit geval Azure Storage. De Sqoop-actie die door de werkstroom wordt gebruikt, is afhankelijk van een JDBC-stuurprogramma voor de communicatie met uw SQL-database, die moet worden gekopieerd van het hoofdknooppunt naar HDFS.

1. Maak `load_flights_by_day` de submap onder het pad van de gebruiker in de lokale opslag van het hoofdknooppunt. Voer vanuit uw geopende ssh-sessie de volgende opdracht uit:

    ```bash
    mkdir load_flights_by_day
    ```

1. Kopieer alle bestanden in de `workflow.xml` `job.properties` huidige map (de en bestanden) naar de `load_flights_by_day` submap. Voer vanaf uw lokale werkstation de volgende opdracht uit:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Werkstroombestanden naar HDFS kopiëren. Voer vanuit uw geopende ssh-sessie de volgende opdrachten uit:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Kopieer `mssql-jdbc-7.0.0.jre8.jar` vanuit het lokale hoofdknooppunt naar de werkstroommap in HDFS. Herzie de opdracht indien nodig als uw cluster een ander jar-bestand bevat. Herzien `workflow.xml` indien nodig om een ander potbestand weer te geven. Voer vanuit uw geopende ssh-sessie de volgende opdracht uit:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Voer de werkstroom uit. Voer vanuit uw geopende ssh-sessie de volgende opdracht uit:

    ```bash
    oozie job -config job.properties -run
    ```

1. Let op de status met behulp van de Oozie Web Console. Selecteer **Oozie,** **Snelle koppelingen**en vervolgens **Oozie Web Console**vanuit Ambari . Selecteer alle **taken**onder het tabblad **Werkstroomtaken** .

    ![hdi oozie webconsole workflows](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. Wanneer de status is GESLAAGD, stelt u de SQL Database-tabel op om de ingevoegde rijen weer te geven. Navigeer met de Azure-portal naar het deelvenster voor uw SQL-database, selecteer **Extra**en open de **queryeditor**.

        SELECT * FROM dailyflights

Nu de werkstroom wordt uitgevoerd voor de enkele testdag, u deze werkstroom inpakken met een coördinator die de werkstroom zo plant dat deze dagelijks wordt uitgevoerd.

## <a name="run-the-workflow-with-a-coordinator"></a>De werkstroom uitvoeren met een coördinator

Als u deze werkstroom zo wilt plannen dat deze dagelijks (of alle dagen in een datumbereik) wordt uitgevoerd, u een coördinator gebruiken. Een coördinator wordt gedefinieerd door `coordinator.xml`een XML-bestand, bijvoorbeeld:

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Zoals u zien, is de meerderheid van de coördinator alleen configuratiegegevens doorgeven aan de werkstroominstantie. Er zijn echter een paar belangrijke items om uit te roepen.

* Punt 1: `start` `end` De en `coordinator-app` de kenmerken van het element zelf bepalen het tijdsinterval waarover de coördinator wordt uitgevoerd.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    Een coördinator is verantwoordelijk `start` voor `end` het plannen van acties binnen `frequency` het en datumbereik, afhankelijk van het interval dat door het kenmerk is opgegeven. Elke geplande actie voert op zijn beurt de werkstroom uit zoals geconfigureerd. In de bovenstaande coördinatordefinitie is de coördinator geconfigureerd om acties uit te voeren van 1 januari 2017 tot 5 januari 2017. De frequentie wordt ingesteld op één dag door `${coord:days(1)}`de [frequentieexpressie Oozie Expression Language](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) . Dit resulteert in het plannen van een actie (en dus de workflow) één keer per dag. Voor datumbereiken die in het verleden zijn, zoals in dit voorbeeld, wordt de actie gepland om zonder vertraging uit te voeren. Het begin van de datum waarop een actie wordt uitgevoerd, wordt de *nominale tijd*genoemd . Als u bijvoorbeeld de gegevens voor 1 januari 2017 wilt verwerken, plant de coördinator actie met een nominale tijd van 2017-01-01T00:00:00 GMT.

* Punt 2: Binnen het datumbereik `dataset` van de werkstroom geeft het element aan waar u in HDFS moet zoeken naar de gegevens voor een bepaald datumbereik en configureert het hoe Oozie bepaalt of de gegevens nog beschikbaar zijn voor verwerking.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Het pad naar de gegevens in HDFS is dynamisch `uri-template` gebouwd volgens de expressie in het element. In deze coördinator wordt ook een frequentie van één dag gebruikt met de dataset. Terwijl de begin- en einddatum op het coördinatorelement bepalen wanneer `initial-instance` de `frequency` acties zijn gepland (en hun nominale tijden `uri-template`worden gedefinieerd), bepalen de en op de gegevensset de berekening van de datum die wordt gebruikt bij het bouwen van de . Stel in dit geval de eerste instantie in op één dag voor de start van de coördinator om ervoor te zorgen dat de gegevens van de eerste dag (1/1/2017) worden opgehaald. De datumberekening van de gegevensset `initial-instance` wordt naar voren geschoven vanaf de waarde van (12/31/2016) die vordert in stappen van de frequentie van gegevenssets (één dag) totdat de meest recente datum wordt gevonden die niet voldoet aan de nominale tijd die is ingesteld door de coördinator (2017-01-01T00:00:00 GMT voor de eerste actie).

    Het `done-flag` lege element geeft aan dat wanneer Oozie controleert op de aanwezigheid van invoergegevens op het afgesproken tijdstip, Oozie gegevens bepaalt of beschikbaar is door aanwezigheid van een directory of bestand. In dit geval is het de aanwezigheid van een csv-bestand. Als er een csv-bestand aanwezig is, gaat Oozie ervan uit dat de gegevens klaar zijn en start een werkstroominstantie om het bestand te verwerken. Als er geen csv-bestand aanwezig is, gaat Oozie ervan uit dat de gegevens nog niet klaar zijn en dat de uitvoering van de werkstroom in een wachtstand gaat.

* Punt 3: `data-in` Het element geeft de specifieke tijdstempel op die `uri-template` moet worden gebruikt als de nominale tijd bij het vervangen van de waarden voor de bijbehorende gegevensset.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    Stel in dit geval de `${coord:current(0)}`instantie in op de expressie , wat zich vertaalt in het gebruik van de nominale tijd van de actie zoals oorspronkelijk gepland door de coördinator. Met andere woorden, wanneer de coördinator de te lopen actie plant met een nominale tijd van 01/01/2017, dan is 01/01/2017 wat wordt gebruikt om de variabelen JAAR (2017) en MONTH (01) in de URI-sjabloon te vervangen. Zodra de URI-sjabloon voor dit exemplaar is berekend, controleert Oozie of de verwachte map of bestand beschikbaar is en plant de volgende uitvoering van de werkstroom dienovereenkomstig.

De drie voorgaande punten leveren samen een situatie op waarin de coördinator de verwerking van de brongegevens van dag tot dag plant.

* Punt 1: De coördinator begint met een nominale datum 2017-01-01.

* Punt 2: Oozie zoekt `sourceDataFolder/2017-01-FlightData.csv`naar gegevens die beschikbaar zijn in .

* Punt 3: Wanneer Oozie dat bestand vindt, plant het een instantie van de werkstroom die de gegevens voor 2017-01-01 verwerkt. Oozie gaat dan verder met de verwerking voor 2017-01-02. Deze evaluatie wordt herhaald tot maar niet inclusief 2017-01-05.

Net als bij werkstromen wordt de configuratie `job.properties` van een coördinator gedefinieerd in een bestand, dat een superset heeft van de instellingen die door de werkstroom worden gebruikt.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

De enige nieuwe eigenschappen `job.properties` die in dit bestand zijn geïntroduceerd, zijn:

| Eigenschap | Waardebron |
| --- | --- |
| oozie.coord.application.path | Geeft de locatie `coordinator.xml` aan van het bestand met de Oozie-coördinator die moet worden uitgevoerd. |
| hiveDailyTableNameVoorvoegsel | Het voorvoegsel dat wordt gebruikt bij het dynamisch maken van de tabelnaam van de faseringstabel. |
| hiveDataFolderVoorvoegsel | Het voorvoegsel van het pad waar alle faseringstabellen worden opgeslagen. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Oozie-coördinator implementeren en uitvoeren

Als u de pijplijn met een coördinator wilt uitvoeren, gaat u op dezelfde manier te werk als voor de werkstroom, behalve dat u vanuit een map één niveau boven de map werkt die uw werkstroom bevat. Deze mapconventie scheidt de coördinatoren van de werkstromen op schijf, zodat u één coördinator koppelen aan verschillende onderliggende werkstromen.

1. Gebruik SCP vanaf uw lokale machine om de coördinatorbestanden te kopiëren tot de lokale opslag van het hoofdknooppunt van uw cluster.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH in je hoofdknooppunt.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Kopieer de coördinatorbestanden naar HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Voer de coördinator uit.

    ```bash
    oozie job -config job.properties -run
    ```

5. Controleer de status met de Oozie-webconsole, selecteer dit keer het tabblad **Afdruktaken** en vervolgens **Alle taken**.

    ![Oozie Web Console Coördinator Vacatures](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Selecteer een coördinatorinstantie om de lijst met geplande acties weer te geven. In dit geval ziet u vier acties met nominale tijden in het bereik van 1/1/2017 tot 1/4/2017.

    ![Oozie Web Console Coördinator Job](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Elke actie in deze lijst komt overeen met een instantie van de werkstroom die gegevens van één dag verwerkt, waarbij het begin van die dag wordt aangegeven door de nominale tijd.

## <a name="next-steps"></a>Volgende stappen

[Apache Oozie Documentatie](https://oozie.apache.org/docs/4.2.0/index.html)
