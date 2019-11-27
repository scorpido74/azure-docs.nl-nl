---
title: Apache Beeline gebruiken met Apache Hive-Azure HDInsight
description: Meer informatie over het gebruik van de Beeline-client om Hive-query's uit te voeren met Hadoop op HDInsight. Beeline is een hulp programma voor het werken met HiveServer2 over JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 26a166e61086af8cf10f761b608fcf66eb8734fd
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406252"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>De Apache Beeline-client gebruiken met Apache Hive

Meer informatie over het gebruik van [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) om Apache Hive-query's uit te voeren op HDInsight.

Beeline is een Hive-client die is opgenomen op de hoofd knooppunten van uw HDInsight-cluster. Beeline maakt gebruik van JDBC om verbinding te maken met HiveServer2, een service die wordt gehost op uw HDInsight-cluster. U kunt Beeline ook gebruiken voor toegang tot de component op HDInsight op afstand via internet. De volgende voor beelden bieden de meest voorkomende verbindings reeksen die worden gebruikt om verbinding te maken met HDInsight vanuit beeline:

## <a name="types-of-connections"></a>Typen verbindingen

### <a name="from-an-ssh-session"></a>Vanuit een SSH-sessie

Wanneer u verbinding maakt vanuit een SSH-sessie met een cluster hoofd knooppunt, kunt u vervolgens verbinding maken met het `headnodehost`-adres op poort `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Via een Azure-Virtual Network

Wanneer u vanaf een client verbinding maakt met HDInsight via een Azure-Virtual Network, moet u de Fully Qualified Domain Name (FQDN) van het hoofd knooppunt van het cluster opgeven. Omdat deze verbinding rechtstreeks met de cluster knooppunten tot stand is gebracht, gebruikt de verbinding poort `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Vervang `<headnode-FQDN>` door het Fully Qualified Domain Name van een cluster hoofd knooppunt. Als u de Fully Qualified Domain Name van een hoofd knooppunt wilt vinden, gebruikt u de informatie in het bestand [HDInsight beheren met behulp van het Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) -document.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Naar HDInsight Enterprise Security Package (ESP)-cluster met behulp van Kerberos

Wanneer u verbinding maakt tussen een client en een Enterprise Security Package (ESP)-cluster dat is gekoppeld aan Azure Active Directory (AAD)-DS op een computer in dezelfde realm van het cluster, moet u ook de domein naam `<AAD-Domain>` en de naam van een domein gebruikers account opgeven met machtigingen voor toegang tot het cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Vervang `<username>` door de naam van een account in het domein met machtigingen voor toegang tot het cluster. Vervang `<AAD-DOMAIN>` door de naam van de Azure Active Directory (AAD) waaraan het cluster is gekoppeld. Gebruik een teken reeks met hoofd letters voor de `<AAD-DOMAIN>` waarde, anders wordt de referentie niet gevonden. Controleer zo nodig `/etc/krb5.conf` voor de realm-namen.

---

### <a name="over-public-or-private-endpoints"></a>Via open bare of persoonlijke eind punten

Wanneer u verbinding maakt met een cluster met behulp van de open bare of persoonlijke eind punten, moet u de naam van het cluster aanmeldings account (standaard `admin`) en het wacht woord opgeven. Bijvoorbeeld, met behulp van Beeline van een-client systeem om verbinding te maken met het `<clustername>.azurehdinsight.net` adres. Deze verbinding wordt tot stand gebracht via poort `443`en is versleuteld met SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `<username>` door het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wacht woord voor het account voor het aanmelden bij het cluster.

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de `curl` opdracht met `-v` optie om verbindings problemen met open bare of persoonlijke eind punten op te lossen voordat u Beeline gebruikt.

---

### <a id="sparksql"></a>Beeline gebruiken met Apache Spark

Apache Spark biedt een eigen implementatie van HiveServer2, die ook wel de Spark Thrift-server wordt genoemd. Deze service maakt gebruik van Spark SQL om query's op te lossen in plaats van Hive, en kan betere prestaties leveren, afhankelijk van uw query.

#### <a name="through-public-or-private-endpoints"></a>Via open bare of privé-eind punten

Het gebruikte connection string is iets anders. In plaats van `httpPath=/hive2` `httpPath/sparkhive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang `<username>` door het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com). Vervang `password` door het wacht woord voor het account voor het aanmelden bij het cluster.

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de `curl` opdracht met `-v` optie om verbindings problemen met open bare of persoonlijke eind punten op te lossen voordat u Beeline gebruikt.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Van cluster kop of binnen Azure Virtual Network met Apache Spark

Wanneer u rechtstreeks verbinding maakt met het hoofd knooppunt van het cluster of van een resource binnen hetzelfde Azure-Virtual Network als het HDInsight-cluster, moet poort `10002` worden gebruikt voor Spark Thrift-server in plaats van `10001`. In het volgende voor beeld ziet u hoe u rechtstreeks verbinding maakt met het hoofd knooppunt:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Let op het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw cluster. Bijvoorbeeld `wasb://` voor Azure Storage, `abfs://` voor Azure Data Lake Storage Gen2 of `adl://` voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, wordt de URI `wasbs://`. Zie [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md)voor meer informatie.

* Optie 1: een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Bij de meeste stappen in dit document wordt ervan uitgegaan dat u Beeline gebruikt vanuit een SSH-sessie naar het cluster.

* Optie 2: een lokale Beeline-client.

## <a id="beeline"></a>Een Hive-query uitvoeren

Dit voor beeld is gebaseerd op het gebruik van de Beeline-client van een SSH-verbinding.

1. Open een SSH-verbinding met het cluster met de onderstaande code. Vervang `sshuser` door de SSH-gebruiker voor uw cluster en `CLUSTERNAME` door de naam van het cluster. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor het SSH-gebruikers account in.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Maak verbinding met HiveServer2 met uw Beeline-client vanuit uw open SSH-sessie door de volgende opdracht in te voeren:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-opdrachten beginnen met een `!` teken, bijvoorbeeld `!help` Help weer gegeven. De `!` kan echter voor sommige opdrachten worden wegge laten. `help` werkt bijvoorbeeld ook.

    Er is `!sql`, die wordt gebruikt om HiveQL-instructies uit te voeren. HiveQL wordt echter vaak gebruikt, zodat u de voor gaande `!sql`kunt weglaten. De volgende twee instructies zijn gelijk:

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

5. Voer de volgende instructies in om een tabel met de naam **log4jLogs** te maken met behulp van voorbeeld gegevens die zijn opgegeven met het HDInsight-cluster: (indien nodig herzien op basis van uw [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme)).

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

    * `DROP TABLE`-als de tabel bestaat, wordt deze verwijderd.

    * `CREATE EXTERNAL TABLE`-maakt een **externe** tabel in Hive. Externe tabellen slaan de tabel definitie in Hive alleen op. De gegevens blijven op de oorspronkelijke locatie.

    * `ROW FORMAT`: de indeling van de gegevens. In dit geval worden de velden in elk logboek gescheiden door een spatie.

    * `STORED AS TEXTFILE LOCATION`-waar de gegevens worden opgeslagen en in welke bestands indeling.

    * `SELECT`: Hiermee selecteert u een telling van alle rijen waarin de kolom **T4** de waarde **[error]** bevat. Deze query retourneert de waarde **3** als er drie rijen met deze waarde zijn.

    * `INPUT__FILE__NAME LIKE '%.log'`-Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om garbage-gegevens in de resultaten te voor komen, geeft deze instructie aan dat de component alleen gegevens moet retour neren van bestanden die eindigen op. log.

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

6. Gebruik `!exit`om Beeline af te sluiten.

## <a id="file"></a>Een HiveQL-bestand uitvoeren

Dit is een voortzetting van het vorige voor beeld. Gebruik de volgende stappen om een bestand te maken en voer het vervolgens uit met Beeline.

1. Gebruik de volgende opdracht om een bestand met de naam **query. HQL**te maken:

    ```bash
    nano query.hql
    ```

2. Gebruik de volgende tekst als de inhoud van het bestand. Met deze query maakt u een nieuwe interne tabel met de naam **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Deze instructies doen de volgende acties:

   * **Create Table als het niet bestaat** : als de tabel nog niet bestaat, wordt deze gemaakt. Omdat het **externe** tref woord niet wordt gebruikt, maakt deze instructie een interne tabel. Interne tabellen worden opgeslagen in het Hive-Data Warehouse en worden volledig beheerd door Hive.
   * **Opgeslagen als Orc** : Hiermee worden de gegevens opgeslagen in de indeling van de geoptimaliseerde rij in kolommen (Orc). De ORC-indeling is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.
   * **overschrijven invoegen... SELECT** -rijen selecteren in de **tabel log4jLogs** die **[error]** bevatten en vervolgens de gegevens invoegen in de tabel **errorLogs** .

    > [!NOTE]  
    > In tegens telling tot externe tabellen, verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

3. Als u het bestand wilt opslaan, gebruikt u **Ctrl**+**X**en voert u vervolgens **Y**en tenslotte **Enter**.

4. Gebruik het volgende om het bestand uit te voeren met beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Met de para meter `-i` start u Beeline en voert u de instructies in het `query.hql`-bestand uit. Zodra de query is voltooid, ontvangt u de `jdbc:hive2://headnodehost:10001/>` prompt. U kunt ook een bestand uitvoeren met de para meter `-f`, die Beeline verlaat nadat de query is voltooid.

5. Als u wilt controleren of de **errorLogs** -tabel is gemaakt, gebruikt u de volgende instructie om alle rijen uit **errorLogs**te retour neren:

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

## <a id="summary"></a><a id="nextsteps"></a>Volgende stappen

* Zie [Apache Hive gebruiken met Apache Hadoop in hdinsight](hdinsight-use-hive.md) voor meer algemene informatie over Hive in hdinsight

* Zie [MapReduce gebruiken met Apache Hadoop op hdinsight](hdinsight-use-mapreduce.md) voor meer informatie over andere manieren waarop u met Hadoop in hdinsight kunt werken.
