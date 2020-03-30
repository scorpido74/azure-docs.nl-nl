---
title: Een interactieve Spark Shell gebruiken in Azure HDInsight
description: Een interactieve Spark Shell biedt een read-execute-printproces voor het uitvoeren van Spark-opdrachten één voor één en het bekijken van de resultaten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162800"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Apache Spark uitvoeren vanuit de Spark Shell

Een interactieve [Apache Spark](https://spark.apache.org/) Shell biedt een REPL -omgeving (read-execute-print loop) voor het één voor één uitvoeren van Spark-opdrachten en het bekijken van de resultaten. Dit proces is handig voor ontwikkeling en foutopsporing. Spark biedt één shell voor elk van zijn ondersteunde talen: Scala, Python en R.

## <a name="run-an-apache-spark-shell"></a>Een Apache Spark Shell uitvoeren

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark levert schelpen voor Scala (spark-shell) en Python (pyspark). Voer in uw SSH-sessie *een* van de volgende opdrachten in:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Als u een optionele configuratie wilt gebruiken, moet u ervoor zorgen dat u de [uitzondering OutOfMemoryError voor Apache Spark](./apache-spark-troubleshoot-outofmemory.md)eerst bekijkt.

1. Een paar basisvoorbeeldopdrachten. Kies de relevante taal:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Een CSV-bestand opvragen. Let op de `spark-shell` taal `pyspark`hieronder werkt voor en .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Query een CSV-bestand en sla resultaten in variabele:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Weergaveresultaten:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Afsluiten

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession- en SparkContext-exemplaren

Wanneer u de Spark Shell uitvoert, worden standaard exemplaren van SparkSession en SparkContext automatisch voor u geinstantieerd.

Voer in als u `spark`toegang wilt krijgen tot de instantie SparkSession. Voer `sc`in als u toegang wilt krijgen tot de instantie SparkContext.

## <a name="important-shell-parameters"></a>Belangrijke shellparameters

De opdracht Spark`spark-shell`Shell `pyspark`( of ) ondersteunt veel opdrachtregelparameters. Als u een volledige lijst met parameters wilt `--help`bekijken, start u de Spark Shell met de schakelaar . Sommige van deze parameters `spark-submit`zijn alleen van toepassing op , die de Spark Shell wraps.

| schakelen | description | Voorbeeld |
| --- | --- | --- |
| --master MASTER_URL | Hiermee geeft u de stramien-URL op. In HDInsight is deze `yarn`waarde altijd . | `--master yarn`|
| --potten JAR_LIST | Door komma's gescheiden lijst met lokale potten die op de bestuurders- en executeurklassenpaden worden opgenomen. In HDInsight bestaat deze lijst uit paden naar het standaardbestandssysteem in Azure Storage of Data Lake Storage. | `--jars /path/to/examples.jar` |
| --pakketten MAVEN_COORDS | Door komma's gescheiden lijst van mavencoördinaten van potten die op de bestuurders- en executeurklassepaden moeten worden opgenomen. Zoekt in de lokale maven repo, dan maven centraal, `--repositories`dan eventuele extra externe repositories opgegeven met . De indeling voor de coördinaten is *groupId:**artifactId*:*versie*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-bestanden LIJST | Alleen voor Python wordt een door komma's gescheiden lijst met .zip-, .egg- of .py-bestanden op het PYTHONPATH plaatsen. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Volgende stappen

- Zie [Inleiding tot Apache Spark op Azure HDInsight](apache-spark-overview.md) voor een overzicht.
- Zie [Een Apache Spark-cluster maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) om te werken met Spark-clusters en SparkSQL.
- Zie [Wat is Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) om toepassingen te schrijven die streaminggegevens verwerken met Spark.
