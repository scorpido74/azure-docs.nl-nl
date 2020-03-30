---
title: Apache Storm voorbeeld Java topologie - Azure HDInsight
description: Meer informatie over het maken van Apache Storm-topologieën op Java door een voorbeeld van woordtellingstopologie te maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083299"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Maak een Apache Storm topologie in Java

Meer informatie over het maken van een op Java gebaseerde topologie voor [Apache Storm.](https://storm.apache.org/) Hier maakt u een Stormtopologie die een toepassing voor het aantal woorden implementeert. Je gebruikt [Apache Maven](https://maven.apache.org/) om het project te bouwen en te verpakken. Vervolgens leert u hoe u de topologie definieert met behulp van het [Apache Storm Flux-framework.](https://storm.apache.org/releases/2.0.0/flux.html)

Nadat u de stappen in dit document hebt voltooid, u de topologie implementeren in Apache Storm op HDInsight.

> [!NOTE]  
> Een voltooide versie van de voorbeelden van stormtopologie [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)die in dit document zijn gemaakt, is beschikbaar op .

## <a name="prerequisites"></a>Vereisten

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.

## <a name="test-environment"></a>Testomgeving

De omgeving die voor dit artikel wordt gebruikt, was een computer met Windows 10.  De opdrachten werden uitgevoerd in een opdrachtprompt en de verschillende bestanden zijn bewerkt met Kladblok.

Voer in een opdrachtprompt de onderstaande opdrachten in om een werkomgeving te maken:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Een Maven-project maken

Voer de volgende opdracht in om een Maven-project met de naam WordCount te **maken:**

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Met deze opdracht `WordCount` wordt een map gemaakt met de naam op de huidige locatie, die een basisproject van Maven bevat. De tweede opdracht wijzigt `WordCount`de huidige werkmap in . De derde opdracht maakt `resources`een nieuwe map, die later zal worden gebruikt.  De `WordCount` map bevat de volgende items:

* `pom.xml`: Bevat instellingen voor het Maven-project.
* `src\main\java\com\microsoft\example`: Bevat uw toepassingscode.
* `src\test\java\com\microsoft\example`: Bevat tests voor uw toepassing.  

### <a name="remove-the-generated-example-code"></a>De gegenereerde voorbeeldcode verwijderen

Verwijder de gegenereerde `AppTest.java`test- `App.java` en toepassingsbestanden en voer de onderstaande opdrachten in:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven-opslagplaatsen toevoegen

HDInsight is gebaseerd op het Hortonworks Data Platform (HDP), dus we raden u aan de Hortonworks repository te gebruiken om afhankelijkheden te downloaden voor uw Apache Storm-projecten.  

Open `pom.xml` door onderstaande opdracht in te voeren:

```cmd
notepad pom.xml
```

Voeg vervolgens de volgende `<url>https://maven.apache.org</url>` XML toe na de regel:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Eigenschappen toevoegen

Met Maven u waarden op projectniveau definiëren die eigenschappen worden genoemd. Voeg `pom.xml`daarin de volgende `</repositories>` tekst toe na de regel:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

U deze waarde nu gebruiken `pom.xml`in andere delen van de. Wanneer u bijvoorbeeld de versie van Storm-componenten `${storm.version}` opgeeft, u een waarde gebruiken in plaats van harde codering.

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Voeg een afhankelijkheid toe voor Storm-componenten. Voeg `pom.xml`in de volgende `<dependencies>` tekst in de sectie toe:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Bij het compileren gebruikt Maven `storm-core` deze informatie om op te zoeken in de Maven repository. Het ziet er eerst in de repository op uw lokale computer. Als de bestanden er niet zijn, downloadt Maven ze uit de openbare Maven-opslagplaats en slaat ze op in de lokale opslagplaats.

> [!NOTE]  
> Let `<scope>provided</scope>` op de regel in deze sectie. Deze instelling vertelt Maven om storm-core uit te sluiten van alle **JAR-bestanden** die worden gemaakt, omdat het wordt geleverd door het systeem.

## <a name="build-configuration"></a>Configuratie bouwen

Met Maven-plug-ins u de bouwfasen van het project aanpassen. Bijvoorbeeld hoe het project wordt samengesteld of hoe het te verpakken in een JAR-bestand. Voeg `pom.xml`in , voeg de `</project>` volgende tekst direct boven de regel.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Deze sectie wordt gebruikt om plug-ins, resources en andere buildconfiguratieopties toe te voegen. Zie [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)voor een `pom.xml` volledige verwijzing van het bestand .

### <a name="add-plug-ins"></a>Plug-ins toevoegen

* **Exec Maven Plugin**

    Voor Apache Storm topologieën geïmplementeerd in Java, de [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) is handig omdat het u toelaat om gemakkelijk de topologie lokaal draaien in uw ontwikkelomgeving. Voeg het volgende `<plugins>` toe `pom.xml` aan het gedeelte van het bestand om de Exec Maven-plug-in op te nemen:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler-plug-in**

    Een andere handige plug-in is de [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/), die wordt gebruikt om compilatie opties te veranderen. Wijzig de Java-versie die Maven gebruikt voor de bron en het doel voor uw toepassing.

  * Voor HDInsight __3.4 of eerder__stelt u de bron- en doelversie van Java in op __1,7__.

  * Voor HDInsight __3.5__stelt u de bron- en doelversie van Java in op __1,8__.

  Voeg de volgende `<plugins>` tekst toe `pom.xml` in het gedeelte van het bestand om de Plug-in Apache Maven Compiler op te nemen. In dit voorbeeld wordt 1,8 opgegeven, zodat de doelHDInsight-versie 3,5 is.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Resources configureren

In de sectie Resources u niet-codebronnen opnemen, zoals configuratiebestanden die nodig zijn voor componenten in de topologie. Voeg in dit voorbeeld de `<resources>` volgende tekst `pom.xml` toe in de sectie van het bestand. Sla het bestand op en sluit het.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

In dit voorbeeld wordt de bronnenmap`${basedir}`in de hoofdmap van het project `log4j2.xml`( ) toegevoegd als een locatie die bronnen bevat en bevat het bestand met de naam . Dit bestand wordt gebruikt om te configureren welke informatie wordt geregistreerd door de topologie.

## <a name="create-the-topology"></a>De topologie maken

Een Op Java gebaseerde Apache Storm-topologie bestaat uit drie componenten die u als afhankelijkheid moet schrijven (of verwijzen).

* **Spouts**: Leest gegevens uit externe bronnen en zendt gegevensstromen uit naar de topologie.

* **Bouten**: Voert verwerking op stromen uitgezonden door tuiten of andere bouten, en zendt een of meer stromen.

* **Topologie**: Definieert hoe de tuiten en bouten zijn gerangschikt, en biedt het ingangspunt voor de topologie.

### <a name="create-the-spout"></a>De uitloop maken

Om de vereisten voor het instellen van externe gegevensbronnen te verminderen, zendt de volgende uitloop gewoon willekeurige zinnen uit. Het is een aangepaste versie van een tuit die is voorzien van de [Storm-Starter voorbeelden.](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)  Hoewel deze topologie slechts één tuit gebruikt, kunnen anderen verschillende gegevens uit verschillende bronnen in de topologie bevatten.

Voer de onderstaande opdracht in `RandomSentenceSpout.java`om een nieuw bestand te maken en te openen:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand.  Sluit vervolgens het bestand.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Zie een van de volgende voorbeelden voor een voorbeeld van een uitloop die uit een externe gegevensbron wordt gelezen:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Een voorbeeld tuit dat leest van Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Een tuit die leest van Kafka.

### <a name="create-the-bolts"></a>Maak de bouten

Bouten verwerken de gegevensverwerking. Bouten kunnen alles doen, bijvoorbeeld, berekening, persistentie, of praten met externe componenten. Deze topologie maakt gebruik van twee bouten:

* **SplitSentence**: Splitst de zinnen die door **RandomSentenceSpout** worden uitgezonden in afzonderlijke woorden.

* **WordCount:** Telt hoe vaak elk woord heeft plaatsgevonden.

#### <a name="splitsentence"></a>Splitzin

Voer de onderstaande opdracht in `SplitSentence.java`om een nieuw bestand te maken en te openen:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand.  Sluit vervolgens het bestand.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordAantal

Voer de onderstaande opdracht in `WordCount.java`om een nieuw bestand te maken en te openen:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand.  Sluit vervolgens het bestand.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>De topologie definiëren

De topologie bindt de tuiten en bouten samen in een grafiek, die bepaalt hoe gegevens tussen de componenten stromen. Het biedt ook parallelle hints die Storm gebruikt bij het maken van exemplaren van de componenten binnen het cluster.

De volgende afbeelding is een basisdiagram van de grafiek van componenten voor deze topologie.

![diagram met de tuiten en bouten regeling](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Als u de topologie wilt implementeren, voert u `WordCountTopology.java`de onderstaande opdracht in om een nieuw bestand te maken en te openen:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand.  Sluit vervolgens het bestand.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Logboekregistratie configureren

Storm gebruikt [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) om informatie in te loggen. Als u logboekregistratie niet configureert, zendt de topologie diagnostische informatie uit. Als u wilt bepalen wat `log4j2.xml` er `resources` is geregistreerd, maakt u een bestand met de naam in de map door de onderstaande opdracht in te voeren:

```cmd
notepad resources\log4j2.xml
```

Kopieer en plak vervolgens de XML-tekst hieronder in het nieuwe bestand.  Sluit vervolgens het bestand.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Met deze XML configureert `com.microsoft.example` u een nieuwe logger voor de klasse, die de componenten in dit voorbeeldtopologie bevat. Het niveau is ingesteld om te traceren voor deze logger, die alle logging informatie uitgezonden door componenten in deze topologie vangt.

De `<Root level="error">` sectie configureert het hoofdniveau `com.microsoft.example`van logboekregistratie (alles niet in) om alleen foutgegevens te registreren.

Zie [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)voor meer informatie over het configureren van logboekregistratie voor Log4j 2 .

> [!NOTE]  
> Storm versie 0.10.0 en hoger gebruik Log4j 2.x. Oudere versies van storm gebruikt Log4j 1.x, die een ander formaat gebruikt voor log configuratie. Zie [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)voor informatie over de oudere configuratie .

## <a name="test-the-topology-locally"></a>De topologie lokaal testen

Nadat u de bestanden hebt opgeslagen, gebruikt u de volgende opdracht om de topologie lokaal te testen.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Tijdens het uitvoeren geeft de topologie opstartgegevens weer. De volgende tekst is een voorbeeld van de uitvoer van het aantal woorden:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Dit voorbeeldlogboek geeft aan dat het woord 'en' 113 keer is uitgestoten. De telling blijft stijgen zolang de topologie wordt uitgevoerd omdat de tuit continu dezelfde zinnen uitzendt.

Er is een interval van 5 seconden tussen de emissie van woorden en tellingen. De component **WordCount** is geconfigureerd om alleen informatie uit te zenden wanneer een tick tuple binnenkomt. Het verzoekt dat de tuples van de teek slechts om de vijf seconden worden geleverd.

## <a name="convert-the-topology-to-flux"></a>De topologie converteren naar Flux

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) is een nieuw framework beschikbaar met Storm 0.10.0 en hoger, waarmee u de configuratie scheiden van de implementatie. Uw componenten worden nog steeds gedefinieerd in Java, maar de topologie wordt gedefinieerd met behulp van een YAML-bestand. U een standaardtopologiedefinitie verpakken bij uw project of een zelfstandig bestand gebruiken bij het indienen van de topologie. Wanneer u de topologie indient bij Storm, u omgevingsvariabelen of configuratiebestanden gebruiken om waarden in de YAML-topologiedefinitie in te vullen.

Het YAML-bestand definieert de componenten die moeten worden gebruikt voor de topologie en de gegevensstroom daartussen. U een YAML-bestand opnemen als onderdeel van het jar-bestand of u een extern YAML-bestand gebruiken.

Zie [Flux framework (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Als gevolg van een [bug (methttps://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) Storm 1.0.1, moet u mogelijk een [Storm-ontwikkelomgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html) installeren om Flux-topologieën lokaal uit te voeren.

1. Voorheen `WordCountTopology.java` definieerde de topologie, maar is niet nodig met Flux. Verwijder het bestand met de volgende opdracht:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Voer de onderstaande opdracht in `topology.yaml`om een nieuw bestand te maken en te openen:

    ```cmd
    notepad resources\topology.yaml
    ```

    Kopieer en plak de tekst hieronder vervolgens in het nieuwe bestand.  Sluit vervolgens het bestand.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Voer de opdracht `pom.xml` hieronder in om te openen om de beschreven revisies hieronder uit te voeren:

    ```cmd
    notepad pom.xml
    ```

   1. Voeg de volgende nieuwe `<dependencies>` afhankelijkheid toe aan de sectie:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Voeg de volgende `<plugins>` plug-in toe aan de sectie. Deze plug-in verwerkt de creatie van een pakket (jar-bestand) voor het project en past een aantal transformaties toe die specifiek zijn voor Flux bij het maken van het pakket.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Voor de sectie Exec Maven `<configuration>`  >  `<mainClass>` Plugin `${storm.topology}` `org.apache.storm.flux.Flux`navigeert u naar en wijzigt u . Met deze instelling kan Flux de toonologie lokaal in ontwikkeling uitvoeren.

   1. Voeg `<resources>` in de sectie `<includes>`het volgende toe aan . Deze XML bevat het YAML-bestand dat de topologie definieert als onderdeel van het project.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Test de fluxtopologie lokaal

1. Voer de volgende opdracht in om de Flux-topologie samen te stellen en uit te voeren met Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Als uw topologie Storm 1.0.1-bits gebruikt, mislukt deze opdracht. Deze fout wordt [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)veroorzaakt door . Installeer Storm in plaats daarvan [in uw ontwikkelomgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html) en gebruik de volgende stappen:
    >
    > Als u [Storm in uw ontwikkelomgeving](https://storm.apache.org/releases/current/Setting-up-development-environment.html)hebt geïnstalleerd, u in plaats daarvan de volgende opdrachten gebruiken:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    De `--local` parameter voert de topologie uit in de lokale modus op uw ontwikkelomgeving. De `-R /topology.yaml` parameter `topology.yaml` gebruikt de bestandsbron uit het jar-bestand om de topologie te definiëren.

    Tijdens het uitvoeren geeft de topologie opstartgegevens weer. De volgende tekst is een voorbeeld van de uitvoer:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Er is een vertraging van 10 seconden tussen batches van geregistreerde informatie.

2. Maak een nieuwe topologie yaml uit het project.

    1. Voer de opdracht `topology.xml`hieronder in om te openen:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Zoek de volgende sectie en `10` `5`wijzig de waarde van . Deze wijziging wijzigt het interval tussen het uitzenden van batches van woordtellingen van 10 seconden naar 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Bestand opslaan `newtopology.yaml`als .

3. Voer de volgende opdracht in om de topologie uit te voeren:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Of, als je Storm op je ontwikkelomgeving:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Deze opdracht `newtopology.yaml` gebruikt de als de topologie definitie. Omdat we de `compile` parameter niet hebben opgenomen, gebruikt Maven de versie van het project dat in eerdere stappen is gebouwd.

    Zodra de topologie begint, moet u merken dat de tijd tussen `newtopology.yaml`uitgestoten batches is gewijzigd om de waarde in . U dus zien dat u uw configuratie wijzigen via een YAML-bestand zonder dat u de topologie opnieuw hoeft te compileren.

Zie [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) is een abstractie op hoog niveau die wordt geleverd door Storm. Het ondersteunt stateful verwerking. Het belangrijkste voordeel van Trident is dat het kan garanderen dat elk bericht dat de topologie binnenkomt slechts één keer wordt verwerkt. Zonder Trident te gebruiken, kan uw topologie alleen garanderen dat berichten ten minste één keer worden verwerkt. Er zijn ook andere verschillen, zoals ingebouwde componenten die kunnen worden gebruikt in plaats van het maken van bouten. In feite worden bouten vervangen door minder generieke componenten, zoals filters, projecties en functies.

Trident toepassingen kunnen worden gemaakt met behulp van Maven projecten. U gebruikt dezelfde basisstappen als eerder in dit artikel wordt gepresenteerd, alleen de code is anders. Trident kan ook (op dit moment) niet worden gebruikt met het Flux framework.

Zie het [Trident API-overzicht](https://storm.apache.org/releases/current/Trident-API-Overview.html)voor meer informatie over Trident.

## <a name="next-steps"></a>Volgende stappen

Je hebt geleerd hoe je een Apache Storm topologie maakt met Java. Leer nu hoe je:

* [Apache Storm-topologieën implementeren en beheren op HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Ontwikkel C# topologieën voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

U meer voorbeeld Apache Storm topologieën vinden door te gaan [naar Voorbeeld topologieën voor Apache Storm op HDInsight](apache-storm-example-topology.md).
