---
title: Hadoop Oozie-werk stromen gebruiken in azure HDInsight op basis van Linux
description: Gebruik Hadoop Oozie in HDInsight op basis van Linux. Meer informatie over het definiëren van een Oozie-werk stroom en het verzenden van een Oozie-taak.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c24370c91c7164786503cdd8e3c44de60abc8370
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122501"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Apache Oozie met Apache Hadoop gebruiken om een werk stroom te definiëren en uit te voeren op Azure HDInsight op basis van Linux

Meer informatie over het gebruik van Apache Oozie met Apache Hadoop op Azure HDInsight. Oozie is een werk stroom-en coördinatie systeem waarmee Hadoop-taken worden beheerd. Oozie is geïntegreerd met de Hadoop-stack en biedt ondersteuning voor de volgende taken:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

U kunt Oozie ook gebruiken om taken te plannen die specifiek zijn voor een systeem, zoals Java-Program ma's of shell scripts.

> [!NOTE]  
> Een andere optie voor het definiëren van werk stromen met HDInsight is het gebruik van Azure Data Factory. Zie voor meer informatie over Data Factory [Apache Pig en Apache Hive met Data Factory gebruiken][azure-data-factory-pig-hive]. Als u Oozie wilt gebruiken in clusters met Enterprise Security Package raadpleegt u [Apache Oozie in HDInsight Hadoop-clusters uitvoeren met Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).


## <a name="prerequisites"></a>Vereisten

* **Een Hadoop-cluster in HDInsight**. Zie aan de [slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Een SSH-client**. Zie [verbinding maken met HDInsight (Apache Hadoop) met SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Een Azure SQL database**.  Zie [een Azure-SQL database maken in de Azure Portal](../sql-database/sql-database-get-started.md).  In dit artikel wordt gebruikgemaakt `oozietest`van een Data Base met de naam.

* Het [URI-schema](./hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit is `wasb://` voor Azure Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data Lake Storage Gen2, wordt `wasbs://` de URI of `abfss://`, respectievelijk, weer geven, [beveiligde overdracht](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Voorbeeld werk stroom

De werk stroom die in dit document wordt gebruikt, bevat twee acties. Acties zijn definities voor taken, zoals het uitvoeren van Hive, Sqoop, MapReduce of andere processen:

![HDInsight oozie-werk stroom diagram](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Een Hive-actie voert een HiveQL-script uit om records `hivesampletable` op te halen uit de die zijn opgenomen in HDInsight. In elke rij met gegevens wordt een bezoek van een specifiek mobiel apparaat beschreven. De record indeling wordt weer gegeven zoals in de volgende tekst:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Het Hive-script dat in dit document wordt gebruikt, telt de totale bezoeken voor elk platform, zoals Android of iPhone, en slaat de tellingen op in een nieuwe Hive-tabel.

    Zie [Apache Hive gebruiken met HDInsight][hdinsight-use-hive]voor meer informatie over Hive.

2. Met een Sqoop-actie wordt de inhoud van de nieuwe Hive-tabel geëxporteerd naar een tabel die is gemaakt in Azure SQL Database. Zie [Apache Sqoop gebruiken met HDInsight][hdinsight-use-sqoop]voor meer informatie over Sqoop.

> [!NOTE]  
> Zie [Wat is er nieuw in de Hadoop-cluster versies van hdinsight][hdinsight-versions]voor ondersteunde Oozie-versies op hdinsight-clusters.

## <a name="create-the-working-directory"></a>De werkmap maken

Oozie verwacht u dat alle resources die vereist zijn voor een taak, worden opgeslagen in dezelfde map. In dit voor `wasbs:///tutorials/useoozie`beeld wordt gebruikt. Voer de volgende stappen uit om deze map te maken:

1. Bewerk de code hieronder om te `sshuser` vervangen door de SSH-gebruikers naam voor het cluster en `clustername` Vervang door de naam van het cluster.  Voer vervolgens de code in om verbinding te maken met het HDInsight-cluster met [behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. Als u de map wilt maken, gebruikt u de volgende opdracht:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > De `-p` para meter zorgt ervoor dat alle mappen in het pad worden gemaakt. De `data` map wordt gebruikt voor het opslaan van de gegevens die `useooziewf.hql` worden gebruikt door het script.

3. Bewerk de code hieronder om door `username` uw SSH-gebruikers naam te vervangen.  Gebruik de volgende opdracht om ervoor te zorgen dat Oozie uw gebruikers account kan imiteren:

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > U kunt fouten negeren die aangeven dat de gebruiker al lid is van de `users` groep.

## <a name="add-a-database-driver"></a>Een database stuur programma toevoegen

Omdat deze werk stroom gebruikmaakt van Sqoop voor het exporteren van gegevens naar de SQL database, moet u een kopie van het JDBC-stuur programma opgeven dat wordt gebruikt om te communiceren met de SQL database. Als u het JDBC-stuur programma wilt kopiëren naar de werkmap, gebruikt u de volgende opdracht uit de SSH-sessie:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Controleer het huidige JDBC-stuur programma dat `/usr/share/java/`bestaat op.

Als uw werk stroom andere resources heeft gebruikt, zoals een jar die een MapReduce-toepassing bevat, moet u deze resources ook toevoegen.

## <a name="define-the-hive-query"></a>De Hive-query definiëren

Gebruik de volgende stappen om een HiveQL-script (component Query Language) te maken waarmee een query wordt gedefinieerd. Verderop in dit document gebruikt u de query in een Oozie-werk stroom.

1. Gebruik bij de SSH-verbinding de volgende opdracht om een bestand te maken `useooziewf.hql`met de naam:

    ```bash
    nano useooziewf.hql
    ```

3. Nadat de GNU nano editor is geopend, gebruikt u de volgende query als de inhoud van het bestand:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Er worden twee variabelen gebruikt in het script:

   * `${hiveTableName}`: Bevat de naam van de tabel die moet worden gemaakt.

   * `${hiveDataFolder}`: Bevat de locatie voor het opslaan van de gegevens bestanden voor de tabel.

     Het definitie bestand van de werk stroom, werk stroom. XML in dit artikel, geeft deze waarden door aan dit HiveQL-script tijdens runtime.

4. Als u het bestand wilt opslaan, selecteert u CTRL + `Y`X, voert u in en selecteert u vervolgens **Enter**.  

5. Gebruik de volgende opdracht om te `useooziewf.hql` kopiëren `wasbs:///tutorials/useoozie/useooziewf.hql`naar:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Met deze opdracht wordt `useooziewf.hql` het bestand opgeslagen in de met HDFS compatibele opslag voor het cluster.

## <a name="define-the-workflow"></a>De werk stroom definiëren

Oozie-werk stroom definities worden geschreven in Hadoop process Definition Language (hPDL). Dit is een XML-proces definitie taal. Gebruik de volgende stappen om de werk stroom te definiëren:

1. Gebruik de volgende instructie voor het maken en bewerken van een nieuw bestand:

    ```bash
    nano workflow.xml
    ```

2. Wanneer de nano editor wordt geopend, voert u de volgende XML in als de bestands inhoud:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
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
            <arg>${hiveDataFolder}</arg>
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

    Er zijn twee acties gedefinieerd in de werk stroom:

   * `RunHiveScript`: Deze actie is de actie starten en voert het `useooziewf.hql` Hive-script uit.

   * `RunSqoopExport`: Met deze actie exporteert u de gegevens die zijn gemaakt van het Hive-script naar een SQL database met behulp van Sqoop. Deze actie wordt alleen uitgevoerd als `RunHiveScript` de actie is geslaagd.

     De werk stroom heeft verschillende vermeldingen, zoals `${jobTracker}`. Deze vermeldingen worden vervangen door de waarden die u in de taak definitie gebruikt. Verderop in dit document maakt u de taak definitie.

     Noteer ook de `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` vermelding in de sectie Sqoop. Met deze vermelding krijgt Oozie om dit archief beschikbaar te maken voor Sqoop wanneer deze actie wordt uitgevoerd.

3. Als u het bestand wilt opslaan, selecteert u CTRL + `Y`X, voert u in en selecteert u vervolgens **Enter**.  

4. Gebruik de volgende opdracht om het `workflow.xml` bestand te kopiëren naar: `/tutorials/useoozie/workflow.xml`

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Een tabel maken

> [!NOTE]  
> Er zijn veel manieren om verbinding te maken met SQL Database om een tabel op te stellen. In de volgende stappen wordt [FreeTDS](https://www.freetds.org/) gebruikt vanuit het HDInsight-cluster.

1. Gebruik de volgende opdracht om FreeTDS te installeren op het HDInsight-cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Bewerk de code hieronder om te `<serverName>` vervangen door de naam van uw Azure SQL `<sqlLogin>` -server en met de aanmelding bij Azure SQL Server.  Voer de opdracht in om verbinding te maken met de vereiste SQL database.  Voer het wacht woord in bij de prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    U ontvangt uitvoer zoals de volgende tekst:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. Voer de volgende regels in bij de prompt `1>`:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Wanneer u de instructie `GO` invoert, worden de vorige instructies geëvalueerd. Met deze instructies maakt u een tabel `mobiledata`met de naam, die wordt gebruikt door de werk stroom.

    Gebruik de volgende opdrachten om te controleren of de tabel is gemaakt:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    U ziet uitvoer als de volgende tekst:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Sluit het hulp programma TSQL af `exit` door op `1>` de vraag te typen.

## <a name="create-the-job-definition"></a>De taak definitie maken

De taak definitie beschrijft waar u de werk stroom. XML kunt vinden. Ook wordt beschreven waar u andere bestanden kunt vinden die worden gebruikt door de werk `useooziewf.hql`stroom, zoals. Daarnaast definieert het de waarden voor eigenschappen die worden gebruikt in de werk stroom en de gekoppelde bestanden.

1. Gebruik de volgende opdracht om het volledige adres van de standaard opslag te verkrijgen. Dit adres wordt gebruikt in het configuratie bestand dat u in de volgende stap maakt.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Met deze opdracht wordt informatie geretourneerd, zoals de volgende XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Als het HDInsight-cluster Azure Storage als standaard opslag gebruikt, begint `<value>` de inhoud van het `wasbs://`element met. Als Azure Data Lake Storage Gen1 in plaats daarvan wordt gebruikt, begint `adl://`dit met. Als Azure Data Lake Storage Gen2 wordt gebruikt, begint dit met `abfs://`.

    Sla de inhoud van het `<value>` element op, zoals dit wordt gebruikt in de volgende stappen.

2. Bewerk de XML hieronder als volgt:

    |Waarde van tijdelijke aanduiding| Waarde vervangen|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Waarde ontvangen van stap 1.|
    |beheerder| Uw aanmeldings naam voor het HDInsight-cluster als u geen beheerder bent.|
    |Servernaam| Naam van Azure SQL database-server.|
    |sqlLogin| Aanmelding bij Azure SQL database server.|
    |sqlPassword| Aanmeldings wachtwoord voor de Azure SQL database-server.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    De meeste informatie in dit bestand wordt gebruikt om de waarden in te vullen die worden gebruikt in de werk stroom. XML-of ooziewf. `${nameNode}`HQL-bestanden, zoals.  Als het pad een `wasbs` pad is, moet u het volledige pad gebruiken. Verkort het niet naar alleen `wasbs:///`. De `oozie.wf.application.path` vermelding definieert waar het bestand workflow. XML moet worden gevonden. Dit bestand bevat de werk stroom die door deze taak is uitgevoerd.

3. Als u de configuratie van de Oozie-taak definitie wilt maken, gebruikt u de volgende opdracht:

    ```bash
    nano job.xml
    ```

4. Nadat de nano editor is geopend, plakt u de bewerkte XML als de inhoud van het bestand.

5. Als u het bestand wilt opslaan, selecteert u CTRL + `Y`X, voert u in en selecteert u vervolgens **Enter**.

## <a name="submit-and-manage-the-job"></a>De taak verzenden en beheren

De volgende stappen gebruiken de Oozie-opdracht voor het indienen en beheren van Oozie-werk stromen in het cluster. De Oozie-opdracht is een vriendelijke interface over de [Oozie-rest API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Wanneer u de opdracht Oozie gebruikt, moet u de FQDN voor het hoofd knooppunt van HDInsight gebruiken. Deze FQDN is alleen toegankelijk vanuit het cluster, of als het cluster zich op een virtueel Azure-netwerk bevindt, vanaf andere computers in hetzelfde netwerk.

1. Gebruik de volgende opdracht om de URL voor de Oozie-service te verkrijgen:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Hiermee wordt informatie geretourneerd zoals de volgende XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Het `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` gedeelte is de URL die moet worden gebruikt met de opdracht Oozie.

2. Bewerk de code om de URL te vervangen door de naam die u eerder hebt ontvangen. Als u een omgevings variabele voor de URL wilt maken, gebruikt u het volgende, zodat u deze niet voor elke opdracht hoeft in te voeren:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Gebruik de volgende opdracht om de taak te verzenden:

    ```bash
    oozie job -config job.xml -submit
    ```

    Met deze opdracht wordt de taak informatie `job.xml` geladen van en verzonden naar Oozie, maar wordt deze niet uitgevoerd.

    Nadat de opdracht is voltooid, moet deze de ID van de taak retour neren, bijvoorbeeld `0000005-150622124850154-oozie-oozi-W`. Deze ID wordt gebruikt om de taak te beheren.

4. Bewerk de code hieronder om te `<JOBID>` vervangen door de id die u in de vorige stap hebt geretourneerd.  Als u de status van de taak wilt weer geven, gebruikt u de volgende opdracht:

    ```bash
    oozie job -info <JOBID>
    ```

    Dit retourneert informatie als de volgende tekst:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Deze taak heeft de status `PREP`. Deze status geeft aan dat de taak is gemaakt, maar niet is gestart.

5. Bewerk de code hieronder om te `<JOBID>` vervangen door de id die u eerder hebt geretourneerd.  Als u de taak wilt starten, gebruikt u de volgende opdracht:

    ```bash
    oozie job -start <JOBID>
    ```

    Als u de status na deze opdracht controleert, wordt deze uitgevoerd in een actieve status en wordt er informatie over de acties in de taak geretourneerd.  Het duurt enkele minuten voordat de taak is voltooid.

6. Bewerk de code hieronder om te `<serverName>` vervangen door de naam van uw Azure SQL `<sqlLogin>` -server en met de aanmelding bij Azure SQL Server.  Nadat de taak is voltooid, kunt u controleren of de gegevens zijn gegenereerd en geëxporteerd naar de SQL database tabel met behulp van de volgende opdracht.  Voer het wacht woord in bij de prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Voer bij `1>` de prompt de volgende query in:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    De informatie die wordt geretourneerd, is vergelijkbaar met de volgende tekst:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Zie het [opdracht regel programma Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)voor meer informatie over de Oozie-opdracht.

## <a name="oozie-rest-api"></a>Oozie REST API

Met de Oozie-REST API kunt u uw eigen hulpprogram ma's bouwen die samen werken met Oozie. Hieronder vindt u HDInsight-specifieke informatie over het gebruik van de Oozie-REST API:

* **URI**: U hebt toegang tot de REST API van buiten het cluster `https://CLUSTERNAME.azurehdinsight.net/oozie`op.

* **Verificatie**: Als u de verificatie wilt uitvoeren, gebruikt u de API het cluster-HTTP-account (beheerder) en het wacht woord. Bijvoorbeeld:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Zie [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)(Engelstalig) voor meer informatie over het gebruik van de Oozie rest API.

## <a name="oozie-web-ui"></a>Oozie-webgebruikersinterface

De Oozie-webgebruikersinterface biedt een webgebaseerde weer gave van de status van Oozie-taken in het cluster. Met de Web-UI kunt u de volgende informatie bekijken:

   * De status van taak
   * Taakdefinitie
   * Configuratie
   * Een grafiek van de acties in de taak
   * Logboeken voor de taak

U kunt ook de details van de acties in een taak bekijken.

Voer de volgende stappen uit om toegang te krijgen tot de Oozie-webgebruikersinterface:

1. Een SSH-tunnel maken naar het HDInsight-cluster. Zie [ssh-tunneling gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

2. Nadat u een tunnel hebt gemaakt, opent u de Ambari-web-gebruikers interface in `http://headnodehost:8080`uw webbrowser met behulp van URI.

3. Selecteer links op de pagina **Oozie** > **Quick links** > **Oozie Web UI**.

    ![Ambari oozie-Web-UI-stappen](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. De Oozie web-gebruikers interface wordt standaard gebruikt om de actieve werk stroom taken weer te geven. Als u alle werk stroom taken wilt weer geven, selecteert u **alle taken**.

    ![Werk stroom taken Oozie web console](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Selecteer de taak om meer informatie over een taak weer te geven.

    ![Informatie over HDInsight Apache Oozie-taak](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Op het tabblad **taak gegevens** ziet u de informatie over de basis taak en de afzonderlijke acties in de taak. U kunt de tabbladen bovenaan gebruiken om de **taak definitie**, **taak configuratie**, toegang tot het **taak logboek**te bekijken of een directed acyclic Graph (dag) van de taak weer te geven onder **taak dag**.

   * **Taak logboek**: Selecteer de knop **Logboeken ophalen** om alle logboeken voor de taak op te halen, of gebruik het veld **Zoek filter invoeren** om de logboeken te filteren.

       ![HDInsight Apache Oozie-taak logboek](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Taak dag**: De DAG is een grafisch overzicht van de gegevens paden die via de werk stroom worden uitgevoerd.

       ![HDInsight Apache Oozie job dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Als u een van de acties op het tabblad **taak gegevens** selecteert, wordt er informatie over de actie geopend. Selecteer bijvoorbeeld de actie **RunSqoopExport** .

    ![Informatie over HDInsight oozie-taak acties](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. U kunt details weer geven voor de actie, zoals een koppeling naar de **console-URL**. Gebruik deze koppeling om informatie over taak beheer voor de taak weer te geven.

## <a name="schedule-jobs"></a>Taken plannen

U kunt de coördinator gebruiken om een start, een eind en de frequentie van het voorval voor taken op te geven. Voer de volgende stappen uit om een schema voor de werk stroom te definiëren:

1. Gebruik de volgende opdracht om een bestand met de naam **Coordinator. XML**te maken:

    ```bash
    nano coordinator.xml
    ```

    Gebruik de volgende XML als de inhoud van het bestand:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > De `${...}` variabelen worden vervangen door waarden in de taak definitie tijdens runtime. De variabelen zijn:
    >
    > * `${coordFrequency}`: De tijd tussen het uitvoeren van exemplaren van de taak.
    > * `${coordStart}`: De begin tijd van de taak.
    > * `${coordEnd}`: De eind tijd van de taak.
    > * `${coordTimezone}`: Coördinator taken bevinden zich in een vaste tijd zone zonder zomer tijd, meestal aangeduid met UTC. Deze tijd zone wordt aangeduid als de *Oozie-verwerkings tijd.*
    > * `${wfPath}`: Het pad naar de werk stroom. XML.

2. Als u het bestand wilt opslaan, selecteert u CTRL + `Y`X, voert u in en selecteert u vervolgens **Enter**.

3. Als u het bestand wilt kopiëren naar de werkmap voor deze taak, gebruikt u de volgende opdracht:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Als u het `job.xml` bestand dat u eerder hebt gemaakt, wilt wijzigen, gebruikt u de volgende opdracht:

    ```bash
    nano job.xml
    ```

    Breng de volgende wijzigingen aan:

   * Als u wilt dat Oozie het coördinator bestand uitvoert in plaats van de werk `<name>oozie.wf.application.path</name>` stroom `<name>oozie.coord.application.path</name>`, wijzigt u in.

   * Als u de `workflowPath` variabele wilt instellen die wordt gebruikt door de coördinator, voegt u de volgende XML toe:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Vervang de `wasbs://mycontainer@mystorageaccount.blob.core.windows` tekst door de waarde die wordt gebruikt in de andere vermeldingen in het bestand job. XML.

   * Als u het begin, het einde en de frequentie voor de coördinator wilt definiëren, voegt u de volgende XML toe:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Met deze waarden wordt de begin tijd ingesteld op 12:00 uur op 10 mei 2018 en de eind tijd op 12 mei 2018. Het interval voor het uitvoeren van deze taak is ingesteld op dagelijks. De frequentie is in minuten, dus 24 uur x 60 minuten = 1440 minuten. Ten slotte wordt de tijd zone ingesteld op UTC.

5. Als u het bestand wilt opslaan, selecteert u CTRL + `Y`X, voert u in en selecteert u vervolgens **Enter**.

6. Gebruik de volgende opdracht om de taak te verzenden en te starten:

    ```bash
    oozie job -config job.xml -run
    ```

7. Als u naar de Oozie-webgebruikersinterface gaat en het tabblad **coördinator taken** selecteert, ziet u informatie zoals in de volgende afbeelding:

    ![Tabblad Oozie web console-coördinator taken](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    De **volgende materialisatie** -vermelding bevat de volgende keer dat de taak wordt uitgevoerd.

8. Net als bij de eerdere werk stroom taak, als u de taak vermelding in de web-gebruikers interface selecteert, wordt informatie over de taak weer gegeven:

    ![Taak info voor Apache Oozie Coordinator](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > In deze afbeelding worden alleen geslaagde uitvoeringen van de taak weer gegeven, niet de afzonderlijke acties binnen de geplande werk stroom. Als u de afzonderlijke acties wilt zien, selecteert u een van de **actie** vermeldingen.

    ![OOzie web console-taak informatie tabblad](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Problemen oplossen

Met de Oozie-gebruikers interface kunt u Oozie-logboeken weer geven. De Oozie-gebruikers interface bevat ook koppelingen naar de JobTracker-logboeken voor de MapReduce taken die door de werk stroom zijn gestart. Het patroon voor het oplossen van problemen moet zijn:

   1. Bekijk de taak in de Oozie-webgebruikersinterface.

   2. Als er een fout of fout optreedt voor een specifieke actie, selecteert u de actie om te zien of het **fout bericht** veld meer informatie over de fout bevat.

   3. Gebruik, indien beschikbaar, de URL van de actie om meer details weer te geven, zoals de JobTracker-logboeken, voor de actie.

Hieronder vindt u specifieke fouten die kunnen optreden en hoe u deze kunt oplossen.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Kan het cluster niet initialiseren

**Symptomen**: De taak status wordt gewijzigd in **opgeschort**. Details voor de taak geven de `RunHiveScript` status weer als **START_MANUAL**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Oorzaak**: De Azure Blob-opslag adressen die worden gebruikt in het bestand **Job. XML** bevatten niet de opslag container of de naam van het opslag account. De indeling van het Blob-opslag `wasbs://containername@storageaccountname.blob.core.windows.net`adres moet zijn.

**Resolutie**: Wijzig de Blob Storage-adressen die de taak gebruikt.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie is niet toegestaan om de &lt;gebruiker te imiteren&gt;

**Symptomen**: De taak status wordt gewijzigd in **opgeschort**. Details voor de taak geven de `RunHiveScript` status weer als **START_MANUAL**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    JA002: User: oozie is not allowed to impersonate <USER>

**Oorzaak**: De huidige machtigings instellingen staan niet toe dat Oozie het opgegeven gebruikers account imiteert.

**Resolutie**: Oozie kan gebruikers imiteren in de groep **gebruikers** . Gebruik de `groups USERNAME` om de groepen weer te geven waarvan het gebruikers account lid is. Als de gebruiker geen lid is van de groep **gebruikers** , gebruikt u de volgende opdracht om de gebruiker toe te voegen aan de groep:

    sudo adduser USERNAME users

> [!NOTE]  
> Het kan enkele minuten duren voordat HDInsight detecteert dat de gebruiker is toegevoegd aan de groep.

### <a name="launcher-error-sqoop"></a>FOUT bij starten (Sqoop)

**Symptomen**: De taak status wordt gewijzigd in **beëindigd**. Details voor de taak tonen de `RunSqoopExport` status als **fout**. Als u de actie selecteert, wordt het volgende fout bericht weer gegeven:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Oorzaak**: Sqoop kan het database stuur programma dat is vereist voor toegang tot de data base niet laden.

**Resolutie**: Wanneer u Sqoop van een Oozie-taak gebruikt, moet u het database stuur programma toevoegen aan de andere resources, zoals de werk stroom. XML, die door de taak wordt gebruikt. U kunt ook verwijzen naar het archief dat het database stuur programma `<sqoop>...</sqoop>` bevat vanuit de sectie van de werk stroom. XML.

Voor de taak in dit document gebruikt u bijvoorbeeld de volgende stappen:

1. Kopieer het `mssql-jdbc-7.0.0.jre8.jar` bestand naar de **/tutorials/useoozie** -map:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Wijzig de `workflow.xml` om de volgende XML toe te voegen op een `</sqoop>`nieuwe regel:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een Oozie-werk stroom definieert en hoe u een Oozie-taak uitvoert. Raadpleeg de volgende artikelen voor meer informatie over het werken met HDInsight:

* [Gegevens uploaden voor Apache Hadoop-taken in HDInsight][hdinsight-upload-data]
* [Apache Sqoop gebruiken met Apache Hadoop in HDInsight][hdinsight-use-sqoop]
* [Apache Hive gebruiken met Apache Hadoop op HDInsight][hdinsight-use-hive]
* [Apache Pig gebruiken met Apache Hadoop op HDInsight][hdinsight-use-pig]
* [Java MapReduce-Program ma's ontwikkelen voor HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
