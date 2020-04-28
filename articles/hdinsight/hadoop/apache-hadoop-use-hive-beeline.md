---
title: Apache Beeline gebruiken met Apache Hive-Azure HDInsight
description: Meer informatie over het gebruik van de Beeline-client om Hive-query's uit te voeren met Hadoop op HDInsight. Beeline is een hulp programma voor het werken met HiveServer2 over JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2396207c88716420d299382006a270eb747ddc03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192660"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>De Apache Beeline-client gebruiken met Apache Hive

Meer informatie over het gebruik van [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) om Apache Hive-query's uit te voeren op HDInsight.

Beeline is een Hive-client die is opgenomen op de hoofd knooppunten van uw HDInsight-cluster. Als u Beeline lokaal wilt installeren, raadpleegt u [Beeline-client installeren](#install-beeline-client)hieronder. Beeline maakt gebruik van JDBC om verbinding te maken met HiveServer2, een service die wordt gehost op uw HDInsight-cluster. U kunt Beeline ook gebruiken voor toegang tot de component op HDInsight op afstand via internet. De volgende voor beelden bieden de meest voorkomende verbindings reeksen die worden gebruikt om verbinding te maken met HDInsight vanuit Beeline.

## <a name="types-of-connections"></a>Typen verbindingen

### <a name="from-an-ssh-session"></a>Vanuit een SSH-sessie

Wanneer u verbinding maakt vanuit een SSH-sessie met een cluster hoofd knooppunt, kunt u vervolgens `headnodehost` verbinding maken met `10001`het adres op de poort:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Via een Azure-Virtual Network

Wanneer u vanaf een client verbinding maakt met HDInsight via een Azure-Virtual Network, moet u de Fully Qualified Domain Name (FQDN) van het hoofd knooppunt van het cluster opgeven. Omdat deze verbinding rechtstreeks met de cluster knooppunten tot stand is gebracht, gebruikt de `10001`verbinding poort:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Vervang `<headnode-FQDN>` door de Fully Qualified Domain name van een cluster hoofd knooppunt. Als u de Fully Qualified Domain Name van een hoofd knooppunt wilt vinden, gebruikt u de informatie in het bestand [HDInsight beheren met behulp van het Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) -document.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Naar HDInsight Enterprise Security Package (ESP)-cluster met behulp van Kerberos

Wanneer u vanaf een client verbinding maakt met een Enterprise Security Package (ESP)-cluster dat is gekoppeld aan Azure Active Directory (AAD)-DS op een computer in dezelfde realm van het cluster, moet u `<AAD-Domain>` ook de domein naam en de naam van een domein gebruikers account opgeven met `<username>`machtigingen voor toegang tot het cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Vervang `<username>` door de naam van een account in het domein met machtigingen voor toegang tot het cluster. Vervang `<AAD-DOMAIN>` door de naam van de Azure Active Directory (Aad) waaraan het cluster is gekoppeld. Gebruik een letterlijke teken reeks `<AAD-DOMAIN>` voor de waarde, anders wordt de referentie niet gevonden. Controleer `/etc/krb5.conf` zo nodig de realm-namen.

De JDBC-URL vinden vanuit Ambari:

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, waarbij `CLUSTERNAME` de naam van het cluster is. Zorg ervoor dat HiveServer2 wordt uitgevoerd.

1. Het klem bord gebruiken om de HiveServer2 JDBC-URL te kopiëren.

---

### <a name="over-public-or-private-endpoints"></a>Via open bare of persoonlijke eind punten

Wanneer u verbinding maakt met een cluster met behulp van de open bare of persoonlijke eind punten, moet u de naam `admin`van het cluster aanmeldings account (standaard) en het wacht woord opgeven. Bijvoorbeeld, met behulp van Beeline van een-client systeem om `clustername.azurehdinsight.net` verbinding te maken met het adres. Deze verbinding wordt tot stand gebracht `443`en is versleuteld met behulp van TLS/SSL.

Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `admin` door het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wacht woord voor het account voor het aanmelden bij het cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de opdracht `curl` gebruiken met `-v` de optie voor het oplossen van verbindings problemen met open bare of persoonlijke eind punten voordat u Beeline gebruikt.

---

### <a name="use-beeline-with-apache-spark"></a>Beeline gebruiken met Apache Spark

Apache Spark biedt een eigen implementatie van HiveServer2, die ook wel de Spark Thrift-server wordt genoemd. Deze service maakt gebruik van Spark SQL om query's op te lossen in plaats van Hive. En kunnen betere prestaties leveren, afhankelijk van uw query.

#### <a name="through-public-or-private-endpoints"></a>Via open bare of privé-eind punten

Het gebruikte connection string is iets anders. In plaats van `httpPath=/hive2` het te `httpPath/sparkhive2`gebruiken. Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `admin` door het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wacht woord voor het account voor het aanmelden bij het cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de opdracht `curl` gebruiken met `-v` de optie voor het oplossen van verbindings problemen met open bare of persoonlijke eind punten voordat u Beeline gebruikt.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Van cluster kop of binnen Azure Virtual Network met Apache Spark

Wanneer u rechtstreeks verbinding maakt met het hoofd knooppunt van het cluster of van een resource binnen hetzelfde Azure-Virtual Network als het HDInsight `10002` -cluster, moet de poort worden gebruikt voor `10001`de Spark Thrift-server in plaats van. In het volgende voor beeld ziet u hoe u rechtstreeks verbinding maakt met het hoofd knooppunt:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Vereisten voor voor beelden

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Let op het URI-schema voor de primaire opslag van uw cluster. Bijvoorbeeld `wasb://` voor Azure Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, is `wasbs://`de URI. Zie [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md)voor meer informatie.

* Optie 1: een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Bij de meeste stappen in dit document wordt ervan uitgegaan dat u Beeline gebruikt vanuit een SSH-sessie naar het cluster.

* Optie 2: een lokale Beeline-client.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Dit voor beeld is gebaseerd op het gebruik van de Beeline-client van een SSH-verbinding.

1. Open een SSH-verbinding met het cluster met de onderstaande code. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor het SSH-gebruikers account in.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Maak verbinding met HiveServer2 met uw Beeline-client vanuit uw open SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-opdrachten beginnen met `!` een teken, bijvoorbeeld `!help` Help weer gegeven. Voor sommige `!` opdrachten kan echter wel worden wegge laten. Werkt bijvoorbeeld `help` ook.

    Dit wordt `!sql`gebruikt om HiveQL-instructies uit te voeren. HiveQL wordt echter vaak gebruikt, zodat u de voor gaande `!sql`kunt weglaten. De volgende twee instructies zijn gelijk:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Op een nieuw cluster wordt slechts één tabel weer gegeven: **hivesampletable**.

4. Gebruik de volgende opdracht om het schema voor de hivesampletable weer te geven:

    ```hiveql
    describe hivesampletable;
    ```

    Met deze opdracht wordt de volgende informatie geretourneerd:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    In deze informatie worden de kolommen in de tabel beschreven.

5. Voer de volgende instructies in om een tabel met de naam **log4jLogs** te maken met behulp van voorbeeld gegevens die zijn opgegeven met het HDInsight-cluster: (indien nodig herzien op basis van uw URI-schema).

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Deze instructies doen de volgende acties:

    |Rekeningen |Beschrijving |
    |---|---|
    |TABEL NEERZETTEN|Als de tabel bestaat, wordt deze verwijderd.|
    |EXTERNE TABEL MAKEN|Hiermee maakt u een **externe** tabel in Hive. Externe tabellen slaan de tabel definitie in Hive alleen op. De gegevens blijven op de oorspronkelijke locatie.|
    |RIJ-INDELING|Hoe de gegevens worden ingedeeld. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
    |OPGESLAGEN ALS TEXTFILE-LOCATIE|Waar de gegevens worden opgeslagen en in welke bestands indeling.|
    |SELECT|Hiermee wordt het aantal rijen geselecteerd waarin de kolom **T4** de waarde **[error]** bevat. Deze query retourneert de waarde **3** als er drie rijen met deze waarde zijn.|
    |INPUT__FILE__NAME zoals%. log|Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om garbage-gegevens in de resultaten te voor komen, geeft deze instructie aan dat de component alleen gegevens moet retour neren van bestanden die eindigen op. log.|

   > [!NOTE]  
   > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een MapReduce-bewerking.
   >
   > Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar alleen de tabel definitie.

    De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Beeline afsluiten:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Een HiveQL-bestand uitvoeren

Dit voor beeld is een voortzetting van het vorige voor beeld. Gebruik de volgende stappen om een bestand te maken en voer het vervolgens uit met Beeline.

1. Gebruik de volgende opdracht om een bestand met de naam **query. HQL**te maken:

    ```bash
    nano query.hql
    ```

1. Gebruik de volgende tekst als de inhoud van het bestand. Met deze query maakt u een nieuwe interne tabel met de naam **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze instructies doen de volgende acties:

    |Rekeningen |Beschrijving |
    |---|---|
    |CREATE TABLE ALS DEZE NIET BESTAAT|Als de tabel nog niet bestaat, wordt deze gemaakt. Omdat het **externe** tref woord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-Data Warehouse en worden volledig beheerd door Hive.|
    |OPGESLAGEN ALS ORC|Hiermee worden de gegevens opgeslagen in de ORC-indeling (Optimized Row in kolommen). De ORC-indeling is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.|
    |OVERSCHRIJVEN INVOEGEN... UITGESCHAKELD|Hiermee worden rijen uit de **log4jLogs** -tabel met **[error]** geselecteerd en worden de gegevens vervolgens ingevoegd in de tabel **errorLogs** .|

    > [!NOTE]  
    > In tegens telling tot externe tabellen, verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

1. Als u het bestand wilt opslaan, gebruikt u **CTRL**+**X**en voert u vervolgens **Y**en tenslotte **Enter**.

1. Gebruik het volgende om het bestand uit te voeren met beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > De `-i` para meter start Beeline en voert de instructies in `query.hql` het bestand uit. Zodra de query is voltooid, ontvangt u de `jdbc:hive2://headnodehost:10001/>` prompt. U kunt ook een bestand uitvoeren met de `-f` para meter, waardoor Beeline wordt afgesloten nadat de query is voltooid.

1. Als u wilt controleren of de **errorLogs** -tabel is gemaakt, gebruikt u de volgende instructie om alle rijen uit **errorLogs**te retour neren:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Er moeten drie rijen met gegevens worden geretourneerd, die allemaal **[error]** bevatten in kolom T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Beeline-client installeren

Hoewel Beeline is opgenomen op de hoofd knooppunten, wilt u deze mogelijk lokaal installeren.  De installatie stappen voor een lokale computer zijn gebaseerd op een [Windows-subsysteem voor Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Pakket lijsten bijwerken. Voer de volgende opdracht in uw bash-shell in:

    ```bash
    sudo apt-get update
    ```

1. Installeer Java als dat niet is geïnstalleerd. U kunt controleren met de `which java` opdracht.

    1. Als er geen Java-pakket is geïnstalleerd, voert u de volgende opdracht in:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Open het bashrc-bestand (vaak gevonden in ~/.bashrc) `nano ~/.bashrc`:.

    1. Wijzig het bashrc-bestand. Voeg de volgende regel toe aan het einde van het bestand:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Druk vervolgens op **CTRL + X**, vervolgens op **j**en vervolgens op ENTER.

1. Down load Hadoop-en Beeline-archieven, voer de volgende opdrachten in:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. De archieven uitpakken, voert u de volgende opdrachten in:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Wijzig het bashrc-bestand verder. U moet het pad naar de locatie waar de archieven zijn uitgepakt, identificeren. Als u het [Windows-subsysteem voor Linux](https://docs.microsoft.com/windows/wsl/install-win10)gebruikt en u de stappen precies hebt uitgevoerd, is `/mnt/c/Users/user/`het pad `user` waar uw gebruikers naam is.

    1. Open het bestand:`nano ~/.bashrc`

    1. Wijzig de onderstaande opdrachten met het juiste pad en voer deze vervolgens aan het einde van het bashrc-bestand in:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Druk vervolgens op **CTRL + X**, vervolgens op **j**en vervolgens op ENTER.

1. Sluit de bash-sessie en open deze opnieuw.

1. Test de verbinding. Gebruik de verbindings indeling van [meer dan open bare of persoonlijke eind punten](#over-public-or-private-endpoints).

## <a name="next-steps"></a>Volgende stappen

* Zie [Apache Hive gebruiken met Apache Hadoop in hdinsight](hdinsight-use-hive.md) voor meer algemene informatie over Hive in hdinsight

* Zie [MapReduce gebruiken met Apache Hadoop op hdinsight](hdinsight-use-mapreduce.md) voor meer informatie over andere manieren waarop u met Hadoop in hdinsight kunt werken.
