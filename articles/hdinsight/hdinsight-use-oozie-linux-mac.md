---
title: Hadoop Oozie-werkstromen gebruiken in Azure HDInsight op Linux
description: Gebruik Hadoop Oozie in Linux-gebaseerde HDInsight. Meer informatie over het definiëren van een Oozie-werkstroom en het indienen van een Oozie-taak.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744907"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Apache Oozie gebruiken met Apache Hadoop voor het definiëren en uitvoeren van een werkstroom in Azure HDInsight op basis van Linux

Meer informatie over het gebruik van Apache Oozie met Apache Hadoop op Azure HDInsight. Oozie is een workflow- en coördinatiesysteem dat Hadoop-taken beheert. Oozie is geïntegreerd met de Hadoop stack en ondersteunt de volgende taken:

* Apache Hadoop MapReduce
* Apache Varken
* Apache Bijenkorf
* Apache Sqoop

U Oozie ook gebruiken om taken te plannen die specifiek zijn voor een systeem, zoals Java-programma's of shellscripts.

> [!NOTE]  
> Een andere optie om werkstromen met HDInsight te definiëren, is het gebruik van Azure Data Factory. Zie Apache Pig en [Apache Hive gebruiken met Data Factory][azure-data-factory-pig-hive]voor meer informatie over Data Factory. Zie [Apache Oozie uitvoeren in HDInsight Hadoop-clusters met Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)voor het gebruik van Oozie op clusters met Enterprise Security Package.

## <a name="prerequisites"></a>Vereisten

* **Een Hadoop cluster op HDInsight**. Zie [Aan de slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Een SSH-client.** Zie [Verbinding maken met HDInsight (Apache Hadoop) met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Een Azure SQL-database**.  Zie [Een Azure SQL-database maken in de Azure-portal](../sql-database/sql-database-get-started.md).  Dit artikel maakt gebruik van een database met de naam **oozietest**.

* Het [URI-schema](./hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit geldt `wasb://` voor Azure `abfs://` Storage, Azure Data `adl://` Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure `wasbs://`Storage, is de URI . Zie ook, [veilige overdracht](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Voorbeeldwerkstroom

De werkstroom die in dit document wordt gebruikt, bevat twee acties. Acties zijn definities voor taken, zoals het uitvoeren van Hive, Sqoop, MapReduce of andere processen:

![HDInsight oozie-werkstroomdiagram](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Met een Hive-actie wordt een HiveQL-script uitgevoerd om records uit de `hivesampletable` rapporten te extraheren die bij HDInsight zijn inbegrepen. Elke rij gegevens beschrijft een bezoek van een specifiek mobiel apparaat. De recordnotatie wordt weergegeven als de volgende tekst:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Het Hive-script dat in dit document wordt gebruikt, telt het totale aantal bezoeken voor elk platform, zoals Android of iPhone, en slaat de tellingen op in een nieuwe Hive-tabel.

    Zie [Apache Hive gebruiken met HDInsight][hdinsight-use-hive]voor meer informatie over Hive.

2. Met een Sqoop-actie wordt de inhoud van de nieuwe Hive-tabel geëxporteerd naar een tabel die is gemaakt in Azure SQL Database. Zie [Apache Sqoop gebruiken met HDInsight][hdinsight-use-sqoop]voor meer informatie over Sqoop.

> [!NOTE]  
> Zie [Nieuwe nieuwe in de Hadoop-clusterversies van HDInsight][hdinsight-versions]voor ondersteunde Oozie-versies op HDInsight-clusters.

## <a name="create-the-working-directory"></a>De werkmap maken

Oozie verwacht dat u alle resources die nodig zijn voor een taak opslaat in dezelfde map. In dit `wasbs:///tutorials/useoozie`voorbeeld wordt gebruik gebruikt . Voer de volgende stappen uit om deze map te maken:

1. Bewerk de onderstaande `sshuser` code om te vervangen door de `CLUSTERNAME` SSH-gebruikersnaam voor het cluster en vervang de naam van het cluster.  Voer vervolgens de code in om verbinding te maken met het HDInsight-cluster [met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Als u de map wilt maken, gebruikt u de volgende opdracht:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > De `-p` parameter zorgt voor het maken van alle mappen in het pad. De `data` map wordt gebruikt om de `useooziewf.hql` gegevens die door het script worden gebruikt, vast te houden.

3. Bewerk de onderstaande `sshuser` code om te vervangen door uw SSH-gebruikersnaam.  Gebruik de volgende opdracht om ervoor te zorgen dat Oozie zich kan voordoen als uw gebruikersaccount:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > U fouten negeren die aangeven dat `users` de gebruiker al lid is van de groep.

## <a name="add-a-database-driver"></a>Een databasestuurprogramma toevoegen

Omdat deze werkstroom Sqoop gebruikt om gegevens naar de SQL-database te exporteren, moet u een kopie van het JDBC-stuurprogramma opgeven dat wordt gebruikt om te communiceren met de SQL-database. Als u het JDBC-stuurprogramma naar de werkmap wilt kopiëren, gebruikt u de volgende opdracht uit de SSH-sessie:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Controleer het werkelijke JDBC-stuurprogramma dat bestaat op `/usr/share/java/`.

Als uw werkstroom andere bronnen heeft gebruikt, zoals een pot met een mapReduce-toepassing, moet u deze resources ook toevoegen.

## <a name="define-the-hive-query"></a>De Hive-query definiëren

Gebruik de volgende stappen om een Hive query language (HiveQL) script te maken dat een query definieert. U gebruikt de query later in dit document in een Oozie-werkstroom.

1. Gebruik de volgende opdracht om vanaf de SSH-verbinding een bestand met de naam `useooziewf.hql`te maken:

    ```bash
    nano useooziewf.hql
    ```

1. Nadat de GNU-nanoeditor is geopend, gebruikt u de volgende query als de inhoud van het bestand:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Er zijn twee variabelen die in het script worden gebruikt:

   * `${hiveTableName}`: Bevat de naam van de te maken tabel.

   * `${hiveDataFolder}`: Bevat de locatie om de gegevensbestanden voor de tabel op te slaan.

     Het werkstroomdefinitiebestand, workflow.xml in dit artikel, geeft deze waarden door aan dit HiveQL-script tijdens runtime.

1. Als u het bestand wilt opslaan, selecteert u **Ctrl+X,** voert u **Y**in en selecteert u **Enter**.  

1. Gebruik de volgende `useooziewf.hql` opdracht `wasbs:///tutorials/useoozie/useooziewf.hql`om te kopiëren naar:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Met deze `useooziewf.hql` opdracht wordt het bestand opgeslagen in de Opslag met HDFS-compatibel voor het cluster.

## <a name="define-the-workflow"></a>De werkstroom definiëren

Oozie-werkstroomdefinities zijn geschreven in Hadoop Process Definition Language (hPDL), een XML-procesdefinitietaal. Gebruik de volgende stappen om de werkstroom te definiëren:

1. Gebruik de volgende instructie om een nieuw bestand te maken en te bewerken:

    ```bash
    nano workflow.xml
    ```

2. Nadat de nano-editor is geopend, voert u de volgende XML in als inhoud van het bestand:

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

    Er zijn twee acties gedefinieerd in de werkstroom:

   * `RunHiveScript`: Deze actie is de `useooziewf.hql` startactie en voert het Hive-script uit.

   * `RunSqoopExport`: Met deze actie worden de gegevens die zijn gemaakt van het Hive-script geëxporteerd naar een SQL-database met behulp van Sqoop. Deze actie wordt `RunHiveScript` alleen uitgevoerd als de actie is geslaagd.

     De werkstroom bevat verschillende `${jobTracker}`vermeldingen, zoals . U vervangt deze vermeldingen door de waarden die u gebruikt in de taakdefinitie. U maakt de taakdefinitie later in dit document.

     Let ook `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` op de vermelding in de Sqoop sectie. Dit item instrueert Oozie om dit archief beschikbaar te maken voor Sqoop wanneer deze actie wordt uitgevoerd.

3. Als u het bestand wilt opslaan, selecteert u **Ctrl+X,** voert u **Y**in en selecteert u **Enter**.  

4. Gebruik de volgende opdracht `workflow.xml` om `/tutorials/useoozie/workflow.xml`het bestand te kopiëren naar:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Een tabel maken

> [!NOTE]  
> Er zijn veel manieren om verbinding te maken met SQL Database om een tabel te maken. In de volgende stappen wordt [FreeTDS](https://www.freetds.org/) gebruikt vanuit het HDInsight-cluster.

1. Gebruik de volgende opdracht om FreeTDS te installeren op het HDInsight-cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Bewerk de onderstaande `<serverName>` code om te vervangen `<sqlLogin>` door de naam van uw Azure SQL-server en met de azure SQL-server-aanmelding.  Voer de opdracht in om verbinding te maken met de vereiste SQL-database.  Voer het wachtwoord bij de prompt in.

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

    Wanneer u de instructie `GO` invoert, worden de vorige instructies geëvalueerd. Met deze instructies wordt `mobiledata`een tabel gemaakt met de naam , die wordt gebruikt door de werkstroom.

    Gebruik de volgende opdrachten om te controleren of de tabel is gemaakt:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    U ziet uitvoer als de volgende tekst:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Sluit het tsql-hulpprogramma `1>` af door op de prompt in te voeren. `exit`

## <a name="create-the-job-definition"></a>De taakdefinitie maken

De taakdefinitie beschrijft waar u de workflow.xml vinden. Het beschrijft ook waar andere bestanden die worden `useooziewf.hql`gebruikt door de werkstroom te vinden, zoals . Bovendien worden de waarden gedefinieerd voor eigenschappen die worden gebruikt binnen de werkstroom en de bijbehorende bestanden.

1. Als u het volledige adres van de standaardopslag wilt krijgen, gebruikt u de volgende opdracht. Dit adres wordt gebruikt in het configuratiebestand dat u in de volgende stap maakt.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Met deze opdracht retourneert informatie zoals de volgende XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Als het HDInsight-cluster Azure Storage als `<value>` standaardopslag `wasbs://`gebruikt, begint de inhoud van het element met . Als azure Data Lake Storage Gen1 in `adl://`plaats daarvan wordt gebruikt, begint dit met . Als Azure Data Lake Storage Gen2 `abfs://`wordt gebruikt, begint dit met .

    Sla de inhoud `<value>` van het element op, zoals het in de volgende stappen wordt gebruikt.

2. Bewerk de xml hieronder als volgt:

    |Tijdelijke waarde| Vervangen waarde|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Waarde ontvangen vanaf stap 1.|
    |beheerder| Uw inlognaam voor het HDInsight-cluster als deze niet beheerder is.|
    |Servernaam| De naam van azure SQL-databaseserver.|
    |sqlLogin| Azure SQL-databaseserver inloggen.|
    |sqlPassword sqlPassword| Inlogwachtwoord van Azure SQL-databaseserver.|

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

    De meeste informatie in dit bestand wordt gebruikt om de waarden in te vullen die `${nameNode}`worden gebruikt in de bestanden workflow.xml of ooziewf.hql, zoals .  Als het pad `wasbs` een pad is, moet u het volledige pad gebruiken. Verkort het niet tot `wasbs:///`gewoon. De `oozie.wf.application.path` vermelding bepaalt waar het bestand workflow.xml moet worden gevonden. Dit bestand bevat de werkstroom die door deze taak is uitgevoerd.

3. Als u de oozie-taakdefinitieconfiguratie wilt maken, gebruikt u de volgende opdracht:

    ```bash
    nano job.xml
    ```

4. Nadat de nano-editor is geopend, plakt u de bewerkte XML als de inhoud van het bestand.

5. Als u het bestand wilt opslaan, selecteert u **Ctrl+X,** voert u **Y**in en selecteert u **Enter**.

## <a name="submit-and-manage-the-job"></a>De taak verzenden en beheren

De volgende stappen gebruiken de opdracht Oozie om Oozie-werkstromen in het cluster in te dienen en te beheren. De Oozie commando is een vriendelijke interface over de [Oozie REST API.](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)

> [!IMPORTANT]  
> Wanneer u de opdracht Oozie gebruikt, moet u de FQDN gebruiken voor het hdInsight-hoofdknooppunt. Deze FQDN is alleen toegankelijk vanaf het cluster of als het cluster zich op een virtueel Azure-netwerk bevindt, vanaf andere machines in hetzelfde netwerk.

1. Als u de URL naar de Oozie-service wilt verkrijgen, gebruikt u de volgende opdracht:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Hiermee worden gegevens geretourneerd zoals de volgende XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Het `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` gedeelte is de URL die u wilt gebruiken met de opdracht Oozie.

2. Bewerk de code om de URL te vervangen door de URL die u eerder hebt ontvangen. Als u een omgevingsvariabele voor de URL wilt maken, gebruikt u het volgende, zodat u deze niet voor elke opdracht hoeft in te voeren:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Gebruik het volgende om de taak in te dienen:

    ```bash
    oozie job -config job.xml -submit
    ```

    Deze opdracht laadt de `job.xml` taakgegevens van en stuurt deze naar Oozie, maar voert deze niet uit.

    Nadat de opdracht is voltooid, moet de id van `0000005-150622124850154-oozie-oozi-W`de taak worden teruggegeven, bijvoorbeeld . Deze ID wordt gebruikt om de taak te beheren.

4. Bewerk de onderstaande `<JOBID>` code om te vervangen door de id die in de vorige stap is geretourneerd.  Als u de status van de taak wilt weergeven, gebruikt u de volgende opdracht:

    ```bash
    oozie job -info <JOBID>
    ```

    Hiermee worden informatie geretourneerd zoals de volgende tekst:

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

    Deze baan heeft `PREP`een status van . Deze status geeft aan dat de taak is gemaakt, maar niet is gestart.

5. Bewerk de onderstaande `<JOBID>` code om te vervangen door de eerder geretourneerde id.  Als u de taak wilt starten, gebruikt u de volgende opdracht:

    ```bash
    oozie job -start <JOBID>
    ```

    Als u de status na deze opdracht controleert, wordt deze in een lopende toestand uitgevoerd en wordt informatie geretourneerd voor de acties binnen de taak.  De klus duurt enkele minuten.

6. Bewerk de onderstaande `<serverName>` code om te vervangen `<sqlLogin>` door de naam van uw Azure SQL-server en met de azure SQL-server-aanmelding.  *Nadat de taak is voltooid,* u met behulp van de volgende opdracht controleren of de gegevens zijn gegenereerd en geëxporteerd naar de SQL-databasetabel.  Voer het wachtwoord bij de prompt in.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Voer `1>` bij de prompt de volgende query in:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    De geretourneerde informatie is als de volgende tekst:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Zie [Opdrachtregelgereedschap Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)voor meer informatie over de opdracht Oozie.

## <a name="oozie-rest-api"></a>Oozie REST API

Met de Oozie REST API kun je je eigen tools bouwen die werken met Oozie. Het volgende is HDInsight-specifieke informatie over het gebruik van de Oozie REST API:

* **URI:** U hebt toegang tot de `https://CLUSTERNAME.azurehdinsight.net/oozie`REST API van buiten het cluster op .

* **Verificatie:** gebruik de API het cluster HTTP-account (beheerder) en wachtwoord om te verifiëren. Bijvoorbeeld:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Zie [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)voor meer informatie over het gebruik van de Oozie REST API.

## <a name="oozie-web-ui"></a>Oozie web Gebruikersinterface

De Oozie web-gebruikersinterface biedt een webgebaseerde weergave van de status van Oozie-taken op het cluster. Met de web-gebruikersinterface u de volgende informatie bekijken:

   * Functiestatus
   * Jobdefinitie
   * Configuratie
   * Een grafiek van de acties in de taak
   * Logboeken voor de taak

U ook de details voor de acties in een taak bekijken.

Voer de volgende stappen uit om toegang te krijgen tot de oozie-webgebruikersinterface:

1. Maak een SSH-tunnel naar het HDInsight-cluster. Zie [SSH Tunneling gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

2. Nadat u een tunnel hebt gemaakt, opent u de gebruikersinterface van Ambari in uw webbrowser met URI `http://headnodehost:8080`.

3. Selecteer aan de linkerkant van de pagina **oozie** > **Quick Links** > **Oozie Web UI**.

    ![Apache Ambari oozie web ui stappen](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. De oozie web-gebruikersinterface standaard om de lopende werkstroomtaken weer te geven. Als u alle werkstroomtaken wilt weergeven, selecteert u **Alle taken**.

    ![Oozie-werkstroomwerktaak](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Als u meer informatie over een taak wilt weergeven, selecteert u de taak.

    ![HDInsight Apache Oozie job info](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Op het tabblad **Taakgegevens** ziet u de basistaakinformatie en de individuele acties binnen de taak. U de tabbladen bovenaan gebruiken om de **taakdefinitie,** **taakconfiguratie,** het **taaklogboek**te bekijken of een gerichte acyclische grafiek (DAG) van de taak onder **TaakDAG**weer te geven.

   * **Taaklogboek:** selecteer de knop **Logboeken opvragen** om alle logboeken voor de taak op te halen of gebruik het veld **Zoekfilter invoeren** om de logboeken te filteren.

       ![HDInsight Apache Oozie job log](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **TaakDAG**: De DAG is een grafisch overzicht van de gegevenspaden die door de workflow worden genomen.

       ![HDInsight Apache Oozie jobdag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Als u een van de acties selecteert op het tabblad **Taakgegevens,** wordt informatie voor de actie weergegeven. Selecteer bijvoorbeeld de actie **RunSqoopExport.**

    ![HDInsight oozie job action info](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. U details voor de actie zien, zoals een koppeling naar de URL van de **console.** Gebruik deze link om de informatie over taaktrackers voor de taak weer te geven.

## <a name="schedule-jobs"></a>Taken plannen

U de coördinator gebruiken om een begin, een einde en de gebeurtenisfrequentie voor taken op te geven. Voer de volgende stappen uit om een planning voor de werkstroom te definiëren:

1. Gebruik de volgende opdracht om een bestand met de naam **coordinator.xml**te maken:

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
    > De `${...}` variabelen worden vervangen door waarden in de taakdefinitie bij runtime. De variabelen zijn:
    >
    > * `${coordFrequency}`: De tijd tussen het uitvoeren van instanties van de taak.
    > * `${coordStart}`: De begintijd van de baan.
    > * `${coordEnd}`: De eindtijd van het werk.
    > * `${coordTimezone}`: Coördinator taken bevinden zich in een vaste tijdzone zonder zomertijd, meestal vertegenwoordigd door het gebruik van UTC. Deze tijdzone wordt de *Oozie-verwerkingstijdzone genoemd.*
    > * `${wfPath}`: Het pad naar de workflow.xml.

2. Als u het bestand wilt opslaan, selecteert u **Ctrl+X,** voert u **Y**in en selecteert u **Enter**.

3. Als u het bestand wilt kopiëren naar de werkmap voor deze taak, gebruikt u de volgende opdracht:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Als u `job.xml` het bestand wilt wijzigen dat u eerder hebt gemaakt, gebruikt u de volgende opdracht:

    ```bash
    nano job.xml
    ```

    Breng de volgende wijzigingen aan:

   * Als u Oozie wilt instrueren om het `<name>oozie.wf.application.path</name>` coördinatorbestand uit te voeren in plaats van de werkstroom, wijzigt u in `<name>oozie.coord.application.path</name>`.

   * Als u `workflowPath` de variabele wilt instellen die door de coördinator wordt gebruikt, voegt u de volgende XML toe:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Vervang `wasbs://mycontainer@mystorageaccount.blob.core.windows` de tekst door de waarde die wordt gebruikt in de andere vermeldingen in het bestand job.xml.

   * Als u het begin, de eind- en frequentie voor de coördinator wilt definiëren, voegt u de volgende XML toe:

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

       Deze waarden stellen de begintijd in op 10 mei 2018 en de eindtijd naar 12 mei 2018. Het interval voor het uitvoeren van deze taak is ingesteld op dagelijks. De frequentie is in minuten, dus 24 uur x 60 minuten = 1440 minuten. Ten slotte is de tijdzone ingesteld op UTC.

5. Als u het bestand wilt opslaan, selecteert u **Ctrl+X,** voert u **Y**in en selecteert u **Enter**.

6. Als u de taak wilt verzenden en starten, gebruikt u de volgende opdracht:

    ```bash
    oozie job -config job.xml -run
    ```

7. Als u naar de gebruikersinterface van het Oozie-web gaat en het tabblad **Campagnecoördinator vacatures** selecteert, ziet u informatie zoals in de volgende afbeelding:

    ![Tabblad Oozie-webconsolecoördinator](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    Het item **Volgende materialisatie** bevat de volgende keer dat de taak wordt uitgevoerd.

8. Als u, net als de eerdere werkstroomtaak, de taakvermelding in de webgebruikersinterface selecteert, wordt informatie over de taak weergegeven:

    ![Apache Oozie coördinator job info](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Deze afbeelding toont alleen succesvolle uitvoeringen van de taak, niet de afzonderlijke acties binnen de geplande werkstroom. Als u de afzonderlijke acties wilt bekijken, selecteert u een van de **actievermeldingen.**

    ![Tabblad Functiegegevens van oOzie-webconsole](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Problemen oplossen

Met de Oozie-gebruikersinterface u Oozie-logboeken bekijken. De Oozie-gebruikersinterface bevat ook koppelingen naar de JobTracker-logboeken voor de mapReduce-taken die door de werkstroom zijn gestart. Het patroon voor het oplossen van problemen moet zijn:

   1. Bekijk de vacature in oozie web gebruikersinterface.

   2. Als er een fout of fout optreedt voor een specifieke actie, selecteert u de actie om te zien of het veld **Foutbericht** meer informatie over de fout bevat.

   3. Gebruik indien beschikbaar de URL van de actie om meer details, zoals de JobTracker-logboeken, weer te geven voor de actie.

De volgende zijn specifieke fouten die u tegenkomen en hoe u deze oplossen.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Kan cluster niet initialiseren

**Symptomen**: De functiestatus verandert in **SUSPENDED**. In de details `RunHiveScript` voor de taak wordt de status **als START_MANUAL**weergegeven. Als u de actie selecteert, wordt het volgende foutbericht weergegeven:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Oorzaak:** de Azure Blob-opslagadressen die worden gebruikt in het **bestand job.xml,** bevatten niet de naam van de opslagcontainer of het opslagaccount. De opslagadresindeling van `wasbs://containername@storageaccountname.blob.core.windows.net`Blob moet .

**Oplossing:** wijzig de Blob-opslagadressen die de taak gebruikt.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie mag &lt;GEBRUIKER niet imiteren&gt;

**Symptomen**: De functiestatus verandert in **SUSPENDED**. In de details `RunHiveScript` voor de taak wordt de status **als START_MANUAL**weergegeven. Als u de actie selecteert, wordt het volgende foutbericht weergegeven:

    JA002: User: oozie is not allowed to impersonate <USER>

**Oorzaak:** met de huidige machtigingsinstellingen kan Oozie zich niet voordoen als het opgegeven gebruikersaccount.

**Oplossing**: Oozie kan zich voordoen als gebruikers in de **gebruikersgroep.** Gebruik `groups USERNAME` de groep om de groepen te zien waarvan het gebruikersaccount lid is. Als de gebruiker geen lid is van de **gebruikersgroep,** gebruikt u de volgende opdracht om de gebruiker aan de groep toe te voegen:

    sudo adduser USERNAME users

> [!NOTE]  
> Het kan enkele minuten duren voordat HDInsight herkent dat de gebruiker aan de groep is toegevoegd.

### <a name="launcher-error-sqoop"></a>Launcher FOUT (Sqoop)

**Symptomen**: De status van de baan verandert in **KILLED**. In gegevens voor `RunSqoopExport` de taak wordt de status ALS **FOUT weergegeven.** Als u de actie selecteert, wordt het volgende foutbericht weergegeven:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Oorzaak:** Sqoop kan het databasestuurprogramma niet laden dat nodig is om toegang te krijgen tot de database.

**Oplossing:** Wanneer u Sqoop gebruikt vanuit een Oozie-taak, moet u het databasestuurprogramma opnemen bij de andere bronnen, zoals de workflow.xml, die de taak gebruikt. Verwijs ook naar het archief dat `<sqoop>...</sqoop>` het databasestuurprogramma bevat vanuit de sectie van de workflow.xml.

Voor de taak in dit document gebruikt u bijvoorbeeld de volgende stappen:

1. Kopieer `mssql-jdbc-7.0.0.jre8.jar` het bestand naar de **map /tutorials/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Wijzig `workflow.xml` de volgende XML op een `</sqoop>`nieuwe regel:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel heb je geleerd hoe je een Oozie-workflow definieert en hoe je een Oozie-taak uitvoert. Zie de volgende artikelen voor meer informatie over hoe u met HDInsight werken:

* [Gegevens uploaden voor Apache Hadoop-taken in HDInsight][hdinsight-upload-data]
* [Apache Sqoop gebruiken met Apache Hadoop in HDInsight][hdinsight-use-sqoop]
* [Gebruik Apache Hive met Apache Hadoop op HDInsight][hdinsight-use-hive]
* [Java MapOntwikkelenVerlaag programma's voor HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
