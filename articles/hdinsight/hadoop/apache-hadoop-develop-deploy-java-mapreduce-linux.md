---
title: Java MapReduce maken voor Apache Hadoop - Azure HDInsight
description: Meer informatie over het gebruik van Apache Maven om een op Java gebaseerde MapReduce-toepassing te maken en deze vervolgens uit te voeren met Hadoop op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311951"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Java MapReduce-programma's ontwikkelen voor Apache Hadoop in HDInsight

Meer informatie over het gebruik van Apache Maven om een op Java gebaseerde MapReduce-toepassing te maken en deze vervolgens uit te voeren met Apache Hadoop op Azure HDInsight.

## <a name="prerequisites"></a>Vereisten

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.

## <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

De omgeving die voor dit artikel wordt gebruikt, was een computer met Windows 10. De opdrachten werden uitgevoerd in een opdrachtprompt en de verschillende bestanden zijn bewerkt met Kladblok. Dienovereenkomstig aanpassen voor uw omgeving.

Voer in een opdrachtprompt de onderstaande opdrachten in om een werkomgeving te maken:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Een Maven-project maken

1. Voer de volgende opdracht in om een Maven-project met de naam **wordcountjava**te maken:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Met deze opdracht wordt een map `artifactID` gemaakt met de naam die is opgegeven door de parameter **(wordcountjava** in dit voorbeeld.) Deze map bevat de volgende items:

    * `pom.xml`- Het [Project Object Model (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) dat informatie en configuratiedetails bevat die worden gebruikt om het project te bouwen.
    * src\main\java\org\apache\hadoop\examples: Bevat uw toepassingscode.
    * src\test\java\org\apache\hadoop\examples: Bevat tests voor uw toepassing.

1. Verwijder de gegenereerde voorbeeldcode. Verwijder de gegenereerde `AppTest.java`test- `App.java` en toepassingsbestanden en voer de onderstaande opdrachten in:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Het projectobjectmodel bijwerken

Zie https://maven.apache.org/pom.htmlvoor een volledige verwijzing naar het pom.xml-bestand . Open `pom.xml` door onderstaande opdracht in te voeren:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Voeg `pom.xml`in de volgende `<dependencies>` tekst in de sectie toe:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Dit definieert vereiste bibliotheken &lt;(vermeld\>in artefactId) met &lt;een\>specifieke versie (vermeld in versie). Op het moment van compileren worden deze afhankelijkheden gedownload van de standaard Maven-repository. U de [maven repository zoeken](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) gebruiken om meer te bekijken.

De `<scope>provided</scope>` vertelt Maven dat deze afhankelijkheden niet moeten worden verpakt met de toepassing, omdat ze worden geleverd door de HDInsight cluster op run-time.

> [!IMPORTANT]
> De gebruikte versie moet overeenkomen met de versie van Hadoop aanwezig op uw cluster. Zie het document voor [versieversie van HDInsight](../hdinsight-component-versioning.md) voor meer informatie over versies.

### <a name="build-configuration"></a>Configuratie bouwen

Met Maven-plug-ins u de bouwfasen van het project aanpassen. Deze sectie wordt gebruikt om plug-ins, resources en andere buildconfiguratieopties toe te voegen.

Voeg de volgende `pom.xml` code toe aan het bestand en sla het bestand op en sluit deze. Deze tekst moet `<project>...</project>` zich in de tags in `</dependencies>` `</project>`het bestand bevinden, bijvoorbeeld tussen en .

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

In deze sectie worden de Apache Maven Compiler Plugin en Apache Maven Shade Plugin geconfigureerd. De compilerplug-in wordt gebruikt om de topologie samen te stellen. De schaduwplug-in wordt gebruikt om licentieduplicatie te voorkomen in het JAR-pakket dat door Maven is gebouwd. Deze plug-in wordt gebruikt om een fout met "dubbele licentiebestanden" te voorkomen tijdens het uitvoeren van de looptijd op het HDInsight-cluster. Het gebruik van maven-shade-plugin met de `ApacheLicenseResourceTransformer` implementatie voorkomt de fout.

De maven-shade-plugin produceert ook een uber-pot die alle afhankelijkheden bevat die nodig zijn voor de toepassing.

Sla het bestand `pom.xml` op.

## <a name="create-the-mapreduce-application"></a>De toepassing MapReduce maken

1. Voer de onderstaande opdracht in `WordCount.java`om een nieuw bestand te maken en te openen. Selecteer **Ja** bij de prompt om een nieuw bestand te maken.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand. Sluit vervolgens het bestand.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    Let op de `org.apache.hadoop.examples` naam van `WordCount`het pakket is en de klassenaam is . U gebruikt deze namen wanneer u de taak MapReduce indient.

## <a name="build-and-package-the-application"></a>De toepassing bouwen en verpakken

Gebruik `wordcountjava` in de map de volgende opdracht om een JAR-bestand te maken dat de toepassing bevat:

```cmd
mvn clean package
```

Met deze opdracht worden eerdere buildartefacten gereinigd, alle afhankelijkheden gedownload die nog niet zijn geïnstalleerd en vervolgens de toepassing gebouwd en verpakt.

Zodra de opdracht is `wordcountjava/target` voltooid, bevat `wordcountjava-1.0-SNAPSHOT.jar`de map een bestand met de naam .

> [!NOTE]
> Het `wordcountjava-1.0-SNAPSHOT.jar` bestand is een uberjar, die niet alleen de Functie WordCount bevat, maar ook afhankelijkheden die de taak vereist tijdens runtime.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>De JAR uploaden en taken uitvoeren (SSH)

De volgende `scp` stappen worden gebruikt om de JAR te kopiëren naar het primaire hoofdknooppunt van uw Apache HBase op het HDInsight-cluster. De `ssh` opdracht wordt vervolgens gebruikt om verbinding te maken met het cluster en het voorbeeld rechtstreeks op het hoofdknooppunt uit te voeren.

1. Upload de pot naar het cluster. Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Maak verbinding met het cluster. Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik vanuit de SSH-sessie de volgende opdracht om de toepassing MapReduce uit te voeren:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Met deze opdracht wordt de toepassing WordCount MapReduce gestart. Het invoerbestand `/example/data/gutenberg/davinci.txt`is en de `/example/data/wordcountout`uitvoermap is . Zowel het invoerbestand als de uitvoer worden opgeslagen in de standaardopslag voor het cluster.

1. Zodra de taak is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    U ontvangt een lijst met woorden en tellingen, met waarden die lijken op de volgende tekst:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u een Java MapReduce-taak ontwikkelen. Bekijk de volgende documenten voor andere manieren om met HDInsight te werken.

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)
