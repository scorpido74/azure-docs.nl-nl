---
title: Apache Storm met Python-componenten - Azure HDInsight
description: Meer informatie over het maken van een Apache Storm-topologie die Python-componenten gebruikt in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460021"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Ontwikkel Apache Storm topologieën met Python op HDInsight

Meer informatie over het maken van een [Apache Storm-topologie](https://storm.apache.org/) die python-componenten gebruikt. Apache Storm ondersteunt meerdere talen, zodat u zelfs componenten uit verschillende talen in één topologie combineren. Met het [Flux-framework](https://storm.apache.org/releases/current/flux.html) (geïntroduceerd met Storm 0.10.0) u eenvoudig oplossingen maken die gebruikmaken van Python-componenten.

> [!IMPORTANT]  
> De informatie in dit document is getest met Storm op HDInsight 3.6.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en Selecteer **Storm** voor **clustertype**.

* Een lokale ontwikkelomgeving van Storm (optioneel). Een lokale Storm-omgeving is alleen nodig als u de topologie lokaal wilt uitvoeren. Zie [Een ontwikkelomgeving instellen](https://storm.apache.org/releases/current/Setting-up-development-environment.html)voor meer informatie.

* [Python 2.7 of hoger](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.

## <a name="storm-multi-language-support"></a>Ondersteuning voor storm in meerdere talen

Apache Storm is ontworpen om te werken met componenten geschreven met behulp van een programmeertaal. De componenten moeten begrijpen hoe te werken met de Thrift-definitie voor Storm. Voor Python wordt een module geleverd als onderdeel van het Apache Storm-project waarmee u eenvoudig communiceren met Storm. U vindt deze [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)module op .

Storm is een Java-proces dat draait op de Java Virtual Machine (JVM). Componenten die in andere talen zijn geschreven, worden uitgevoerd als subprocessen. De Storm communiceert met deze subprocessen met behulp van JSON-berichten die via stdin/stdout worden verzonden. Meer details over de communicatie tussen componenten zijn te vinden in de [multi-lang Protocol](https://storm.apache.org/releases/current/Multilang-protocol.html) documentatie.

## <a name="python-with-the-flux-framework"></a>Python met het Flux-framework

Met het Flux-framework u Storm-topologieën afzonderlijk van de componenten definiëren. Het Flux-framework gebruikt YAML om de Stormtopologie te definiëren. De volgende tekst is een voorbeeld van het verwijzen naar een Python-component in het YAML-document:

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

De `FluxShellSpout` klasse wordt gebruikt `sentencespout.py` om het script te starten dat de uitloop implementeert.

Flux verwacht dat de Python-scripts zich in de `/resources` map bevinden in het potbestand dat de topologie bevat. Dit voorbeeld slaat de Python-scripts op in de `/multilang/resources` map. De `pom.xml` bevat dit bestand met behulp van de volgende XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Zoals eerder vermeld, is `storm.py` er een bestand dat de Thrift definitie voor Storm implementeert. Het Flux-framework omvat `storm.py` automatisch wanneer het project wordt gebouwd, zodat u zich geen zorgen hoeft te maken over het opnemen ervan.

## <a name="build-the-project"></a>Het project bouwen

1. Download het [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)project van .

1. Open een opdrachtprompt en navigeer `hdinsight-python-storm-wordcount-master`naar de projecthoofd: . Voer de volgende opdracht in:

    ```cmd
    mvn clean compile package
    ```

    Met deze `target/WordCount-1.0-SNAPSHOT.jar` opdracht wordt een bestand gemaakt dat de gecompileerde topologie bevat.

## <a name="run-the-storm-topology-on-hdinsight"></a>Voer de Storm topologie uit op HDInsight

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om het `WordCount-1.0-SNAPSHOT.jar` bestand naar het cluster Storm op HDInsight te kopiëren. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Zodra het bestand is geüpload, maakt u verbinding met het cluster via SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik vanuit de SSH-sessie de volgende opdracht om de topologie op het cluster te starten:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Eenmaal begonnen, een Storm topologie loopt tot gestopt.

1. Gebruik de gebruikersinterface storm om de topologie in het cluster weer te geven. De Storm UI `https://CLUSTERNAME.azurehdinsight.net/stormui`bevindt zich op . Vervang `CLUSTERNAME` door uw clusternaam.

1. Stop de Storm topologie. Gebruik de volgende opdracht om de topologie in het cluster te stoppen:

    ```bash
    storm kill wordcount
    ```

    U ook de gebruikersinterface van Storm gebruiken. Selecteer **onder Topologie-acties** voor de topologie de optie **Doden**.

## <a name="run-the-topology-locally"></a>De topologie lokaal uitvoeren

Als u de topologie lokaal wilt uitvoeren, gebruikt u de volgende opdracht:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Dit commando vereist een lokale Storm ontwikkelingsomgeving. Zie [Een ontwikkelomgeving instellen](https://storm.apache.org/releases/current/Setting-up-development-environment.html)voor meer informatie.

Zodra de topologie wordt gestart, wordt informatie naar de lokale console gestuurd die vergelijkbaar is met de volgende tekst:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Als u de topologie wilt stoppen, gebruikt u __Ctrl + C__.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende documenten voor andere manieren om Python te gebruiken met HDInsight: [Hoe gebruik je Python User Defined Functions (UDF) in Apache Pig en Apache Hive.](../hadoop/python-udf-hdinsight.md)
