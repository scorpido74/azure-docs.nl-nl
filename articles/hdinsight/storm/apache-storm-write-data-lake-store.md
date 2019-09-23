---
title: Zelf studie-Apache Storm schrijf bewerkingen naar opslag/Data Lake Storage-Azure HDInsight
description: Zelf studie-informatie over het gebruik van Apache Storm om te schrijven naar de HDFS-compatibele opslag voor Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: b6114a764d0834b7bcfe4b95d34fae6a03a8a40e
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181018"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Zelfstudie: Schrijven naar Apache Hadoop HDFS van Apache Storm in azure HDInsight

In deze zelf studie ziet u hoe u Apache Storm kunt gebruiken om gegevens te schrijven naar de met HDFS compatibele opslag die door Apache Storm op HDInsight wordt gebruikt. HDInsight kan zowel Azure Storage als Azure Data Lake Storage als HDFS-compatibele opslag gebruiken. Storm biedt een [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) -onderdeel dat gegevens naar HDFS schrijft. Dit document bevat informatie over het schrijven naar elk type opslag van de HdfsBolt.

De voor beeld-topologie die in dit document wordt gebruikt, is afhankelijk van onderdelen die zijn opgenomen in Storm op HDInsight. Het kan nodig zijn om met Azure Data Lake Storage te werken wanneer het wordt gebruikt met andere Apache Storm clusters.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het cluster met een script actie configureren
> * De topologie bouwen en inpakken
> * De topologie implementeren en uitvoeren
> * Uitvoer gegevens weer geven
> * De topologie stoppen

## <a name="prerequisites"></a>Vereisten

* [JDK-versie 8 (Java Developer Kit)](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) is op de juiste wijze [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project voor het maken van een systeem voor Java-projecten.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit is `wasb://` voor Azure Storage, `abfs://` voor Azure data Lake Storage Gen2 of `adl://` voor Azure data Lake Storage gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage, zou de URI zijn `wasbs://`.  Zie ook [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Voorbeeldconfiguratie

De volgende YAML is een uittreksel uit het `resources/writetohdfs.yaml` bestand dat in het voor beeld is opgenomen. Dit bestand definieert de Storm-topologie met behulp van het [stroom](https://storm.apache.org/releases/current/flux.html) raamwerk voor Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Deze YAML definieert de volgende items:

* `syncPolicy`: Hiermee wordt gedefinieerd wanneer bestanden worden gesynchroniseerd/verwijderd naar het bestands systeem. In dit voor beeld worden elke 1000 Tuples.
* `fileNameFormat`: Hiermee definieert u het pad en de bestands naam patroon die moeten worden gebruikt bij het schrijven van bestanden. In dit voor beeld wordt het pad gegeven tijdens runtime met behulp van een filter en de bestands `.txt`extensie.
* `recordFormat`: Hiermee wordt de interne indeling van de geschreven bestanden gedefinieerd. In dit voor beeld worden velden gescheiden door het `|` teken.
* `rotationPolicy`: Hiermee wordt gedefinieerd wanneer bestanden worden gedraaid. In dit voor beeld wordt er geen draaiing uitgevoerd.
* `hdfs-bolt`: Gebruikt de vorige onderdelen als configuratie parameters voor de `HdfsBolt` klasse.

Zie [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)voor meer informatie over het stroom kader.

## <a name="configure-the-cluster"></a>Het cluster configureren

Storm op HDInsight bevat standaard geen onderdelen die `HdfsBolt` worden gebruikt om te communiceren met Azure Storage of Data Lake Storage in het klassenpad van Storm. Gebruik de volgende script actie om deze onderdelen toe te voegen aan de `extlib` Directory voor Storm op het cluster:

| Eigenschap | Value |
|---|---|
|Scripttype |-Aangepast|
|Bash-script-URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Knooppunt type (n) |Nimbus, Supervisor|
|Parameters |Geen|

Zie het document [HDInsight-clusters aanpassen met script acties](./../hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie over het gebruik van dit script met uw cluster.

## <a name="build-and-package-the-topology"></a>De topologie bouwen en inpakken

1. Down load het voorbeeld project [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) van naar uw ontwikkel omgeving.

2. Wijzig vanuit een opdracht prompt, Terminal of shell-sessie de mappen in de hoofdmap van het gedownloade project. Gebruik de volgende opdracht om de topologie te bouwen en in te pakken:

    ```cmd
    mvn compile package
    ```

    Zodra het build-en pakket is voltooid, is er een nieuwe map `target`met de naam, die een `StormToHdfs-1.0-SNAPSHOT.jar`bestand bevat met de naam. Dit bestand bevat de gecompileerde topologie.

## <a name="deploy-and-run-the-topology"></a>De topologie implementeren en uitvoeren

1. Gebruik de volgende opdracht om de topologie naar het HDInsight-cluster te kopiëren. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Nadat het uploaden is voltooid, gebruikt u de volgende om via SSH verbinding te maken met het HDInsight-cluster. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Als de verbinding tot stand is gebracht, gebruikt u de volgende `dev.properties`opdracht om een bestand te maken met de naam:

    ```bash
    nano dev.properties
    ```

1. Gebruik de volgende tekst als de inhoud van het `dev.properties` bestand. Wijzig indien nodig op basis van uw [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Gebruik __CTRL + X__, vervolgens __Y__en tenslotte __Enter__om het bestand op te slaan. De waarden in dit bestand instellen de opslag-URL en de naam van de map waarnaar gegevens worden geschreven.

1. Gebruik de volgende opdracht om de topologie te starten:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Met deze opdracht wordt de topologie gestart met het stroom raamwerk door deze te verzenden naar het Nimbus-knoop punt van het cluster. De topologie wordt gedefinieerd door het `writetohdfs.yaml` bestand dat in het jar is opgenomen. Het `dev.properties` bestand wordt door gegeven als een filter en de waarden in het bestand worden door de topologie gelezen.

## <a name="view-output-data"></a>Uitvoer gegevens weer geven

Als u de gegevens wilt weer geven, gebruikt u de volgende opdracht:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Er wordt een lijst weer gegeven met de bestanden die door deze topologie zijn gemaakt. De volgende lijst bevat een voor beeld van de gegevens die door de vorige opdrachten worden geretourneerd:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>De topologie stoppen

Storm-topologieën worden uitgevoerd tot deze worden gestopt of het cluster wordt verwijderd. Gebruik de volgende opdracht om de topologie te stoppen:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende HDInsight-cluster en eventuele andere resources die aan de resourcegroep zijn gekoppeld, verwijderd.

Ga als volgt te werk om de resourcegroep te verwijderen in Azure Portal:

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies __Resourcegroepen__ om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die u wilt verwijderen en klik met de rechtermuisknop op de knop __Meer__ (... ) aan de rechterkant van de vermelding.
3. Selecteer __Resourcegroep verwijderen__ en bevestig dit.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Apache Storm kunt gebruiken om gegevens te schrijven naar de met HDFS compatibele opslag die door Apache Storm op HDInsight wordt gebruikt.

> [!div class="nextstepaction"]
> Andere [Apache Storm-voor beelden voor HDInsight](apache-storm-example-topology.md) ontdekken