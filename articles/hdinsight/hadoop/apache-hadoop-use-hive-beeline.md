---
title: Apache Beeline gebruiken met Apache Hive - Azure HDInsight
description: Meer informatie over het gebruik van de Beeline-client om Hive-query's uit te voeren met Hadoop op HDInsight. Beeline is een hulpprogramma voor het werken met HiveServer2 via JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 426294f20dd51538920182a0e7a2915f6a47ba54
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383561"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>De Apache Beeline-client gebruiken met Apache Hive

Meer informatie over het gebruik van [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) om Apache Hive-query's uit te voeren op HDInsight.

Beeline is een Hive-client die is opgenomen op de hoofdknooppunten van uw HDInsight-cluster. Zie [Beeline-client installeren](#install-beeline-client), hieronder. Beeline maakt gebruik van JDBC om verbinding te maken met HiveServer2, een service die wordt gehost op uw HDInsight-cluster. U Beeline ook gebruiken om op afstand via het internet toegang te krijgen tot Hive op HDInsight. De volgende voorbeelden bieden de meest voorkomende verbindingstekenreeksen die worden gebruikt om verbinding te maken met HDInsight van Beeline.

## <a name="types-of-connections"></a>Typen verbindingen

### <a name="from-an-ssh-session"></a>Van een SSH-sessie

Wanneer u verbinding maakt van een SSH-sessie met een `headnodehost` clusterheadnode, u verbinding maken met het adres op de poort: `10001`

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Via een Azure Virtual Network

Wanneer u verbinding maakt van een client met HDInsight via een Azure Virtual Network, moet u de volledig gekwalificeerde domeinnaam (FQDN) van een clusterhoofdknooppunt opgeven. Aangezien deze verbinding rechtstreeks met de clusterknooppunten `10001`wordt gemaakt, maakt de verbinding gebruik van poort:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

<a name="replace-headnode-fqdn-with-the-fully-qualified-domain-name-of-a-cluster-headnode-to-find-the-fully-qualified-domain-name-of-a-headnode-use-the-information-in-the-manage-hdinsight-using-the-apache-ambari-rest-api-document"></a>Vervang `<headnode-FQDN>` door de volledig gekwalificeerde domeinnaam van een clusterheadnode. Als u de volledig gekwalificeerde domeinnaam van een headnode wilt vinden, gebruikt u de informatie in het [MANAGE HDInsight met behulp van het Apache Ambari REST API-document.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)
---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>ESP-cluster (HDInsight Enterprise Security Package) gebruiken met Kerberos

Wanneer u verbinding maakt tussen een client en een ESP-cluster (Enterprise Security Package) dat is gekoppeld aan Azure Active Directory `<AAD-Domain>` (AAD)-DS op een machine in `<username>`dezelfde realm van het cluster, moet u ook de domeinnaam en de naam van een domeingebruikersaccount opgeven met machtigingen voor toegang tot het cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Vervang `<username>` de naam van een account op het domein door machtigingen om toegang te krijgen tot het cluster. Vervang `<AAD-DOMAIN>` de naam van de Azure Active Directory (AAD) waaraan het cluster is verbonden. Gebruik een hoofdlettertekenreeks `<AAD-DOMAIN>` voor de waarde, anders wordt de referentie niet gevonden. Controleer `/etc/krb5.conf` voor de koninkrijksnamen indien nodig.

Ga als verkenner naar de JDBC-URL van Ambari:

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`naar `CLUSTERNAME` , waar is de naam van uw cluster. Controleer of HiveServer2 wordt uitgevoerd.

1. Gebruik klembord om de JDBC-URL van HiveServer2 te kopiëren.

---

### <a name="over-public-or-private-endpoints"></a>Over openbare of private eindpunten

Wanneer u verbinding maakt met een cluster met de openbare of privéeindpunten, moet u de naam van het clusteraccount (standaard) `admin`en het wachtwoord opgeven. Bijvoorbeeld met Beeline van een clientsysteem `clustername.azurehdinsight.net` om verbinding te maken met het adres. Deze verbinding wordt `443`gemaakt via de poort en wordt versleuteld met TLS/SSL.

Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `admin` door het clusteraanmeldingsaccount voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wachtwoord voor het clusteraanmeldingsaccount.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

of voor privéeindpunt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Privéeindpunten wijzen naar een basisloadbalancer, die alleen toegankelijk is vanaf de VNET's die in dezelfde regio zijn peered. Zie [beperkingen voor wereldwijde VNet-peering- en loadbalancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U `curl` de `-v` opdracht met optie gebruiken om verbindingsproblemen met openbare of privéeindpunten op te lossen voordat u beeline gebruikt.

---

### <a name="use-beeline-with-apache-spark"></a>Beeline gebruiken met Apache Spark

Apache Spark biedt een eigen implementatie van HiveServer2, die soms wordt aangeduid als de Spark Thrift-server. Deze service gebruikt Spark SQL om query's op te lossen in plaats van Hive en biedt mogelijk betere prestaties, afhankelijk van uw query.

#### <a name="through-public-or-private-endpoints"></a>Via publieke of private eindpunten

De gebruikte verbindingstekenreeks is iets anders. In plaats `httpPath=/hive2` van het `httpPath/sparkhive2`in te dammen is. Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `admin` door het clusteraanmeldingsaccount voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wachtwoord voor het clusteraanmeldingsaccount.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

of voor privéeindpunt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Privéeindpunten wijzen naar een basisloadbalancer, die alleen toegankelijk is vanaf de VNET's die in dezelfde regio zijn peered. Zie [beperkingen voor wereldwijde VNet-peering- en loadbalancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U `curl` de `-v` opdracht met optie gebruiken om verbindingsproblemen met openbare of privéeindpunten op te lossen voordat u beeline gebruikt.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Van clusterkop of binnen Azure Virtual Network met Apache Spark

Wanneer u rechtstreeks verbinding maakt vanaf het clusterhoofdknooppunt of vanuit een bron in `10002` hetzelfde Azure Virtual Network `10001`als het HDInsight-cluster, moet de poort worden gebruikt voor spark thrift-server in plaats van . In het volgende voorbeeld ziet u hoe u rechtstreeks verbinding maken met het hoofdknooppunt:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Voorwaarden voor voorbeelden

* Een Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Let op het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw cluster. Bijvoorbeeld `wasb://` voor Azure Storage, `abfs://` Azure Data Lake Storage `adl://` Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure `wasbs://`Storage, is de URI . Zie [veilige overdracht](../../storage/common/storage-require-secure-transfer.md)voor meer informatie.

* Optie 1: Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). De meeste stappen in dit document gaan ervan uit dat u Beeline gebruikt van een SSH-sessie naar het cluster.

* Optie 2: Een lokale Beeline-client.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Dit voorbeeld is gebaseerd op het gebruik van de Beeline-client van een SSH-verbinding.

1. Open een SSH-verbinding met het cluster met de onderstaande code. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Voer desgevraagd het wachtwoord voor het SSH-gebruikersaccount in.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Maak verbinding met HiveServer2 met uw Beeline-client vanuit uw geopende SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-opdrachten beginnen `!` met een `!help` teken, bijvoorbeeld geeft hulp weer. Nochtans `!` kan het voor sommige bevelen worden weggelaten. Bijvoorbeeld, `help` werkt ook.

    Er is `!sql`, die wordt gebruikt om HiveQL verklaringen uit te voeren. HiveQL wordt echter zo vaak gebruikt dat u `!sql`de voorgaande weglaten. De volgende twee verklaringen zijn gelijkwaardig:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In een nieuw cluster wordt slechts één tabel weergegeven: **hivesampletable**.

4. Gebruik de volgende opdracht om het schema voor de hivesampletable weer te geven:

    ```hiveql
    describe hivesampletable;
    ```

    Met deze opdracht worden de volgende gegevens geretourneerd:

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

    Deze informatie beschrijft de kolommen in de tabel.

5. Voer de volgende instructies in om een tabel met de naam **log4jLogs** te maken met behulp van voorbeeldgegevens die zijn geleverd bij het HDInsight-cluster: (Indien nodig herzien op basis van uw [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Deze verklaringen doen de volgende acties:

    |Verklaring |Beschrijving |
    |---|---|
    |KEUZELIJST LATEN VALLEN|Als de tabel bestaat, wordt deze verwijderd.|
    |EXTERNE TABEL MAKEN|Hiermee maakt u een **externe** tabel in Hive. Externe tabellen slaan alleen de tabeldefinitie op in Hive. De gegevens worden op de oorspronkelijke locatie achtergelaten.|
    |RIJNOTATIE|Hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
    |OPGESLAGEN ALS TEXTFILE-LOCATIE|Waar de gegevens worden opgeslagen en in welke bestandsindeling.|
    |SELECT|Hiermee selecteert u een aantal rijen waarin kolom **t4** de waarde **bevat [FOUT]**. Met deze query wordt een waarde van **3** geretourneerd, aangezien er drie rijen zijn die deze waarde bevatten.|
    |INPUT__FILE__NAME VIND '.log' leuk|Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om te voorkomen dat garbage data in de resultaten, deze verklaring vertelt Hive dat het alleen gegevens moet retourneren van bestanden die eindigen in .log.|

   > [!NOTE]  
   > Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een mapreduce-bewerking.
   >
   > Als u een externe tabel laat vallen, worden de gegevens **niet** verwijderd, alleen de tabeldefinitie.

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

6. Afrit Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Een HiveQL-bestand uitvoeren

Dit is een voortzetting van het eerdere voorbeeld. Gebruik de volgende stappen om een bestand te maken en voer het vervolgens uit met Beeline.

1. Gebruik de volgende opdracht om een bestand met de naam **query.hql**te maken:

    ```bash
    nano query.hql
    ```

1. Gebruik de volgende tekst als de inhoud van het bestand. Met deze query wordt een nieuwe 'interne' tabel met de naam **errorLogs gemaakt:**

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze verklaringen doen de volgende acties:

    |Verklaring |Beschrijving |
    |---|---|
    |TABEL MAKEN ALS DEZE NIET BESTAAT|Als de tabel nog niet bestaat, wordt deze gemaakt. Omdat het **externe** trefwoord niet wordt gebruikt, wordt met deze instructie een interne tabel gemaakt. Interne tabellen worden opgeslagen in het Hive data warehouse en worden volledig beheerd door Hive.|
    |OPGESLAGEN ALS ORC|Slaat de gegevens op in de ORC-indeling (Optimized Row Columnar). ORC-formaat is een zeer geoptimaliseerd en efficiënt formaat voor het opslaan van Hive-gegevens.|
    |INSERT OVERSCHRIJVEN ... Selecteer|Hiermee selecteert u rijen uit de **log4jLogs-tabel** die **[FOUT]** bevatten en worden de gegevens vervolgens ingevoegd in de **tabel errorLogs.**|

    > [!NOTE]  
    > In tegenstelling tot externe tabellen verwijdert het laten vallen van een interne tabel ook de onderliggende gegevens.

1. Als u het bestand wilt opslaan, gebruikt u **Ctrl**+**X,** voert u **Y**in en voert u uiteindelijk **Enter in**.

1. Gebruik het volgende om het bestand uit te voeren met Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > De `-i` parameter start Beeline en `query.hql` voert de instructies uit in het bestand. Zodra de query is voltooid, `jdbc:hive2://headnodehost:10001/>` komt u bij de prompt. U ook een `-f` bestand uitvoeren met de parameter, waarmee Beeline wordt afgesloten nadat de query is voltooid.

1. Als u wilt controleren of de **tabel errorLogs** is gemaakt, gebruikt u de volgende instructie om alle rijen van **foutlogboeken**terug te sturen:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Drie rijen gegevens moeten worden geretourneerd, die allemaal **[FOUT]** bevatten in kolom t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Beeline-client installeren

Hoewel Beeline is opgenomen op de hoofdknooppunten van uw HDInsight-cluster, u deze op een lokale machine installeren.  De onderstaande stappen om Beeline op een lokale machine te installeren, zijn gebaseerd op een [Windows-subsysteem voor Linux.](https://docs.microsoft.com/windows/wsl/install-win10)

1. Pakketlijsten bijwerken. Voer de volgende opdracht in uw bashshell in:

    ```bash
    sudo apt-get update
    ```

1. Installeer Java als deze niet is geïnstalleerd. Je het `which java` navragen bij het commando.

    1. Als er geen java-pakket is geïnstalleerd, voert u de volgende opdracht in:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Open het bashrc-bestand (meestal te vinden `nano ~/.bashrc`in ~/.bashrc): .

    1. Wijzig het bashrc-bestand. Voeg de volgende regel toe aan het einde van het bestand:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Druk vervolgens op **Ctrl+X**, vervolgens **Op Y**en voer vervolgens in.

1. Download hadoop- en beeline-archieven en voer de volgende opdrachten in:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Pak de archieven uit, voer de volgende opdrachten in:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Verder wijzigen van de bashrc bestand. U moet het pad identificeren naar waar de archieven zijn uitgepakt. Als u het [Windows-subsysteem voor Linux](https://docs.microsoft.com/windows/wsl/install-win10)gebruikt en `/mnt/c/Users/user/`u `user` de stappen precies hebt gevolgd, zou uw pad zijn, waar is uw gebruikersnaam.

    1. Open het bestand:`nano ~/.bashrc`

    1. Wijzig de onderstaande opdrachten met het juiste pad en voer ze vervolgens in aan het einde van het bashrc-bestand:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Druk vervolgens op **Ctrl+X**, vervolgens **Op Y**en voer vervolgens in.

1. Sluit en open je bash sessie.

1. Test je verbinding. Gebruik de verbindingsindeling van [Bovenstaande openbare of privé-eindpunten.](#over-public-or-private-endpoints)

## <a name="next-steps"></a>Volgende stappen

* Zie [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md) voor meer algemene informatie over Hive in HDInsight

* Zie [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md) voor meer informatie over andere manieren waarop u met Hadoop werken.
