---
title: MapReduce met Apache Hadoop op HDInsight
description: Meer informatie over het uitvoeren van Apache MapReduce-taken op Apache Hadoop in HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435712"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>MapReduce gebruiken in Apache Hadoop in HDInsight

Meer informatie over het uitvoeren van MapReduce-taken op HDInsight-clusters.

## <a name="example-data"></a>Voorbeeldgegevens

HDInsight biedt verschillende voorbeeldgegevenssets, die `/example/data` `/HdiSamples` zijn opgeslagen in de en directory. Deze mappen bevinden zich in de standaardopslag voor uw cluster. In dit document gebruiken `/example/data/gutenberg/davinci.txt` we het bestand. Dit bestand bevat de notitieboekjes van Leonardo da Vinci.

## <a name="example-mapreduce"></a>Voorbeeld mapReduce

Een voorbeeld MapReduce word count-toepassing is opgenomen in uw HDInsight-cluster. Dit voorbeeld bevindt zich op `/example/jars/hadoop-mapreduce-examples.jar` de standaardopslag voor uw cluster.

De volgende Java-code is de bron van `hadoop-mapreduce-examples.jar` de mapreduce-toepassing in het bestand:

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

Zie [Java MapReduce-toepassingen voor HDInsight ontwikkelen voor](apache-hadoop-develop-deploy-java-mapreduce-linux.md)instructies om uw eigen MapReduce-toepassingen te schrijven.

## <a name="run-the-mapreduce"></a>De map reduceeren uitvoeren

HDInsight kan HiveQL-taken uitvoeren met behulp van verschillende methoden. Gebruik de volgende tabel om te beslissen welke methode geschikt is voor u en volg vervolgens de koppeling voor een walkthrough.

| **Gebruik deze**... | **... om dit te doen** |  ... van dit **besturingssysteem** van de client |
|:--- |:--- |:--- |:--- |
| [Ssh](apache-hadoop-use-mapreduce-ssh.md) |Gebruik de opdracht Hadoop via **SSH** |Linux, Unix, Mac OS X of Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Stuur de taak op afstand in met **REST** |Linux, Unix, Mac OS X of Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |De taak op afstand verzenden met **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie over het werken met gegevens in HDInsight:

* [Java MapOntwikkelenVerlaag programma's voor HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Apache Hive gebruiken met HDInsight](./hdinsight-use-hive.md)
