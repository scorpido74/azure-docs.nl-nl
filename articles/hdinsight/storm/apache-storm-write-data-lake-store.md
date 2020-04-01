---
title: 'Zelfstudie: HDInsight Apache Storm to Storage - Azure/Data Lake'
description: Zelfstudie - Leer hoe u Apache Storm gebruiken om te schrijven naar de HDFS-compatibele opslag voor Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241206"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Zelfstudie: Write to Apache Hadoop HDFS from Apache Storm on Azure HDInsight

Deze zelfstudie laat zien hoe je Apache Storm gebruiken om gegevens te schrijven naar de HDFS-compatibele opslag die wordt gebruikt door Apache Storm op HDInsight. HDInsight kan zowel Azure Storage als Azure Data Lake Storage gebruiken als HDFS-compatibele opslag. Storm biedt een [HdfsBolt-component](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) die gegevens naar HDFS schrijft. Dit document bevat informatie over het schrijven van beide typen opslag van de HdfsBolt.

Het voorbeeld topologie dat in dit document wordt gebruikt, is gebaseerd op componenten die zijn opgenomen bij Storm op HDInsight. Het kan worden gewijzigd om te werken met Azure Data Lake Storage wanneer deze wordt gebruikt met andere Apache Storm-clusters.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het cluster configureren met scriptactie
> * De topologie bouwen en verpakken
> * De topologie implementeren en uitvoeren
> * Uitvoergegevens weergeven
> * De topologie stoppen

## <a name="prerequisites"></a>Vereisten

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit geldt `wasb://` voor Azure `abfs://` Storage, Azure Data `adl://` Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure `wasbs://`Storage, is de URI .  Zie ook, [veilige overdracht](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Voorbeeldconfiguratie

De volgende YAML is `resources/writetohdfs.yaml` een fragment uit het bestand in het voorbeeld. Dit bestand definieert de Stormtopologie met behulp van het [Flux-framework](https://storm.apache.org/releases/current/flux.html) voor Apache Storm.

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

* `syncPolicy`: Hiermee bepaalt u wanneer bestanden worden gesynchroniseerd/doorgespoeld naar het bestandssysteem. In dit voorbeeld, elke 1000 tuples.
* `fileNameFormat`: Hiermee definieert u het pad en het bestandsnaampatroon dat moet worden gebruikt bij het schrijven van bestanden. In dit voorbeeld wordt het pad tijdens runtime geleverd met `.txt`behulp van een filter en is de bestandsextensie .
* `recordFormat`: Definieert de interne indeling van de geschreven bestanden. In dit voorbeeld worden velden `|` afgebakend door het teken.
* `rotationPolicy`: Hiermee bepaalt u wanneer bestanden moeten worden geroterd. In dit voorbeeld wordt geen rotatie uitgevoerd.
* `hdfs-bolt`: Gebruikt de vorige componenten `HdfsBolt` als configuratieparameters voor de klasse.

Zie voor meer informatie over [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)het Flux framework .

## <a name="configure-the-cluster"></a>Het cluster configureren

Storm op HDInsight bevat standaard niet `HdfsBolt` de componenten die worden gebruikt om te communiceren met Azure Storage of Data Lake Storage in het klassenpad van Storm. Gebruik de volgende scriptactie om `extlib` deze componenten toe te voegen aan de map voor Storm op uw cluster:

| Eigenschap | Waarde |
|---|---|
|Scripttype |- Aangepast|
|Bash script URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Knooppunttype(s) |Nimbus, Supervisor|
|Parameters |Geen|

Zie de [clusters HDInsight aanpassen met behulp van scriptacties](./../hdinsight-hadoop-customize-cluster-linux.md) voor informatie over het gebruik van dit script met uw cluster.

## <a name="build-and-package-the-topology"></a>De topologie bouwen en verpakken

1. Download het voorbeeldproject van [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) uw ontwikkelomgeving.

2. Verander mappen van een opdrachtprompt, terminal of shell-sessie naar de basis van het gedownloade project. Als u de topologie wilt maken en verpakken, gebruikt u de volgende opdracht:

    ```cmd
    mvn compile package
    ```

    Zodra de build en verpakking is voltooid, `target`is er een `StormToHdfs-1.0-SNAPSHOT.jar`nieuwe map met de naam , die een bestand met de naam bevat. Dit bestand bevat de gecompileerde topologie.

## <a name="deploy-and-run-the-topology"></a>De topologie implementeren en uitvoeren

1. Gebruik de volgende opdracht om de topologie naar het CLUSTER HDInsight te kopiëren. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Zodra de upload is voltooid, gebruikt u het volgende om verbinding te maken met het HDInsight-cluster met Behulp van SSH. Vervang `CLUSTERNAME` door de naam van het cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Zodra u verbinding hebt gemaakt, gebruikt `dev.properties`u de volgende opdracht om een bestand met de naam te maken:

    ```bash
    nano dev.properties
    ```

1. Gebruik de volgende tekst als `dev.properties` de inhoud van het bestand. Herzien indien nodig op basis van uw [URI-schema.](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Als u het bestand wilt opslaan, gebruikt u __Ctrl + X,__ vervolgens __Y__en ten slotte __Enter__. De waarden in dit bestand stellen de opslag-URL en de mapnaam in waarnaar gegevens zijn geschreven.

1. Gebruik de volgende opdracht om de topologie te starten:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Met deze opdracht wordt de topologie gestart met behulp van het Flux-framework door deze in te dienen bij het Nimbus-knooppunt van het cluster. De topologie wordt gedefinieerd `writetohdfs.yaml` door het bestand in de pot. Het `dev.properties` bestand wordt doorgegeven als een filter en de waarden in het bestand worden gelezen door de topologie.

## <a name="view-output-data"></a>Uitvoergegevens weergeven

Als u de gegevens wilt weergeven, gebruikt u de volgende opdracht:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Er wordt een lijst weergegeven met de bestanden die door deze topologie zijn gemaakt. De volgende lijst is een voorbeeld van de gegevens die door de vorige opdrachten zijn geretourneerd:

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

Stormtopologieën worden uitgevoerd tot deze zijn gestopt of het cluster wordt verwijderd. Als u de topologie wilt stoppen, gebruikt u de volgende opdracht:

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

In deze zelfstudie hebt u geleerd hoe u Apache Storm gebruiken om gegevens te schrijven naar de HDFS-compatibele opslag die door Apache Storm op HDInsight wordt gebruikt.

> [!div class="nextstepaction"]
> Ontdek andere [Apache Storm voorbeelden voor HDInsight](apache-storm-example-topology.md)