---
title: Apache Storm met python-onderdelen-Azure HDInsight
description: Meer informatie over het maken van een Apache Storm topologie die gebruikmaakt van python-onderdelen in azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache Storm python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a15506632e90edae235c3d1889603ca4997a3398
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813891"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Apache Storm topologieën ontwikkelen met behulp van python in HDInsight

Meer informatie over het maken van een [Apache Storm](https://storm.apache.org/) topologie die gebruikmaakt van python-onderdelen. Apache Storm ondersteunt meerdere talen, waardoor u zelfs onderdelen uit verschillende talen in één topologie kunt combi neren. Met het [stroom](https://storm.apache.org/releases/current/flux.html) kader (geïntroduceerd in Storm 0.10.0) kunt u eenvoudig oplossingen maken die gebruikmaken van python-onderdelen.

> [!IMPORTANT]  
> De informatie in dit document is getest met Storm op HDInsight 3,6. 

De code voor dit project is beschikbaar op [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Vereisten

* Python 2,7 of hoger

* Java JDK 1,8 of hoger

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Beschrijving Een lokale Storm-ontwikkel omgeving. Een lokale Storm-omgeving is alleen nodig als u de topologie lokaal wilt uitvoeren. Zie [een ontwikkel omgeving instellen](http://storm.apache.org/releases/current/Setting-up-development-environment.html)voor meer informatie.

## <a name="storm-multi-language-support"></a>Ondersteuning voor Storm meerdere talen

Apache Storm is ontworpen om te werken met onderdelen die zijn geschreven met behulp van een programmeer taal. De onderdelen moeten weten hoe u kunt werken met de [Thrift-definitie voor Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Voor python wordt een module meegeleverd als onderdeel van het Apache Storm project, waarmee u eenvoudig kunt interface met storm. U kunt deze module vinden op [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm is een Java-proces dat wordt uitgevoerd op de Java Virtual Machine (JVM). Onderdelen die in andere talen zijn geschreven, worden uitgevoerd als subprocessen. De Storm communiceert met deze subprocessen met behulp van JSON-berichten die via stdin/stdout worden verzonden. Meer informatie over de communicatie tussen onderdelen vindt u in de documentatie over het [multi-lang protocol](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python met het stroom kader

Met het stroom raamwerk kunt u Storm-topologieën afzonderlijk van de onderdelen definiëren. Het stroom raamwerk gebruikt YAML om de Storm-topologie te definiëren. De volgende tekst is een voor beeld van het verwijzen naar een python-onderdeel in het YAML-document:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

De klasse `FluxShellSpout` wordt gebruikt om het `sentencespout.py` script te starten dat de Spout implementeert.

Stroom verwacht dat de python-scripts zich bevinden in de `/resources` map in het jar-bestand dat de topologie bevat. In dit voor beeld worden de python-scripts `/multilang/resources` in de Directory opgeslagen. Het `pom.xml` bevat dit bestand met de volgende XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Zoals eerder vermeld, is er een `storm.py` bestand dat de Thrift-definitie voor Storm implementeert. Het stroom kader wordt `storm.py` automatisch opgenomen wanneer het project is gemaakt, zodat u zich geen zorgen hoeft te maken over het opnemen ervan.

## <a name="build-the-project"></a>Het project bouwen

Gebruik in de hoofdmap van het project de volgende opdracht:

```bash
mvn clean compile package
```

Met deze opdracht maakt `target/WordCount-1.0-SNAPSHOT.jar` u een bestand met de gecompileerde topologie.

## <a name="run-the-topology-locally"></a>De topologie lokaal uitvoeren

Als u de topologie lokaal wilt uitvoeren, gebruikt u de volgende opdracht:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Voor deze opdracht is een lokale Storm-ontwikkel omgeving vereist. Zie [een ontwikkel omgeving instellen](https://storm.apache.org/releases/current/Setting-up-development-environment.html) voor meer informatie

Zodra de topologie is gestart, wordt er informatie verzonden naar de lokale console, vergelijkbaar met de volgende tekst:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Gebruik __CTRL + C__om de topologie te stoppen.

## <a name="run-the-storm-topology-on-hdinsight"></a>De Storm-topologie op HDInsight uitvoeren

1. Gebruik de volgende opdracht om het `WordCount-1.0-SNAPSHOT.jar` bestand te kopiëren naar uw Storm op HDInsight-cluster:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Vervang `sshuser` door de SSH-gebruiker voor uw cluster. Vervang `mycluster` door de naam van het cluster. U wordt mogelijk gevraagd het wacht woord voor de SSH-gebruiker in te voeren.

    Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van SSH en SCP.

2. Nadat het bestand is geüpload, maakt u met SSH verbinding met het cluster:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Gebruik in de SSH-sessie de volgende opdracht om de topologie op het cluster te starten:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. U kunt de Storm-gebruikers interface gebruiken om de topologie op het cluster weer te geven. De Storm-gebruikers interface bevindt zich op https://mycluster.azurehdinsight.net/stormui. Vervang `mycluster` door de naam van uw cluster.

> [!NOTE]  
> Zodra het is gestart, wordt een storm-topologie uitgevoerd totdat deze wordt gestopt. Gebruik een van de volgende methoden om de topologie te stoppen:
>
> * De `storm kill TOPOLOGYNAME` opdracht vanaf de opdracht regel
> * De knop **Kill** in de gebruikers interface van Storm.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor andere manieren om python te gebruiken met HDInsight:

* [Het gebruik van python door de gebruiker gedefinieerde functies (UDF) in Apache varken en Apache Hive](../hadoop/python-udf-hdinsight.md)
